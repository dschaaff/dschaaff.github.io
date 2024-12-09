---
author: dschaaff
categories:
  - aws
  - kubernetes
  - eks
comments: true
date: "2024-12-09"
slug: eks-auto-mode
tags:
  - aws
  - kubernetes
  - eks
title: Think Twice Before Adopting EKS Auto Mode
---

I've been using AWS' EKS service for many years. What began as a bare bones
offering has slowly and steadily added more features over the years. Amazon
recently announced "auto" mode for EKS clusters. In this new mode AWS manages a
Karpenter installation, core addons such as coredns and vpc-cni, as well as the
underlying ec2 nodes. On paper this sounds great, but as always the devil is in
the details. In this instance, it is important to be aware of the ~ 10% price premium for
this mode. In auto mode AWS will charge an hourly rate for the ec2 instances in
addition to the standard instance charge. While some folks may find this a fair
trade off, I would not choose this mode myself. I run clusters using Karpenter
as the autoscaler and use
[bottlerocket-os](https://github.com/bottlerocket-os/bottlerocket) for the
underlying nodes. In this configuration the management overhead is extremely low. I even gave a [short talk on this setup for AWS](https://www.youtube.com/watch?v=ngFciGgJsbw) a while back. IMO, if you are already running an EKS cluster using Karpenter and Bottlerocket you have little to gain from auto mode relative to the price premium.
