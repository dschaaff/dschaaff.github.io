---
author: dschaaff
categories:
- techology
comments: true
date: "2016-08-16T02:29:44Z"
link: http://danielschaaff.com/2016/08/15/openvpn-and-ec2-jumbo-frames/
slug: openvpn-and-ec2-jumbo-frames
tags:
- aws
- ec2
- networking
- openvpn
title: OpenVPN and ec2 Jumbo Frames
wordpress_id: 537
---

While troubleshooting site to site links running OpenVPN recently I ran into an issue with MTU sizing on the ec2 end. When we originally setup the links we followed the performance tuning advice found [here](https://community.openvpn.net/openvpn/wiki/Gigabit_Networks_Linux). The relevant portion is that we set `tun-mtu 6000` Why did we do this? Here's OpenVPN's explanation



<blockquote>
  By increasing the MTU size of the tun adapter and by disabling OpenVPN's internal fragmentation routines the throughput can be increased quite dramatically. The reason behind this is that by feeding larger packets to the OpenSSL encryption and decryption routines the performance will go up. The second advantage of not internally fragmenting packets is that this is left to the operating system and to the kernel network device drivers. For a LAN-based setup this can work, but when handling various types of remote users (road warriors, cable modem users, etc) this is not always a possibility.
</blockquote>



During later testing we discovered that we could easily push 40mb/s over the OpenVPN tunnel into the ec2 instance, but only 1mb/s or less going the opposite direction. Obviously not ideal.

So how does ec2 MTU sizing play into this? We had on premise VMs on one side with a standard mtu of 1500 on their interfaces while the ec2 instance had jumbo frames enabled with an mtu of 9001. This meant that our traffic towards ec2 was being fragmented into an MTU of 1500 by linux  on the on premise OpenVPN instance. Traffic out of ec2 towards the other end was not fragmented by the vpn instance at all and was sent along to the internet gateway where it would be chopped into a standard MTU size to cross the internet. This manifested itself in significantly lower throughput going from ec2 to the on premise instance.

After discovering this we set the MTU of eth0 on the ec2 instance to 1500 and lowered the OpenVPN tun-mtu to 1500. Since implementing those changes we are achieving equal throughput in both directions!
