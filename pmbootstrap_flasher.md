### Flash to phone

The install will have created two files that need to be flashed to the phone; lk2nd ( a qualcom specific secondary bootloader) and rootfs
( the root file system)

The Xiaomi A1 tissot phone actually has what they call an A/B structure.  I won't explain [it](https://wiki.postmarketos.org/wiki/Android_AB_Slots) here, but I want to be sure it always boots into the new system, so I choose to flash it to both A and B slots

Ensure device is still in fastboot mode, and plugged in via USB. Then, on the PC, run:
```
    $ pmbootstrap flasher flash_lk2nd --partition boot_a
    $ pmbootstrap flasher flash_lk2nd --partition boot_b
    $ pmbootstrap flasher flash_rootfs --partition userdata
    $ fastboot reboot 
```


Wait a while (a couple of minutes) as the phone reboots PostmarketOS. 

As the display option was none, there is little indication that things have succeded


### Login thru SSH

With the USB cable still connected, from your PC terminal ; with your user name run
```
    $ssh user@172.16.42.1  
```
You will be asked for your password.

Hopefully, you now have control of the phone from your computer.