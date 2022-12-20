---
author: dschaaff
categories:
- techology
comments: true
date: "2017-08-17T21:15:40Z"
link: http://danielschaaff.com/2017/08/17/bash-function-to-ssh-into-ec2-instances/
slug: bash-function-to-ssh-into-ec2-instances
tags:
- aws
- bash
- ec2
title: Bash Function to SSH into ec2 Instances
wordpress_id: 850
---

I've often found myself with an instance id that I want to login to look at something. It sucks looking up the IP when you don't know the DNS name. I'm sure there are other ways to do this but here is what I came up with.

```bash
getec2ip() {
 aws ec2 describe-instances --instance-ids $1 | jq [.Reservations[0].Instances[0].PrivateIpAddress] | jq --raw-output .[]
}

assh() {
 host=$(getec2ip ${1})
 ssh user@${host}
}
```

This relies on the aws cli and jq to parse out the ip and has made it much easier for me to quickly hop on an instance.
