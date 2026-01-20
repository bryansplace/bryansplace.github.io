---
layout: page
title: Welcome to Bryan's Place
permalink: /
---

## Welcome

## Repurposing old smartphones

Welcome to my corner of the web. I like to breath new life into "obsolete" phones. Specifically turning them into functional, low-power homelabs. 

### PostmarketOS
After looking at loads of alternatives, I homed in on PostmarketOS. 
The big plus for me is its ability to run Docker.

It has an installation program, Pmbootstrap, that makes things real simple ( no rooting). 

I have two Xiaomi A1 devices that have been running for over three years with no problems.

The aim of this site is to supplement the excellent PostmarketOS (pmos) documentation which contains a tremendous amount of information by focusing on my real world experiences relating to one model.

The key steps are
- [Install Pmbootstrap on PC](#-install-pmbootstrap)
- [Connect the phone](connect_phone.md)
- [PostmarketOS; initialise options](pmbootstrap_init.md)
- [PostmarketOS; install options](pmbootstrap_install.md)
- [Flash new system](pmbootstrap_flasher.md)
- [Setup your network WIFI](setup_wifi.md)

This gives you a functioning server.
I go on to install and use:

- [Docker](docker.md)
- [Firewall](firewall.md)
- [Home Assistant](hass.md)
- [Reduce battery voltage](https://github.com/bryansplace/PostmarketOS-limit-battery-voltage/blob/main/README.md)
- [Bluetooth](bluetooth.md)
- Jupyter
- Connect to NAS storage
- [Tailscale](tailscale.md)

## Install PMbootstrap

You will need a linux based system. 

The official installation instructions can be found [here](https://wiki.postmarketos.org/wiki/Installing_pmbootstrap)

Unfortunately, there are are altrrnatives, which as a beginner, confused me more than it should.

I personally found [using pip](https://wiki.postmarketos.org/wiki/Pmbootstrap/Installation#Using_pip) simplest.


Whatever, in the end...To test that pmbootstrap is installed correctly, run:

    pmbootstrap --version


