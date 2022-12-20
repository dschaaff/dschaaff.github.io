---
author: dschaaff
categories:
- tools
- techology
- cli
- consul
comments: true
date: "2021-02-03T00:00:00Z"
slug: renew-consul-root-ca-certificate
tags:
- tools
- techology
- cli
- consul
title: How to Renew Consul Root CA Certificate
---

The Consul root CA is generated using the `consul tls ca create ` command. If created with the original options the root CA is only valid for a few years. After running production for a while you inevitably need to extend this certificate. To do so we need to generate and sign a new certificate using the existing private key. Consul does not provide any commands for doing so but it can be done using OpenSSL.

First, create a CSR from the existing Consul CA certificate.

`openssl x509 -x509toreq -in consul-agent-ca.pem -signkey consul-agent-ca-key.pem  -out renewedca.csr`

Next, we need to create an OpenSSL config file for generating the new certificate. Read the existing CA certificate with `openssl x509 -in consul-agent-ca.pem -noout -text`. Fill in the OpenSSL config file with the field values from the existing certificate.

```ini
[req]
distinguished_name = req_distinguished_name
prompt = no
[req_distinguished_name]
C = US
ST = CA
L = San Francisco
O = HashiCorp Inc.
OU = HashiCorp Inc.
CN = CN=Consul Agent CA 340255540300806611266427072427762750721
[v3_ca]
basicConstraints = critical, CA:true
keyUsage = digitalSignature, cRLSign, keyCertSign
subjectKeyIdentifier=61:39:3A:36:33:3A:30:31:3A:34:61:3A:32:65:3A:66:30:3A:33:61:3A:64:32:3A:66:31:3A:33:33:3A:65:39:3A:61:62:3A:61:64:3A:65:63:3A:31:37:3A:33:65:3A:39:36:3A:63:34:3A:61:30:3A:37:37:3A:36:61:3A:64:33:3A:64:63:3A:31:66:3A:33:36:3A:35:33:3A:30:38:3A:65:34:3A:38:64:3A:63:66:3A:37:66:3A:39:31
authorityKeyIdentifier = keyid:61:39:3A:36:33:3A:30:31:3A:34:61:3A:32:65:3A:66:30:3A:33:61:3A:64:32:3A:66:31:3A:33:33:3A:65:39:3A:61:62:3A:61:64:3A:65:63:3A:31:37:3A:33:65:3A:39:36:3A:63:34:3A:61:30:3A:37:37:3A:36:61:3A:64:33:3A:64:63:3A:31:66:3A:33:36:3A:35:33:3A:30:38:3A:65:34:3A:38:64:3A:63:66:3A:37:66:3A:39:32

```

Now we can generate a new certificate from the CSR.

`openssl x509 -req -days 3652 -in renewedca.csr -signkey consul-agent-ca-key.pem -out consul-agent-ca-new.pem -extfile ./renewedca.conf -extensions v3_ca`

Finally, verify a previously issued client or server cert against the new CA certificate.

```text
openssl verify -CAfile consul-agent-ca.pem -verbose server-consul-0.pem
server-consul-0.pem: OK
```

If the certificate successfully verifies then we can deploy the new certificate to servers and agents.
