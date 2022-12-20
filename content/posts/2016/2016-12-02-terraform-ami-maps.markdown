---
author: dschaaff
categories:
- techology
comments: true
date: "2016-12-02T03:44:51Z"
link: http://danielschaaff.com/2016/12/01/terraform-ami-maps/
slug: terraform-ami-maps
tags:
- aws
- technology
- terraform
title: Terraform AMI Maps
wordpress_id: 594
---

Up until today we had been using a map variable in terraform to choose our ubuntu 14 ami based on region.

```
variable "ubuntu_amis" {
    description = "Mapping of Ubuntu 14.04 AMIs."
    default = {
        ap-northeast-1 = "ami-a25cffa2"
        ap-southeast-1 = "ami-967879c4"
        ap-southeast-2 = "ami-21ce8b1b"
        cn-north-1     = "ami-d44fd2ed"
        eu-central-1   = "ami-9cf9c281"
        eu-west-1      = "ami-664b0a11"
        sa-east-1      = "ami-c99518d4"
        us-east-1      = "ami-c135f3aa"
        us-gov-west-1  = "ami-91cfafb2"
        us-west-1      = "ami-bf3dccfb"
        us-west-2      = "ami-f15b5dc1"
    }
}
```

We would then set the ami id like so when creating an ec2 instance.

```
ami = "${lookup(var.ubuntu_amis, var.region)}"
```

The problem we ran into is that we now use Ubuntu 16 by default and wanted to expand the ami map to contain its ID's as well. I quickly discovered that nested maps like the one below work.

```
 variable "ubuntu_amis" {
    description = "Mapping of Ubuntu 14.04 AMIs."
    default = {
        "ubuntu14" = {
          ap-northeast-1 = "ami-a25cffa2"
          ap-southeast-1 = "ami-967879c4"
          ap-southeast-2 = "ami-21ce8b1b"
          cn-north-1     = "ami-d44fd2ed"
          eu-central-1   = "ami-9cf9c281"
          eu-west-1      = "ami-664b0a11"
          sa-east-1      = "ami-c99518d4"
          us-east-1      = "ami-c135f3aa"
          us-gov-west-1  = "ami-91cfafb2"
          us-west-1      = "ami-bf3dccfb"
          us-west-2      = "ami-f15b5dc1"
      }
      "ubuntu16" = {
          ap-northeast-1 = "ami-a25cffa2"...
```

I also tried the solution from this [old github issue](https://github.com/hashicorp/terraform/issues/191) but it is no longer valid since the concat function only accepts lists now. In the end I landed using a variable for os version and setting it like this.

```
variable "os-version" {
    description = "Whether to use ubuntu 14 or ubuntu 16"
    default     = "ubuntu16"
}
ariable "ubuntu_amis" {
    description = "Mapping of Ubuntu 14.04 AMIs."
    default = {
          ubuntu14.ap-northeast-1 = "ami-a25cffa2"
          ubuntu14.ap-southeast-1 = "ami-967879c4"
          ubuntu14.ap-southeast-2 = "ami-21ce8b1b"
          ubuntu14.cn-north-1     = "ami-d44fd2ed"
          ubuntu14.eu-central-1   = "ami-9cf9c281"
          ubuntu14.eu-west-1      = "ami-664b0a11"
          ubuntu14.sa-east-1      = "ami-c99518d4"
          ubuntu14.us-east-1      = "ami-c135f3aa"
          ubuntu14.us-gov-west-1  = "ami-91cfafb2"
          ubuntu14.us-west-1      = "ami-bf3dccfb"
          ubuntu14.us-west-2      = "ami-f15b5dc1"
          ubuntu16.ap-northeast-1 = "ami-a68e3ec7"
          ubuntu16.ap-southeast-1 = "ami-5b7ed338"
          ubuntu16.ap-southeast-2 = "ami-e2112881"
          ubuntu16.cn-north-1     = "ami-593feb34"
          ubuntu16.eu-central-1   = "ami-df02c5b0"
          ubuntu16.eu-west-1      = "ami-be376ecd"
          ubuntu16.sa-east-1      = "ami-8f34aae3"
          ubuntu16.us-east-1      = "ami-2808313f"
          ubuntu16.us-gov-west-1  = "ami-19d56d78"
          ubuntu16.us-west-1      = "ami-900255f0"
          ubuntu16.us-west-2      = "ami-7df25b1d"
    }
}
```

Then using a lookup such as this when creating an instance

```
ami = "${lookup(var.ubuntu_amis, "${var.os-version}.${var.region}")}"
```

Hopefully this helps someone out and if you know of a better way to accomplish this please share!
