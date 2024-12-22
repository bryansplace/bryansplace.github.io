---
title: Home assistant docker backup
date: 2024-12-17
tags: hass
---

Home assistant has a backup service that creates a backup in the folder ```config/backups```. I can find no way to change the default location.

Solution was to
1) In docker compose volumes, map the home assistant /config/backups to my mounted Nas drive
```
    volumes:
      - /home/bryan/volumes/hass:/config
      - /home/bryan/data/nas/Bryan/hass_urville_backups:/config/backups
      - /etc/localtime:/etc/localtime:ro
      - /run/dbus:/run/dbus:ro
      - /var/run/dbus:/run/dbus:ro
 ```
 
 2) Automate a home ssistnt periodical backup
 ```
 automation:
  - alias: "Backup Home Assistant"
    trigger:
      - platform: time
        at: "03:00:00"
    action:
      - service: backup.create
        data: {}
  ```