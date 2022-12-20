---
author: dschaaff
categories:
- techology
comments: true
date: "2017-10-06T23:19:08Z"
link: http://danielschaaff.com/2017/10/06/escaping-characters-in-jenkins-declarative-pipelines/
slug: escaping-characters-in-jenkins-declarative-pipelines
tags:
- jenkins
- technology
title: Escaping Characters in Jenkins Declarative Pipelines
wordpress_id: 860
---

I spent way too long troubleshooting a script step in a Jenkins declarative pipeline this week. After lots of frustration I stumbled upon this [gist](https://gist.github.com/cyrille-leclerc/8cad9d1b35ea553820a1) that was a life saver. Long story short you need `\\` to escape literal quotes.
