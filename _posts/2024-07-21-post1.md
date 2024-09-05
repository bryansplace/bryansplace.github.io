---
title: "Photo and Video Server"
date: 2024-08-14
published: false
---

# Objective

To view photos and videos on the tv in a friendly way.

## Jellyfin

Lets take a look at Jellyfin, an open source alternative to Plex.

### Docker

Install as docker-compose on Alpinr server 222.
Image lscr.io/linuxserver/jellyfin:latest
```
services:
  jellyfin:
    image: lscr.io/linuxserver/jellyfin:latest
    container_name: jellyfin
    environment:
      - PUID=10000
      - PGID=10000
      - TZ=Europe/Paris
      - JELLYFIN_PublishedServerUrl=192.168.1.222 #Autodiscovery address
    volumes:
      - /path/to/library:/config
      - /home/bryan/projects/jellyfin/photos:/data/photos
      - /home/bryan/projects/jellyfin/movies:/data/movies
    ports:
      - 8096:8096 # Web gui
      - 8920:8920 #optional
      - 7359:7359/udp # Client discovery lanl
      - 1900:1900/udp # DNLA discovery
    restart: unless-stopped

```
### Summary

Seems to be too resource intensive. No configuration written and system crashed.

Abandond 
.

