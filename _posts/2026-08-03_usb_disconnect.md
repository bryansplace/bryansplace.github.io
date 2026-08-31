---
title: Disconnect Power
date: 2026-04-11
published: true
layout: post
---

If you are running an old phone as a homelab, leaving it permanently on USB charge  might concern you.

I detail the best way to reduce the battery voltage  [here]( https://github.com/bryansplace/PostmarketOS-limit-battery-voltage/blob/main/README.md)

The method described there might initimidate some as it is a bit advanced.

Some people might be tempted to use a wifi switch to switch off the charge when a certain battery voltage is reached.

Personally, I don't like that. The internal battery charger chip is very good at controlling the voltage. But doing it externally continually cycles the charging as the battery doesn't reach a stable charge. That's why I investigated reducing the design voltage so the internal chip could do its work.

But for the record..... 

In fact, an external switch is not required as the charging can be controlled directly in the system.

To report charging or discharging status
```
$cat /sys/class/power_supply/qcom-smbchg-usb/status
```

To disconnect the charging
```
$echo 0 | sudo tee /sys/class/power_supply/qcom-smbchg-usb/online
```
To reconnect
```
$echo 1 | sudo tee /sys/class/power_
supply/qcom-smbchg-usb/online
```