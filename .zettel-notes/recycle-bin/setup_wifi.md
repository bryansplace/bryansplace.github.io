###  Setup WIFI

To automatically start network manager and wifi, set the date/time, run
```
    sudo rc-update add networkmanager
    sudo rc-update add wpa_supplicant
    sudo setup-ntp busybox
    sudo nmtui
 ```   
The nmtui interface gives a 'somewhat' intuitive screen where you can activate the WIFI. (Use a google search for 'nmtui' for instructions if needed).

You'll probably want to set up a static IP address for the phone on your router or preferably using nmtui. A static address can be set using nmtui explained [here](https://www.tecmint.com/nmtui-configure-network-connection/)