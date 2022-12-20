---
author: dschaaff
categories:
- tools
- techology
- cli
- scripts
- kubernetes
comments: true
date: "2020-03-03T00:00:00Z"
slug: cert-manager-bug-lets-encrypt-k8s-renewal
tags:
- tools
- techology
- cli
- kubernetes
title: Lets Encrypt Bug Requires Reissue of Certificates by Cert-Manager in Kubernetes
---

I received a fun email from Lets Encrypt today letting me know that they were revoking all of my certificates on March 4. The bug is described [here](https://community.letsencrypt.org/t/2020-02-29-caa-rechecking-bug/114591).

All of my certificates are managed by [cert-manager](https://cert-manager.io/docs/) inside Kubernetes. This led to the fun challenge of figuring out how to force
a reissue of every certificate. There were 2 approaches that came up in the Kubernetes community slack.

1) Delete all secrets containing cert-manager issued certificates.

2) Force a renewal by updating the `renewBefore` field in the certificate spec.

I opted for option 2 to avoid any requests being served with the default Nginx Ingress Controller certificate while the certificates were being reissued. Thankfully, this was
actually a pretty painless operation.

I used the script below to add the `renewBefore` field to every certificate in a cluster.

<script src="https://gist.github.com/dschaaff/95bb21604139b8b3da27912050cc2347.js"></script>

Then after confirming the certificates have been successfully reissued, just swap the comment on lines 10-11 and re-run the script to remove the `renewBefore` field from the certificate specs.