---
title: Tailscale
date: 2026-04-11
published: false
layout: post

---

## Tailscale

Tailscale [installation](https://tailscale.com/docs/install/start) is straightforward but there are no alpine linux specific instructions

Alpine Linux has a tailscale package: https://pkgs.alpinelinux.org/packages?name=tailscale

Installation steps:

1) Install Tailscale on Alpine Linux
```doas apk add tailscale```

2) Use OpenRC to enable and start the service
```doas rc-update add tailscale```
```doas rc-service tailscale start```

3) Authenticate and connect your machine to your Tailscale network
tailscale up
You’re connected! You can find your Tailscale IPv4 address by running:
tailscale ip -4

