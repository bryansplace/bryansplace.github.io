### Introduction

Phones often have the option to limit the maximum battery charge.

Below, the steps are given to implement this in PostmarketOS. 

The script given simply limits the input current to a low charge if the battery charge is high, switching back to normal charge when necessary.

My device is a  Xiaom A1 tissot ( a generic qcom-msm8953). Details for other devices may vary a little.

### Explaination

The parameters that govern and report the battery and charging are to be found in ```/sys/class/power_supply```. 
There are two sub-directories; one for the battery and one for the charger, in my case qcom-battery   and qcom-smbchg-usb.

Listing the files in these directories gives a somewhat bewildering list. What everything means is documented in https://docs.kernel.org/power/power_supply_class.html, but manufacturers have not strictly followed this.

For the qcom-msm8953, the important items are

```/sys/class/power_supply/qcom-battery/capacity``` which reports the current  charge, eg 80% capacity.

```/sys/class/power_supply/qcom-battery/constant_charge_current_max``` which gives the maximum charge current the battery should take. Make a note of this value 

```/sys/class/power_supply/qcom-smbchg-usb/input_current_limit``` which is read/write and reports/sets the  current limit the charger can send to the battery. It is this value that is reduced to a 'minimal rate' when the desired charge level is reached. 

### Step 1: Create the main script

This script reduces current to a 'trickle' when charge % is over max required and sets it back to normal when the charge is low.

```sudo nano /usr/local/bin/battery-charge-limiter.sh```
```
#!/bin/sh
#/usr/local/bin/battery-charge-limiter.sh

MAX_CAPACITY=60
MIN_CAPACITY=55  # Resume charging
CHECK_INTERVAL=60 # Seconds

CURRENT_LIMIT_PATH="/sys/class/power_supply/qcom-smbchg-usb/input_current_limit"
CAPACITY_PATH="/sys/class/power_supply/qcom-battery/capacity"

NORMAL_CURRENT=1000000  # Adjust based on maximum of your device
TRICKLE_CURRENT=10000

while true; do
    CAPACITY=$(cat "$CAPACITY_PATH")
    
    if [ "$CAPACITY" -ge "$MAX_CAPACITY" ]; then
        echo "$TRICKLE_CURRENT" > "$CURRENT_LIMIT_PATH"
    elif [ "$CAPACITY" -le "$MIN_CAPACITY" ]; then
        echo "$NORMAL_CURRENT" > "$CURRENT_LIMIT_PATH"
    fi
    
    sleep $CHECK_INTERVAL
done
```
Make it executable,
```
sudo chmod +x /usr/local/bin/battery-charge-limiter.sh
```

### Step 2: Create the OpenRC init script

Assuming OpenRC is used
```sudo nano /etc/init.d/battery-charge-limiter```

```
#!/sbin/openrc-run
#/etc/init.d/battery-charge-limiter

name="battery-charge-limiter"
description="Battery Charge Limiter Service"
command="/usr/local/bin/battery-charge-limiter.sh"
command_background=true
pidfile="/run/${RC_SVCNAME}.pid"

depend() {
    need localmount
    after *
}

start_pre() {
    # Check if the required sysfs paths exist
    if [ ! -f /sys/class/power_supply/qcom-smbchg-usb/input_current_limit ]; then
        eerror "Required sysfs path not found"
        return 1
    fi
}
```
Make it executable
```sudo chmod +x /etc/init.d/battery-charge-limiter```

### Step 3: Enable and start the service

Add it to default runlevel (starts at boot)
```sudo rc-update add battery-charge-limiter default```

Start the service now
```sudo rc-service battery-charge-limiter start
```
Check its status is running
```sudo rc-service battery-charge-limiter status```

### Step 4: Verify it's working
Check if the process is running
```ps aux | grep battery-charge-limiter```

Monitor the current limit being applied
```watch -n 5 'cat /sys/class/power_supply/qcom-battery/capacity; cat /sys/class/power_supply/qcom-smbchg-usb/input_current_limit'```

### Management Commands
Start service
```sudo rc-service battery-charge-limiter start```

Stop service
```sudo rc-service battery-charge-limiter stop```

Restart service
```sudo rc-service battery-charge-limiter restart```

Remove from autostart
```sudo rc-update del battery-charge-limiter default```


Once running, you may want to adjust:
NORMAL_CURRENT - Check what your device uses normally:
cat /sys/class/power_supply/qcom-smbchg-usb/input_current_limit
MIN_CAPACITY - Set the hysteresis (how much below 60% before resuming normal charging)
CHECK_INTERVAL - How often to check (60 seconds is reasonable)
That should give you a proper OpenRC-based battery charge limiter for postmarketOS