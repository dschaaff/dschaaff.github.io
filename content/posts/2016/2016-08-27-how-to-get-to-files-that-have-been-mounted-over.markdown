---
author: dschaaff
categories:
- techology
comments: true
date: "2016-08-27T16:27:39Z"
link: http://danielschaaff.com/2016/08/27/how-to-get-to-files-that-have-been-mounted-over/
slug: how-to-get-to-files-that-have-been-mounted-over
tags:
- linux
- mounts
title: How to get to files that have been mounted over
wordpress_id: 549
---

You have a directory with data, and now you've mounted a volume over it. How do you get to the data in the underlying directory without interrupting the mounted volume? Bind mount to the rescue!
Bind mount the directory to another path and you can manipulate the files in underlying directory without disturbing the volume mounted atop of it.
