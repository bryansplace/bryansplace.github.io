### Bluetooth

To set up bluetooth ;

    sudo apk add bluez
    sudo rc-update add bluetooth default
    sudo service bluetooth start

    bluetoothctl power on
    bluetoothctl  scan le
    bluetooth
       scan on
This will add bluetooth software, set it to start after a reboot, and start a scan for bluetooth low energy ( le) devices. Exit to stop.
