---
author: dschaaff
comments: true
date: "2016-08-19T01:07:47Z"
link: http://danielschaaff.com/2016/08/18/digium-switchvox-6-3-upgrade-issues/
slug: digium-switchvox-6-3-upgrade-issues
tags:
- digium
- sip
- switchvox
- voip
title: Digium Switchvox 6.3 Upgrade Issues
wordpress_id: 544
---

I ran into issues upgrading two Digium Switchvox PBX's to version 6.3 last night where they would no longer register with our SIP provider after the upgrade. Not a fun way to end a maintenance window.

In both cases this was caused by differences in the new version of Asterick running under the hood (v13). Under the old versions we were required to configure the Proxy Host under the VoIP provider settings. After upgrading to 6.3 the proxy host setting caused the registration with our provider to fail. There wasn't anything in the release notes pointing directly to this change but Digium support pointed it out. [The best I can tell the behavior was likely introduced in version 6.1](http://kb.digium.com/articles/FAQ/Known-Issues-6-1?retURL=%2Fapex%2FknowledgeProduct%3Fc%3DKnown_Issues&popup=false) (we upgraded from version 5 directly to 6.3). After removing the proxy host setting from the VoIP provider config in the Digium were able to successfully register and make calls.

If you have trouble registering with your provider or making calls after upgrading beyond version 6 of Switchvox I'd recommend removing the proxy host from the provider settings if you have it set.![Pasted_Image_8_18_16__6_06_PM.png]({{ site.url }}/assets/img/pasted_image_8_18_16__6_06_pm.png)
