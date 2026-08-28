### Bluetooth

Bluetooth can be used which is especially usefull in home assistant.

But....I always have problems with it....and end up getting it working, but loose track of exactly what I did.

To set up bluetooth, this 'should' work ;

    sudo apk add bluez
    sudo rc-update add bluetooth default
    sudo service bluetooth start

    bluetoothctl power on
    bluetoothctl  scan le
    bluetooth
       scan on

This  adds bluetooth software, sets it to start after a reboot, and start a scan for bluetooth low energy ( ble) devices. 
'Exit' to stop.

Home assistant tries to detect a USB adapter and gives an error with the phones bluetooth device. It is annoying but bluetooth still works.

When using docker, the dbus volume needs to be mapped into the container:
```    
    volumes:
      ...
      ....
       - /run/dbus:/run/dbus:ro
```




