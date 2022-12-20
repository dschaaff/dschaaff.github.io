---
author: dschaaff
categories:
- techology
comments: true
date: "2016-11-11T01:58:29Z"
link: http://danielschaaff.com/2016/11/10/no-longer-barfing-at-the-mention-of-chatops/
slug: no-longer-barfing-at-the-mention-of-chatops
tags:
- chatops
- devops
- lita
- puppet
- ruby
- technology
title: No Longer Barfing at the Mention of ChatOps
wordpress_id: 555
---

I've poked a lot of fun at chatops but I have found some value in portions of the practice. Let me state upfront that I do not believe paying attention to the chat room all day and having your attention interrupted non-stop is a productive or healthy practice. I have found some big benefits to "chatops" however.



## Visibility



Work that is done in the chatroom, or filtered into the chatroom, is visible to the whole team. This helps the team be aware of what others are doing and stay up to date. I've picked up on quite a few things from this that I wouldn't have learned other wise. This is also why we choose to route a fair amount of notifications into chat. For example we have Jira connected to HipChat and it makes it really easy to stay on top of issues. We also push commit notifications, build notifications, etc in the chatroom. The downside to this is that the rooms get noisy and make it harder to follow actual conversations between humans. One strategy we use to combat that is creating multiple rooms and focusing them around a subject.



## Less Context Switching



I have the ability to accomplish a task without context switching using bots even better then the visibility wins. We have HipChat open all day. It is far quicker to switch the focus to the HipChat window, slam in a command, and get back to what we were doing then it is to ssh into a server, run a command, and then log back out. My [co-worker](https://twitter.com/jonathangnagy) got inspired by Kevin Paulisse's [talk at puppet conf](https://speakerdeck.com/kpaulisse/puppetconf-2016-scaling-puppet-and-puppet-culture-at-github) and their use of [Hubot](https://hubot.github.com) and led us to get our chatops on using the [open source bot Lita](https://www.lita.io). We've already done some work on our own plugins and I'm now sold on working this way.



### Using Lita





#### Working with Active Directory



Our [lita-activedirectory](https://github.com/knuedge/lita-activedirectory) plugin allows us to leverage our [cratus gem](https://github.com/knuedge/cratus) to query and interact with Active Directory over ldap. It currently supports checking if a user account is locked, and unlocking the user account. We plan to extend to allow querying for group memberships and other attributes. One less reason to open Active Directory Users and Computers!



#### Working with Puppet



Our [lita-puppet[(https://github.com/knuedge/lita-puppet) plugin lets interact with our puppet infrastructure and puppetdb. Some tasks it currently supports
- list all nodes containing a specific class
- list all the profile and role classes applied to a node
- clean a cert off the puppet master
- run the puppet agent on a node
- deploy code with r10k on the puppet master



#### Other Cool Uses



We've implemented other helpful abilities
- run a dns lookup using dig
- ping a name or IP
- test for the availability of a URL
- Reset OTP tokens

It has been huge to accomplish these tasks without leaving the keyboard in an app I already have open all the time. Combine this with the visiblity chatops brings to our team and we have a winning combination. I'd encourage you to give it a shot, while we all remember to be mindful of people's time and attention.
