---
author: dschaaff
categories:
- techology
comments: true
date: "2017-11-21T16:37:54Z"
link: http://danielschaaff.com/2017/11/21/exporting-pagerduty-incident-data/
slug: exporting-pagerduty-incident-data
tags:
- pagerduty
- python
- technology
title: Exporting Pagerduty Incident Data
wordpress_id: 865
---

Pagerduty provides a built-in way to export your incident data but only a limited number of data fields are available on the basic plan. Rather than upgrade you can use the API to export the data to a CSV. I found this [gist](https://gist.github.com/ryanhoskin/b9c305274627c783f0d7) listed [here](https://community.pagerduty.com/t/reporting-using-our-api-to-create-custom-reports/464). The python script works great but some of my incident messages contained JSON data that threw off Excel when opening the CSV. I slightly modified the script with character escaping to work around this (lines 98- 107).

<script src="https://gist.github.com/dschaaff/896fd2e3a5481551ddbab3171773ea33.js"></script>
