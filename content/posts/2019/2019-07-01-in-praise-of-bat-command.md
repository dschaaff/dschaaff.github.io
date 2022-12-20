---
author: dschaaff
categories:
- tools
- techology
- cli
comments: true
date: "2019-07-01T00:00:00Z"
slug: in-praise-of-the-bat-command-line-tool
tags:
- tools
- techology
- cli
title: In Praise of the Bat Commandline Tool
---

I’ve been working on helm charts a lot lately. For better or worse that has involved running `helm install —debug —dry-run…` a lot to ensure things render correctly. It is much easier to parse that output when there is syntax highlighting. Enter [bat]([GitHub - sharkdp/bat: A cat(1) clone with wings.](https://github.com/sharkdp/bat)). I can `helm install —debug —dry-run… | bat -l yaml` to get full syntax highlighting.  It’s a small thing but it makes a big difference.

[GitHub - sharkdp/bat: A cat(1) clone with wings.](https://github.com/sharkdp/bat)
