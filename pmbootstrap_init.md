### Initialisation

The initialisation sets up PMbootstrap with you phone make and model together with any options, eg additional software.

Your device might have a pre-built image available. There is one for my Xiaomi Tissot, but I've never used it.

I want just a basic alpine server with the minimum of stuff installed. So we use the route using [Pmbootstrap init](https://wiki.postmarketos.org/wiki/Installation/Using_pmbootstrap#Initializing_pmbootstrap)   to tell pmbootstrap what device we have and what options we want.

On your PC, run 
```$pmbootstrap init```
 

This will give a series of options. Accept all defaults, except ( for the Xiaomi Tissot device) choose:

- Channel: Edge;
- Vendor:  qcom ;
- Device codename: msm8953
- User Interface: none

  As I just want a server, I don't want any user interface. Connection is through SSH.

- Additional packages : nano,networkmanager,networkmanager-wifi,networkmanager-cli,networkmanager-tui,wpa_supplicant

  Surprisingly, the qcom package doesn't have standard network manager. So its best to add them in here.

You'll be asked for a username . This will later be needed for login to the phone device (  thru SSH)

### Install

The command ```pmbootstrap install```