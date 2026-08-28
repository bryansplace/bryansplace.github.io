### Firewall

PostmarketOS now uses a nftables firewall. 

*** This is work in progress ***

The default rules are explained [here](https://wiki.postmarketos.org/wiki/Firewall).

Installed packages can add, and even, modify rules. eg Tailscale will modify rules to try to ensure its rules have priority.

Also, Docker uses iptables, not nftables.
docker info | grep "Firewall"
 Firewall Backend: iptables
 
 sudo nano  /etc/conf.d/docker
 add DOCKER_OPTS="--firewall-backend=nftables"
 restart docker
 rc-service docker restart
 
  docker info | grep "Firewall"         
  Firewall Backend: nftables
 
 
 
  

For testing, 
```
# Stop the firewall
sudo rc-service nftables stop
# Check if it's stopped
sudo rc-status | grep nftables
# Remove firewall from reboot startup
sudo rc-update del nftables

# To start it again:
sudo rc-service nftables start
# Add firewall to reboot startup
sudo rc-update add nftables default

```

As I use the device as a home lab, lots of different ports might be used, I want the firewall to allow all traffic from my home lan using wlan0

To create a rule file to allow all traffic on interface wlan0. Create a new file in /etc/nftables.d/:
```
sudo mkdir -p /etc/nftables.d
sudo nano /etc/nftables.d/homeassistant.nft
```
```
#!/usr/sbin/nft -f

# Allow all traffic from home LAN on wlan0
table inet filter {
    chain input {
        iifname wlan0 accept comment "Allow all traffic from home LAN (wlan0)"
    }
}
```


Save and exit, then reload the firewall:
```sudo rc-service nftables restart```
