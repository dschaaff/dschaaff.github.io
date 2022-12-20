---
author: dschaaff
categories:
- techology
comments: true
date: "2016-12-23T23:19:07Z"
link: http://danielschaaff.com/2016/12/23/finishing-the-puppet-4-migration/
slug: finishing-the-puppet-4-migration
tags:
- puppet
- technology
title: Finishing the Puppet 4 Migration
wordpress_id: 639
---

Two days ago I finished our migration to Puppet 4. Overall I'd say the process was pretty painless. The gist of what I did





  * start running rspec tests against Puppet 4


  * fix issues found in tests


  * run the [catalog preview tool](https://github.com/dschaaff/prosvc-preview_report) and fix any issues found


  * turn on the future parser on the existing master


  * turn off stringify facts


  * create new master and PuppeDB server


  * migrate agents to new master



Thankfully our code wasn't too difficult to update and most of the forge modules we use had also been updated.



## Creating the New Master



I did not want to upgrade our existing master for a variety of reasons I won't get into here. Instead I took the opportunity to migrate it from an old vm to running on ec2 with PuppetDB in RDS. I have to give props to the puppet team for greatly simplifying the setup process of a new master in Puppet 4. Setting up puppetserver is significantly easier then the old passenger based server.



## Migrating the Agents



Puppet provides a [module](https://forge.puppet.com/puppetlabs/puppet_agent) to migrate your agents to the new master. It will copy the existing ssl certs to the new directory and upgrade the agent. I was not able to do this since I was not migrating certs to the new master (I needed to add new DNS alt names). The consequence of this was needing to find a way to upgrade and migrate the agents in an automated fashion. I accomplished this entirely with puppet! The process was





  * pre-create `/etc/puppetlabs/puppet`


  * drop a new config into `/etc/puppetlabs.puppet/puppet.conf` with the new master name


  * setup the puppetlabs puppet collection repo


  * install the new puppet-agent package


  * update cron job for new puppet paths (the fact that I already ran puppet using cron made this simple)


  * purge the old `/etc/puppet` directory



A puppet run would take place on the old master and prep things using the steps above. Then when the cronjob kicked in it would run against the new master and get a new cert issued. Overall this worked really well and we only had to touch 2 machines by hand.



### Migrate Puppet Manifest



This is the manifest I used to migrate our linux machines. Available on GitHub here [https://github.com/dschaaff/puppet-migrate](https://github.com/dschaaff/puppet-migrate).

```ruby
class migrate {

file {'/etc/puppetlabs':
ensure => directory,
}
->
file {'/etc/puppetlabs/puppet':
ensure => directory,
}
->
file {'/etc/puppetlabs/puppet/puppet.conf':
ensure => present,
source => 'puppet:///modules/migrate/puppet.conf'
}

if $facts['osfamily'] == 'Debian' {
include apt
apt::source { 'puppetlabs-pc1':
location => 'http://apt.puppetlabs.com',
repos    => 'PC1',
key      => {
'id'     => '6F6B15509CF8E59E6E469F327F438280EF8D349F',
'server' => 'pgp.mit.edu',
},
notify => Class['apt::update']
}
package {'puppet-agent':
ensure => present,
require => Class['apt::update']
}
}

if $facts['osfamily'] == 'RedHat' {
$version = $facts['operatingsystemmajrelease']
yumrepo {'puppetlabs-pc1':
baseurl  => "https://yum.puppetlabs.com/el/${version}/PC1/\$basearch",
descr    => 'Puppetlabs PC1 Repository',
enabled  => true,
gpgcheck => '1',
gpgkey   => 'https://yum.puppetlabs.com/RPM-GPG-KEY-puppetlabs'
}
->
package {'puppet-agent':
ensure => present,
}
}

$time1 = fqdn_rand(30)
$time2 = $time1 + 30
$minute = [ $time1, $time2 ]

cron {'puppet-agent':
command => '/opt/puppetlabs/bin/puppet agent --no-daemonize --onetime --logdest syslog > /dev/null 2>&1',
user    => 'root',
hour    => '*',
minute  => $minute,
}
->
cron {'puppet-client':
ensure  => 'absent',
command => '/usr/bin/puppet agent --no-daemonize --onetime --logdest syslog > /dev/null 2>&1',
user    => 'root',
hour    => '*',
minute  => $minute,
}
file {'/etc/puppet':
ensure => purged,
force => true,
}
->
file { '/var/lib/puppet/ssl':
ensure => purged,
force => true,
}
}
```

I used a similar manifest for macOS

```ruby
class migrate::mac {
$mac_vers = $facts['macosx_productversion_major']

file {'/etc/puppetlabs':
ensure => directory,
}
->
file {'/etc/puppetlabs/puppet':
ensure => directory,
}
->
file {'/etc/puppetlabs/puppet/puppet.conf':
ensure => present,
source => 'puppet:///modules/migrate/puppet.conf'
}

package {"puppet-agent-1.8.2-1.osx${mac_vers}.dmg":
ensure => present,
source => "https://downloads.puppetlabs.com/mac/${mac_vers}/PC1/x86_64/puppet-agent-1.8.2-1.osx${mac_vers}.dmg"
}

$time1 = fqdn_rand(30)
$time2 = $time1 + 30
$minute = [ $time1, $time2 ]

cron {'puppet-agent':
command => '/opt/puppetlabs/bin/puppet agent --no-daemonize --onetime --logdest syslog > /dev/null 2>&1',
user    => 'root',
hour    => '*',
minute  => $minute,
}

file {'/etc/puppet':
ensure => purged,
force => true,
}
->
file { '/var/lib/puppet/ssl':
ensure => purged,
force => true,
}
->
# using gem since puppet 3.8 did not have packages for Sierra
package {'puppet':
ensure => absent,
provider => 'gem',
}
}
```



### Post Migration Experience



After migrating the agents I only ran into one piece of code that broke due to the upgrade. Somehow I had overlooked the [removal of dynamic scoping in ERB templates](https://docs.puppet.com/puppet/latest/lang_updating_manifests.html#dynamic-scoping-in-erb). This piece of code was not covered by rspec tests, an area for improvement! I relied on this to configure logstash output to elasticsearch. Under Puppet 3 the relevant piece of ERB looked like this

```ruby
output {
if [type] == "syslog" {
elasticsearch {
hosts => [<%= @es_input_nodes.collect { |node| '"' + node.to_s + ':' + @elasticsearch_port.to_s + '"' }.join(',') %>]
ssl => true
}
}
```

The value of `es_input_nodes` was pulled from the params class

```ruby
class elk::logstash (
$syslog_port = $elk::params::syslog_port,
$elasticsearch_nodes = $elk::params::elasticsearch_nodes,
$es_input_nodes = $elk::params::es_input_nodes,
$elasticsearch_port = $elk::params::elasticsearch_port,
$netflow_port = $elk::params::netflow_port
)
```

The params class pulls the info from Puppet DB.

```ruby
$es_input_nodes = sort(query_nodes('Class[Elk::elasticsearch] and elasticsearchrole=data or elasticsearchrole=client'))
```

The removal of dynamic scoping templates meant the template was putting empty values in the logstash config and breaking the service. To fix the variables needed to be scoped properly in the template and now look like this

```ruby
output {
if [type] == "syslog" {
elasticsearch {
hosts => [<%= scope['elk::logstash::es_input_nodes'].collect { |node| '"' + node.to_s + ':' + scope['elk::logstash::elasticsearch_port'].to_s + '"' }.join(',') %>]
ssl => true
}
}
```



## Remaining Work



Prior to the migration I relied on [stephenrjohnson/pupptmodule](https://github.com/stephenrjohnson/puppetmodule) to manage the puppet agent on Linux and macOS. Some work has been done on Puppet 4 compatability but there is still more to do. I'm close to updating the [agent pieces](https://github.com/dschaaff/puppetmodule) for my needs but there is a lot of work to add puppet master support.
