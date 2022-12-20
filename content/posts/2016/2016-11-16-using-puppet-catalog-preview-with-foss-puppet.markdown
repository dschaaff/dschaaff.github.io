---
author: dschaaff
categories:
- techology
comments: true
date: "2016-11-16T00:20:42Z"
link: http://danielschaaff.com/2016/11/15/using-puppet-catalog-preview-with-foss-puppet/
slug: using-puppet-catalog-preview-with-foss-puppet
tags:
- puppet
- puppet3
- puppet4
title: Using Puppet Catalog Preview with FOSS Puppet
wordpress_id: 563
---

We're working to upgrade our infrastructure to Puppet 4 and are making use of the [catalog preview tool](https://forge.puppet.com/puppetlabs/catalog_preview#migrate4_empty_string_true) to help identify code that needs to be updated. The preview tool in and of itself is handy, but the output it produces can be a bit daunting. During the ["Getting to the Latest Puppet"](http://www.slideshare.net/PuppetLabs/puppetconf-2016-getting-to-the-latest-puppet-nate-mccurdy-elizabeth-wittig-plumb-puppet) talk at puppetconf they pointed out a tool that professional services uses to create a nice html version of the output. Naturally I got excited to use this, but discovered it doesn't properly work with open source Puppet due to some hardcoded Puppet Enterprise paths. Fortunately it was only 3 lines to update! My fork is [here](https://github.com/dschaaff/prosvc-preview_report) if its useful to others.
