---
date: 2024-07-01
title: Reuse an Old Smart Phone
layout: page
showMiniToc: true
---
# Reuse an Old Smart Phone

[TOC]
(TOC)

## 1) Introduction

[PostmarketOS](https://wiki.postmarketos.org/wiki/About_postmarketOS)  runs an alpine linux distribution on smartphones and other mobile devices. 

Importantly for me, it can run Docker and I use it like a very powerful raspberry pi server. 
A plus is that a phone is neatly cased and has a power supply. Although there is a display, I don't use it.

They cover a wide variety of devices with varying degrees of  support. The [Installation]( https://wiki.postmarketos.org/wiki/Installation) guide covers lots of alternatives and I found it a bit overwhelming at first.

I have two old Xiaomi A1 (Tissot) phones for which there is the choice of using prebuilt images or install using their pmboostrap program.
Since I use without a display, I use the latter.



## 2) PMbootstrap

PostmarketOS has an installation program called [PMbootstrap](https://wiki.postmarketos.org/wiki/Pmbootstrap). This takes care of rooting and all the stuff required to install the system on your phone.

### 2.1) Install PMbootstrap

The installation instructions can be found [here](https://wiki.postmarketos.org/wiki/Installing_pmbootstrap)

To test that pmbootstrap is installed correctly, run:

    pmbootstrap --version

You should see the installed version.

### 2.2) Enable USB connection

In order to use a USB cable to communicate between the Android phone and the computer, something called 'USB debugging' needs to be enabled on the phone.

Go to the phone Settings -> About -> Tap build number repeatedly until developer menu is enabled. Then tap on “ developer options ” and tick USB debugging.

A Google search eg ' Tissot enable USB debugging' will give lots of instructions and videos if needed.

### 2.3) USB cable

I used to think all USB cables were more or less the same. But experience taught me that they are finiky and a source of problems. A cable might work sometimes, but not other times. If things in the subsequent steps don't go right; try another cable and/or USB port.

### 2.4) Test connection



## 3) PostmarketOS

There are two primary methods of installing postmarketOS. You can
  use a prebuilt image
or
  install using pmbootstrap
 
The prebuilt image includes various display options that I don't want to learn or use. So I install using pmbootstrap
 
#


If you have another device, search [here](https://wiki.postmarketos.org/wiki/Devices) to see if PostmarketOS is available. There are three categories: - main ( a few well supported devices) - community ( quite a few maintained devices) - testing ( devices people have worked on)

The Tissot is in the community category. My earlier Nexus 5 was in the testing category, but I got things working.

The official instructions contain lots and lots of info which can confuse a beginner ( at least me). The following are the steps I used.

Starting from a standard, charged device...

#### 3.1) Enable Fastboot mode

With phone powered off and USB cable unplugged, hold down Volume Down and Power buttons simultaneously for a few seconds, until something called Fastboot Mode screen appears.

#### 3.2) OEM unlock

Plug in the USB cable between PC and phone, then on the PC run:

    $ sudo fastboot oem unlock

When prompted on the the phone device, use Volume Up/Down buttons to highlight 'Yes' choice, then press the Power button to select it.

#### 3.3) Initialization

On your PC, run

    $pmbootstrap init

This will give a series of options. Accept all defaults, except ( for the Tissot device) choose:

Channel: Edge;   

Vendor: Xiaomi ;   

Device codename: Tissot   

User Interface:. none Additional packages : nano,networkmanager,networkmanager-wifi,networkmanager-cli,networkmanager-tui,wpa_supplicant

You'll be asked for a username . This will later be needed for login to the phone device ( both initial Screen and later SSH)

Notes: The channel has two options. A stable release or edge. I first chose stable release, but layer found I couldn't access a NAS file share because 'cifs' kernel requirements were only in edge. This might change in future. Also, it is strange addiditonal network packages are needed.

#### 3.4) Build images

On your PC, run

    $ pmbootstrap install

This builds the boot and system images that are to be installed. You'll be asked to set a password for logging onto the phone device later on. ( Ignore any keymap warning)

#### 3.5) Flash to phone

Ensure device is still in fastboot mode ( ref 1 above), and plugged in via USB. Then, on the PC, run:
```
    fastboot erase system_a
    fastboot erase system_b
    pmbootstrap flasher flash_lk2nd --partition=boot_a
    pmbootstrap flasher flash_lk2nd --partition=boot_b
    pmbootstrap flasher flash_rootfs
    fastboot reboot
```


Wait a while (a couple of minutes) as the phone reboots with PostmarketOS.

#### 3.6) Login and enable SSH

With the USB cable still connected, from your PC terminal ; with your user name run

    $ssh user@172.16.42.1  

You will be asked for your password from step 3.4.

Hopefully, you now have control of the phone from your computer.

## 4) Setup WIFI

In the 'edge' release used, network stuff needs to be started automatically and (strangely) In the SSH session, set up the WIFI ( and add network stuff to start)in the SSH run
```
    sudo rc-update add networkmanager
    sudo rc-update add wpa_supplicant
    sudo ntpd -d -q -n -p pool.ntp.org
    sudo nmtui
```
This gives a 'somewhat' intuitive screen where you can activate the WIFI. (Use a google search for 'nmtui' for instructions if needed).

You'll probably want to set up a static IP address for the phone on your router or preferably using nmtui. A static address can be set using nmtui explained [here](https://www.tecmint.com/nmtui-configure-network-connection/)

#### 4.1) Install Bluetooth

I use Homeassistant to read bluetooth BLE temperature sensors using the 'HACS Passive BLE Monitor' integration using the phone's bluetooth capability.

Setting up the Bluetooth can be done later or skipped.

To set up bluetooth ;
```
    sudo apk add bluez
    sudo apk add dbus-broker
    sudo rc-update add bluetooth boot
    sudo service bluetooth start

    bluetoothctl power on
    bluetoothctl  scan le
    bluetoothctl
       scan on
```
This will add bluetooth software, set it to start after a reboot, and start a scan for bluetooth low energy ( le) devices. Exit to stop.

#### 4.2) Test Wifi

Stop the SSH session ( type exit) and disconnect the USB cable. From the PC, login using the wifi connection

    $ssh user@192.168.X.XXX:21        

where 192.168.X.XXX is the IP address of your phone device.

#### 4.3) Check reboot

Now is a good time to check everything properly restarts after a system start, so run

    sudo reboot

After the phone display comes back, login again and retest ( USB cable unplugged)

    $ssh user@192.168.X.XXX:21

Hopefully, no problems.....

You now have PostmarketOS on the phone .

## 5) Install Docker

Up to this point, this has been standard PostmarketOS installation.

PostmarketOS supports Docker, so this makes installing Home Assistant (and other Docker containers) straight forward.

In this short section, we install docker and docker-compose.

### 5.1) Install Docker

From now on, I assume you are connected by (wifi) SSH.

Following the instructions for installing Docker on [Alpine Linux](https://wiki.alpinelinux.org/wiki/Docker)

    sudo apk upgrade
    sudo apk update
    sudo apk add docker
    sudo addgroup bryan          
    sudo rc-update add docker default
    sudo service docker start
    sudo apk add docker-cli-compose
    sudo reboot
Obviously change 'bryan' for your user name...

### 5.2) Test Docker

To check docker, run:

    docker run hello-world

This should pull and run a very simple test that prints a message "Hello from Docker" and then exit.

### 5.3) Clean up

Assuming all is ok, run:

    docker system prune -a

This will clean up and remove all docker stuff that is not being used.

## 6 Firewall

By default, PostmarketOS uses [nftables](https://wiki.postmarketos.org/wiki/Firewall) with some default rules.
Unfortunately Docker doesn't support nftables and (still) uses iptables .... 

### 6.1 Get things working
To stop the nf filewall starting onboot
'''
$ sudo rc-update del nftables
$ sudo reboot
'''
(Note: 'sudo rc-update add nftables' will put them back again)

### 6.2 Install and configure IPtable
(TODO)         ( See-https://www.digitalocean.com/community/tutorials/iptables-essentials-common-firewall-rules-and-commands)

## 6 Home assistant

We'll now use docker-compose to install Home Assistant along with an MQTT broker.

The Home Assistant site gives instructions how to set up using docker-compose [here](https://www.home-assistant.io/installation/linux/#docker-compose).

In theory, you can follow those instructions. However, I think it is better to create storage volumes first because, if you let Docker make them, you will not have permissions to modify them outside of Home assistant.

### 6.1) Create storage volumes

So, to create the storage volumes on the PostmarkerOS phone I suggest:

    mkdir ~/volumes
    mkdir ~/volumes/hass
    mkdir ~/volumes/mosquitto
    mkdir ~/volumes/mosquitto/config
The mosquitto MQTT broker needs a config file, create it with :

    nano ~/volumes/mosquitto/config/mosquitto.conf

Set the configuration as:

    allow_anonymous true
    listener 1883 0.0.0.0
    # logging
    log_type none  
( Ctrl- O & Ctrl-X to save and exit)

### 6.3) docker-compose

The docker-compose.yml file groups all the docker commands needed to load the systems we want; currently HomeAssistant and MQTT.

Where you put this file isn't critical; to keep things simple I suggest you create it in the home directory (~).

Note: To create a text file, the Linux command line editors (eg nano) are ...what can I say..... geeky . You can use Nano that was installed during the earlier setup or install another editor. I personally use 'CX file explorer' on my normal android phone and connect via SFTP.

Whatever, create the file 'docker-compose.yml' and insert the following for homeassistant and mqtt services

    version: '3'
    services:
      homeassistant:
        container_name: homeassistant
        image: "ghcr.io/home-assistant/home-assistant:2022.7.1"
        volumes:
          - /home/bryan/volumes/hass:/config
          - /etc/localtime:/etc/localtime:ro
          - /run/dbus:/run/dbus:ro
          - /sys:/sys
        network_mode: host
        restart: unless-stopped
        depends_on:
          - mqtt

      mqtt:
        container_name: mqtt    
        image: "eclipse-mosquitto:1.6.13"
        volumes:
          - /home/bryan/volumes/mosquitto/config:/mosquitto/config
          - /home/bryan/volumes/mosquitto/log:/mosquitto/log
          - /home/bryan/volumes/mosquitto/data:/mosquitto/data
          - /etc/localtime:/etc/localtime:ro
        network_mode: host
        restart: unless-stopped
Obviously, change 'bryan' to your user name.

The versions (eg :1.6.13) are optional, but I like to 'freeze' the versions to avoid surprises

This contains the instructions to pull ( download) the standard docker 'image' to build your working 'container'. The volumes tell it where to store data and the network mode 'host' tells it not to isolate containers in a separate network as Docker would normally do.

### 6.4) Docker-compose up

When run for the first time, the following instruction will cause docker to pull (download) and extract the files. This is time consuming and needs the battery charged.

    sudo docker-compose pull

When everything finishes downloading, run

    sudo docker-compose  up -d 

This will start Home Assistant; the -d flag is detached mode which gives you back control of the console.

Wait a few minutes as things take a little time to set up the first time.

To access Home Assistant, from any machine on your local network, use an internet browser to connect to 192.168.X.XXX/8123.

You should have access to the normal home assistant log-in screen.

## 7) Wrap up

That's it. I hope you found this guide informative.

With docker, it's easy to set up other packages. The Tissot has 32GB of memory and it surprised me what can be done. My Home Assistant is minimal (mostly temperature sensors) and I have also running Wireguard VPN server Wordpress & maraidb web server Crowdsec security Swag/Letsencrypt remote access Jupyter Notebook server

Hope you have fun.......
