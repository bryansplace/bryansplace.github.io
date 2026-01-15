## Reduce Battery Voltage

When using an old smartphone as a permanent server that's always plugged into power, the battery is a  point of concern. Batteries have been known to degrade and even catch fire.

Some people propose removing the battery but this is not simple.  The battery is often checked by software as it is required to start the device.

Some people propose using a remote control wifi power switch.

I decided just to keep it at a reduced battery  voltage and .  This is better( than repeated charge/discharge. 

Unfortunately, the battery control is very device dependant, devices have different hardware (PMICs, power management integrated cicuits) which require different software. 

I have exoeriences with an old Nexus 7 and  a less old Xiaomi A1.

The Nexus 7 charger software actually allowed me to change the maximum battery voltage.

The newer Xiaomi ( which uses  the generic qcom-msm8953 firmware) does not. But I show below how to change the 'device tree' to change the maximum battery voltage. This is not difficult but probably more risky if not done correctly ( but not fatal, go back to Fastboot and reflash).

[If your lucky](#if-your-lucky)
[Modify device tree](#modify-device-tree)

Note: You might like to point your trusty AI friend to this page to get specific advice for your device.



### If your lucky

Look into the files in /sys/class/power_supply.

For my old Nexus 5...

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


### Modify device tree

For my Xiaomi A1 tissot, this guide modifies the device tree to lower the maximum charging voltage from 4.4V (100% charge) to 3.8V (~40-50% charge), which is much healthier for a battery that's permanently connected to power.

### Prerequisites
- PostmarketOS installed on your device
- SSH or terminal access to the device
- Root/sudo/doas access

### Steps

### 1. Install Device Tree Compiler

```bash
sudo apk add dtc
```

### 2. Backup Original Device Tree

```bash
# Replace 'tissot' with your device name from /boot/*.dtb
sudo cp /boot/msm8953-xiaomi-tissot.dtb /boot/msm8953-xiaomi-tissot.dtb.backup
```

### 3. Decompile Device Tree

```bash
dtc -I dtb -O dts /boot/msm8953-xiaomi-tissot.dtb -o ~/tissot.dts
```

Note: Warnings during decompilation are normal and can be ignored.

### 4. Find Current Battery Voltage Setting

```bash
grep -n "voltage-max-design-microvolt" ~/tissot.dts
```

This will show the line number containing the voltage setting.

### 5. Modify the Voltage

Change from 4.4V (0x432380) to 3.8V (0x39f880)

IMPORTANT: Adjust line number (3959) based on your grep output
```
sed -i '3959s/0x432380/0x39f880/' ~/tissot.dts
```

**Voltage recommendations:**
- 3.8V (0x39f880) - ~40-50% charge, best for permanent server use
- 4.0V (0x3d0900) - ~60% charge
- 4.1V (0x3e9380) - ~80% charge

**Converting voltage to hex:**

The voltage is specified in microvolts (μV). To convert:
1. Multiply volts by 1,000,000 (e.g., 3.8V = 3,800,000 μV)
2. Convert to hexadecimal using a command or calculator
3. Use the hex value (e.g., 0x39f880) in the device tree

Example conversion command:

```bash
printf "0x%x\n" 3800000
```

### 6. Verify the Change

```
grep -n -A 2 -B 2 "voltage-max-design-microvolt" ~/tissot.dts
```

Confirm the hex value has changed to your target voltage.

### 7. Recompile Device Tree

```
dtc -I dts -O dtb ~/tissot.dts -o ~/tissot-modified.dtb
```

Note: Warnings during compilation are normal and can be ignored.

### 8. Install Modified Device Tree

```bash
sudo cp ~/tissot-modified.dtb /boot/msm8953-xiaomi-tissot.dtb
```

### 9. Reboot

```
sudo reboot
```

### 10. Verify the Change

After reboot, check that the new voltage limit is active:

```
cat /sys/class/power_supply/qcom-battery/voltage_max_design
cat /sys/class/power_supply/qcom-battery/voltage_now
```

The `voltage_max_design` should now show 3800000 (3.8V) instead of 4400000 (4.4V).

### Reverting Changes

If you need to restore the original settings:

```bash
sudo cp /boot/msm8953-xiaomi-tissot.dtb.backup /boot/msm8953-xiaomi-tissot.dtb
sudo reboot
```

I don't know why, but after the reboot, the USB cable needs to be physically unplugged and reconnected. Otherwise, it is left in a discharging state.

To avoid this, create a startup script to do this automatically after each reboot:
```
# Create the startup script
sudo mkdir -p /etc/local.d
sudo nano /etc/local.d/reset-charging.start
```
Add this content:
```
#!/bin/sh
# Reset USB charging after boot
# Wait for system to fully initialize
sleep 10

# Toggle USB online to reset charger state
echo 0 > /sys/class/power_supply/qcom-smbchg-usb/online
sleep 2
echo 1 > /sys/class/power_supply/qcom-smbchg-usb/online

logger "USB charging reset completed"
```

Make it executable and enable the local service:
```
sudo chmod +x /etc/local.d/reset-charging.start
sudo rc-update add local default
```
Now test it:
```
# Reboot and see if it automatically starts charging
sudo reboot
```
After reboot, wait about 15 seconds, then check:
```
cat /sys/class/power_supply/qcom-smbchg-usb/status
cat /sys/class/power_supply/qcom-battery/status
```
Both should show "Charging" (or "Not charging" if already at 3.8V).


#### Notes 
- The minimum safe voltage is 3.4V - never go below this
- Lower voltages extend battery life but reduce available capacity
- For permanent server use, 3.8V is a good balance
- The change persists across reboots and system updates (unless the kernel/dtb is replaced)