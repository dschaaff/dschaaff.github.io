---
author: dschaaff
categories:
- techology
comments: true
date: "2017-03-29T18:02:00Z"
link: http://danielschaaff.com/2017/03/29/tackling-tech-debt-in-puppet/
slug: tackling-tech-debt-in-puppet
tags:
- eyaml
- hiera
- puppet
- technology
title: Tackling Tech Debt in Puppet
wordpress_id: 787
---

I spent some time tackling technical debt in our Puppet code this week. The biggest outstanding item was implementing eyaml for protecting secrets in Hiera. I'd also been encouraging developers to contribute to the Puppet code base  for some time, but they were restricted from the control repo due to some secrets kept in Hiera. This put a big damper on collaboration as Hiera is the data engine for our roles and profiles. Separate git repos were also used for the profile and role modules due to this workflow.

[Hiera-eyaml](https://github.com/voxpupuli/hiera-eyaml) to the rescue! Props to [voxpupuli](https://github.com/voxpupuli) as this was dead simple to implement. Once the secrets were encrypted I tidied up a few more things before collaboration could rain down !




    
  * created a new branch on the existing control repo

    
  * moved the roles and profiles modules into the site directory of the control repo

    
  * create an environment.conf file to add the site dir to the module path

    
  * tested an r10k run on the new environment

    
  * spent some time fighting RSpec, as you do

    
  * merged into production

    
  * created a new git repo for the control module to remove commit history containing secrets

    
  * opened up access to the development team



We've now got a control repo with encrypted secrets open to contributions from across the org. I'm also enjoying the simplified workflow with environments now that hieradata, roles, and profiles are all in a single git repo.
