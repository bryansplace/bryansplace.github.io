---
title: Wol Cherbourg
date:2025-03-09
tags: hass infra
---

## Use Home Assitant for wol

Following the home assistant wake on lan [documentation]( https://www.home-assistant.io/integrations/wake_on_lan/) worked ok.

Cherbourg home assistant can turn on and off Cherbourg Desktop

The config is
```
wake_on_lan:

switch:
  - platform: wake_on_lan
    mac: fc:34:97:17:23:xx
    name: Desktop
    host: '192.168.68.xxx'
    turn_off:
      action: shell_command.turn_off_desktop

shell_command:
  turn_off_desktop: "ssh hass@192.168.68.xxx sudo poweroff"
  ```


