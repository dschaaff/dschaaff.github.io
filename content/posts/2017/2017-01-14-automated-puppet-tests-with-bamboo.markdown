---
author: dschaaff
categories:
- techology
comments: true
date: "2017-01-14T00:07:47Z"
link: http://danielschaaff.com/2017/01/13/automated-puppet-tests-with-bamboo/
slug: automated-puppet-tests-with-bamboo
tags:
- bamboo
- puppet
- rspec
- technology
title: Automated Puppet Tests with Bamboo
wordpress_id: 672
---

[rnelson0](https://twitter.com/rnelson0?lang=en) is walking through [automated Puppet testing with Jenkins ](https://rnelson0.com/2017/01/12/automating-puppet-tests-with-a-jenkins-job-version-1-0/)on his blog. I thought I'd highlight how you can use a similar workflow in Atlassian's Bamboo application. This assumes you already have a working Bamboo setup and are familiar with the general process for testing Puppet modules with rspec.



## Create a new plan



The first step is to set up a new plan to use for the testing. Click "Create" and then "Create a new plan" in the top menu bar.![Pasted_Image_1_13_17__3_08_PM.png]({{ site.url }}/assets/img/pasted_image_1_13_17__3_08_pm.png)

Bamboo organizes related jobs, builds, etc. into projects. On the next screen either create a new Puppet project or select an existing project if you've already set that up.![Pasted_Image_1_13_17__3_10_PM.png]({{ site.url }}/assets/img/pasted_image_1_13_17__3_10_pm.png)

Fill in the details and select the repository you'd like to start testing. Bamboo can read from a large number of version control systems. I happen to use Bitbucket so this is simple.  Once your happy with the selections click "Configure plan".

![Pasted_Image_1_13_17__3_15_PM.png]({{ site.url }}/assets/img/pasted_image_1_13_17__3_15_pm.png)

At the next screen we setup our tasks. The first task, Source Code Checkout, is added by default and checks out the repo configured in the previous step. I like to break things down into small script tasks so they are easier to troubleshoot and duplicate between jobs.

Click add task and select script.

![Pasted_Image_1_13_17__3_17_PM.png]({{ site.url }}/assets/img/pasted_image_1_13_17__3_17_pm.png)

The first step is to setup the Ruby environment. This presumes you already have Bamboo build agents up and running and that rvm is installed. This script below is what I use. It performs some checks to ensure ruby is properly setup.

```bash
#!/bin/bash

if [ "$(ps -p "$$" -o comm=)" != "bash" ]; then
 /bin/bash "$0" "$@"
 exit "$?"
fi
source /etc/profile.d/rvm.sh
ruby="ruby-2.1.8"
install_count=`rvm list | grep $ruby | wc -l`

if [ $install_count -lt 1 ]; then
  rvm install $ruby
fi
rvm use $ruby
rvm user gemsets
```

Now click "Add task" and add another script task. We will use this script step to create a new gemset and install the gems listed in the Gemfile using bundler. If you use environment variables to specify the version of puppet to install enter that in the environment variables field.![Pasted_Image_1_13_17__3_55_PM.png]({{ site.url }}/assets/img/pasted_image_1_13_17__3_55_pm.png)

```bash
#!/bin/bash

if [ "$(ps -p "$$" -o comm=)" != "bash" ]; then
/bin/bash "$0" "$@"
exit "$?"
fi
source /etc/profile.d/rvm.sh
ruby="ruby-2.1.8"
gemset="puppet-4.8.0-validate"
rvm use $ruby
rvm user gemsets
rvm gemset create $gemset
rvm gemset use $gemset
gem install bundler
rm Gemfile.lock
bundle install
```

Now lets add another script step and use it to run the actual tests. At the end of the test this removes the Gemset to ensure a clean environment for each new run.  You can also add other options here using environment variables such as `STRICT_VARIABLES=no`.![Pasted_Image_1_13_17__3_30_PM.png]({{ site.url }}/assets/img/pasted_image_1_13_17__3_30_pm.png)

```bash
#!/bin/bash

if [ "$(ps -p "$$" -o comm=)" != "bash" ]; then
/bin/bash "$0" "$@"
exit "$?"
fi
source /etc/profile.d/rvm.sh
ruby="ruby-2.1.8"
gemset="puppet-4.8.0-validate"
rvm use $ruby
rvm user gemsets
rvm gemset use $gemset

bundle exec rake validate
bundle exec rake spec
rvm gemset delete --force $gemset
```

We've now covered the basics needed to get started and can click create at the bottom to finalize the plan. After doing so we'll be looking  at the job configuration page. The script tasks we just created are in the "Default Job".

![Pasted_Image_1_13_17__3_33_PM.png]({{ site.url }}/assets/img/pasted_image_1_13_17__3_33_pm.png)That name isn't very clear so lets update it. Click "Default Job" and then select the "Job details" tab. Here we'll enter a more descriptive name such as "Puppet 4.8 rspec" and click save. A descriptive name is handy because you can clone stages across plans to avoid repeating the script setup.



## Repository Triggers



If you're using the built in Bitbucket Server integration like my setup then Bamboo will automatically run the plan whenever a new commit is pushed to the repo. You can customize the type of trigger to use polling, scheduling, or other options as well. Simply select the "Triggers" tab under plan configuration and set appropriately.![Pasted_Image_1_13_17__3_37_PM.png]({{ site.url }}/assets/img/pasted_image_1_13_17__3_37_pm.png)



## Tracking Results



If you were to run this plan now you would get a pass or fail in Bamboo  but would have to look at the logs for the job to see the actual details of the results. We can use JUnit to get an better view of the test in Bamboo. To set this up create a `.rspec` file in the root of the Puppet module you're testing with the following content

```
--format RspecJunitFormatter
--out results.xml
```

This will write the results of the test in JUnit format to the results.xml file. You'll also want to add this file to `.gitignore`. Now we can add a step to our plan to parse this file. Go back to the plan configuration and select the stage we setup previously. Click add task and select "JUnit Parser."![Pasted_Image_1_13_17__3_41_PM.png]({{ site.url }}/assets/img/pasted_image_1_13_17__3_41_pm.png)

Configure the JUnit parser so it finds our results file.![Pasted_Image_1_13_17__3_42_PM.png]({{ site.url }}/assets/img/pasted_image_1_13_17__3_42_pm.png)

Click save and run the plan again. You'll now see your test results nicely formatted in the plan history. Here's an example of what that looks like for failed tests.![Pasted_Image_1_13_17__3_44_PM.png]({{ site.url }}/assets/img/pasted_image_1_13_17__3_44_pm.png)![Pasted_Image_1_13_17__3_45_PM.png]({{ site.url }}/assets/img/pasted_image_1_13_17__3_45_pm.png)

You don't need to redo this work for each repo. When setting up a plan for a new module start by removing the default stage. Then click add job and select clone an existing job. ![Pasted_Image_1_13_17__4_01_PM.png]({{ site.url }}/assets/img/pasted_image_1_13_17__4_01_pm.png)

You can then select which plan to clone from.![Pasted_Image_1_13_17__4_02_PM.png]({{ site.url }}/assets/img/pasted_image_1_13_17__4_02_pm.png)

It takes a bit more setup then using Travis CI but its not difficult to get rspec testing up and running in Bamboo.


