
To connect the phone, four things need to be done; 
enable USB debugging on the phone, 
use a good USB cable, 
put the phone into flash mode and 
unlock the phone.

#### USB debugging.

In order to use a USB cable to communicate between the Android phone and the computer, something called 'USB debugging' needs to be enabled on the phone.

A Google search ( eg ' Xiaomi A1 enable USB debugging' ) will give lots of instructions and videos if needed.

The procedure is generally:
Go to the phone Settings -> About -> Tap build number repeatedly until developer menu is enabled. Then tap on “ developer options ” and tick USB debugging.

####  USB cable

I used to think all USB cables were more or less the same. But experience taught me that they are finiky and a source of problems. A cable might work sometimes, but not other times. If things in the subsequent steps don't go right; try another cable and/or USB port.


#### Flash mode

Thr phone needs to be put into flash mode.

Generally, the PMOS device page will tell you how to do this, eg 
<https://wiki.postmarketos.org/wiki/Xiaomi_Mi_A1_(xiaomi-tissot)#How_to_enter_flash_mode>
 ```
Power on the device with both Power and Volume Down buttons down
```

####  OEM unlock

Plug in the USB cable between PC and phone, then on the PC run:

    $ sudo fastboot oem unlock

When prompted on the the phone device, use Volume Up/Down buttons to highlight 'Yes' choice, then press the Power button to select it.

