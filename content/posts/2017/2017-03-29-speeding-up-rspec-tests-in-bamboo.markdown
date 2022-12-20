---
author: dschaaff
categories:
- techology
comments: true
date: "2017-03-29T18:18:23Z"
link: http://danielschaaff.com/2017/03/29/speeding-up-rspec-tests-in-bamboo/
slug: speeding-up-rspec-tests-in-bamboo
tags:
- puppet
- rspec
- technology
title: Speeding up RSpec Tests in Bamboo
wordpress_id: 815
---

Now that [roles and profiles are in my control repo](http://danielschaaff.com/2017/03/29/tackling-tech-debt-in-puppet/) my RSpec tests are taking longer then ever. As of this writing the control repo contains 938 tests and I'm still a long way from 100% coverage. This really slows down the feedback loop when running tests. When running locally I often just run RSpec against a specific spec file rather then run the whole test suite, but I still wanted a way to speed things up in Bamboo.

I had used [parallel_tests](https://github.com/grosser/parallel_tests) before to run things quicker on my local machine but was having issues with each test overwriting the JUnit output file and giving me an incomplete result set at the end. I stumbled across a fix for this yesterday which I'm pretty happy with. My original .rspec file had the file name of the JUnit output hard coded.```
--format documentation
--color
--format RspecJunitFormatter
--out results.xml
```
By making the following change each parallel test writes to its own JUnit output file.
```
--format documentation
--color
--format RspecJunitFormatter
--out results<%= ENV['TEST_ENV_NUMBER'] %>.xml
```

Bamboo was already parsing the results using a wildcard so no change was needed there (see [this post](http://danielschaaff.com/2017/01/13/automated-puppet-tests-with-bamboo/) for details on my Bamboo setup).  The last step was to change the rake task Bamboo is running from `rake spec` to `rake parallel_spec`. This change cut the test time down from an average of 24 minutes to 8 minutes and faster feedback is always a plus!


