---
title: Nexus7 battery
date: 2023-06-15

layout: post


---

The Nexus 5 has a TI (Texas Instruments) bq24192 battery charger chip and battery charging can be disabled. 
To be gentle on the battery, when the phone starts, I
- set the charging to 'Trickle' ( which reduces to charge current)
- reduce to charging voltage from 4.3V to 3.6V
Also, I
-  Start with charging enabled
-  Disable a timer that turns off charging
-  Turn down the display brightness 

I also changed file permissions to enable Home assistant to even  switch voltage.

As the system alway boots with origional values, a startup script is needed to overwite the changes.

SSH into device if needed, then
```
cd /etc/local.d
sudo nano bq24190-charger.start
```
Then enter the following
```
#!/bin/ash
# Set battery charge voltage to 3.6V
chmod 777  /sys/class/power_supply/bq24190-charger/constant_charge_voltage
echo 3600000 > /sys/class/power_supply/bq24190-charger/constant_charge_voltage

#Start with Trickle charge 
chmod 777  /sys/class/power_supply/bq24190-charger/charge_type
echo Trickle > /sys/class/power_supply/bq24190-charger/charge_type

#Start with charging enabled
chmod 777  /sys/class/power_supply/bq24149-charger/f_chg_config
echo 1  /sys/class/power_supply/bq24149-charger/f_chg_config

#Disable Charging Safety Timer 
chmod 777  /sys/class/power_supply/bq24149-charger/f_en_timer
echo 0  /sys/class/power_supply/bq24149-charger/f_en_timer

#Turn down display brightness
echo 10 /sys/class/backlight/lcd-backlight/brightness
chmod 777 /sys/class/backlight/lcd-backlight/brightness

# INCREASE INPUT CURRENT LIMIT
echo 1000000 /sys/class/power_supply/bq24190-charger/input_current_limit
chmod 777 /sys/class/power_supply/bq24190-charger/input_current_limit
```
Exit nano saving the file  and make the script executable...
```
sudo chmod u+x /etc/local.d/bq24190-charger.start
```