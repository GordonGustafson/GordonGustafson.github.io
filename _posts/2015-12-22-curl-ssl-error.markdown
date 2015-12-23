---
layout: post
title:  "'curl: server certificate verification failed' on Debian Stretch"
date:   2015-12-22
tags: [how-i-fixed debian]
---

    $ curl -O https://raw.githubusercontent.com/bagder/ca-bundle/master/ca-bundle.crt
    % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                    Dload  Upload   Total   Spent    Left  Speed
    0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
    curl: (60) server certificate verification failed. CAfile: /etc/ssl/certs/ca-certificates.crt CRLfile: none
    More details here: http://curl.haxx.se/docs/sslcerts.html

    curl performs SSL certificate verification by default, using a "bundle"
    of Certificate Authority (CA) public keys (CA certs). If the default
    bundle file isn't adequate, you can specify an alternate file
    using the --cacert option.
    If this HTTPS server uses a certificate signed by a CA represented in
    the bundle, the certificate verification probably failed due to a
    problem with the certificate (it might be expired, or the name might
    not match the domain name in the URL).
    If you'd like to turn off curl's verification of the certificate, use
    the -k (or --insecure) option.

Curl is using the certificates in `/etc/ssl/certs/ca-certificates.crt`.
Let's see what's in there:

    $ cat /etc/ssl/certs/ca-certificates.crt
    $

It's empty.
What's supposed to be in there?

    $ apt-file search !$
    apt-file search /etc/ssl/certs/ca-certificates.crt
    $

No known debian package provides `/etc/ssl/certs/ca-certificates.crt`, which is evidently where `curl` looks for certificates by default.
We need to provide `curl` with a certificate.
Let's go certificate hunting!

    $ aptitude search "~i cert"
    i A ca-certificates                 - Common CA certificates
    i A ca-certificates-java            - Common CA certificates (JKS keystore)
    $ apt-file list ca-certificates | grep '\.crt$'
    *** 180 certificates in /usr/share/ca-certificates/mozilla/ ***

The `--cacert` option takes one certificate file, and I have no idea which of the 180 to use.
`man curl` tells me there's also `--capath`, which does exactly what I want:

    $ curl --capath /usr/share/ca-certificates/mozilla/ -O https://raw.githubusercontent.com/bagder/ca-bundle/master/ca-bundle.crt
    $

Victory!
We're trusting a **lot** of authorities here, but it's better than ignoring the certificate with `curl --insecure`.

Keen observers have probably noticed that I'm downloading a certificate file; that's because this journey was a side-quest for [this one][rubygems-ssl-error].

[rubygems-ssl-error]: {% post_url 2015-12-22-rubygems-ssl-error %}
