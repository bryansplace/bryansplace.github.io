### Initialization

Your device might have a pre-built image available. There is one for my Xiaomi Tissot, but I've never used it.

I want just a basic alpine server with the minimum of stuff installed. So we use the route using ```Pmbootstrap init``` to tell pmbootstrap what device we have and what options we want.

https://wiki.postmarketos.org/wiki/Installation/Using_pmbootstrap#Initializing_pmbootstrap

  https://wiki.postmarketos.org/wiki/Installation/Using_pmbootstrap#Installing_pmbootstrap

On your PC, run

 
$pmbootstrap init
 

This will give a series of options. Accept all defaults, except ( for the Tissot device) choose:

Channel: Edge;

Vendor: Xiaomi ;

Device codename: Tissot

User Interface:. none Additional packages : nano,networkmanager,networkmanager-wifi,networkmanager-cli,networkmanager-tui,wpa_supplicant

You'll be asked for a username . This will later be needed for login to the phone device ( both initial Screen and later SSH)