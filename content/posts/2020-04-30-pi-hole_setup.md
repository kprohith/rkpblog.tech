---
toc: true
layout: post
description: Pi-Hole DNS sink over HTTPS
categories: [markdown]
title: Configuring Pi-Hole with DNS over HTTPS
---

# Pi-Hole DNS over HTTPS
I just set up [DNS over HTTPS](https://en.wikipedia.org/wiki/DNS_over_HTTPS) using [this guide](https://docs.pi-hole.net/guides/dns-over-https/) on my [pi-hole](https://pi-hole.net) DNS sink.

Basically,

1. Get the [cloudflared](https://developers.cloudflare.com/1.1.1.1/dns-over-https/cloudflared-proxy/) daemon compiled for ARM (Raspberry Pi)

2. Configure it as a service that runs on startup under a user named cloudflared

3. Use systemd to start the service (or reboot, I guess?)

Ultimately this still goes to Cloudflare’s 1.1.1.1 DNS, except routed over HTTPS:
```bash
# Commandline args for cloudflared
CLOUDFLARED_OPTS=--port 5053 --upstream https://1.1.1.1/dns-query --upstream https://1.0.0.1/dns-query
```
Why? Basically, with traditional HTTPS people don’t know what you are browsing (because it’s encrypted) but they can *definitely tell what website (domain name) you are looking at*.

Even if you are visiting a site using HTTPS, your DNS query is sent over an unencrypted connection. That means that even if you are browsing https://cloudflare.com, anyone listening to packets on the network knows you are attempting to visit cloudflare.com.

The second problem with unencrypted DNS is that it is easy for a Man-In-The-Middle to change DNS answers to route unsuspecting visitors to their phishing, malware or surveillance site. DNSSEC solves this problem as well by providing a mechanism to check the validity of a DNS answer, but only a single-digit percentage of domains use DNSSEC.

With DNS over HTTPS, a man in the middle can’t see the content, nor can they tell what website you are on. Also prevents tampering with DNS as as side benefit. :sweat_smile:
