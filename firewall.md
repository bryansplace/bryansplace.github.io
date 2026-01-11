### Firewall

PostmarketOS now includes a nftables firewall. 

For testing, 
```
# Stop the firewall
sudo rc-service nftables stop
# Check if it's stopped
sudo rc-status | grep nftables
# To start it again:
sudo rc-service nftables start
```

As I use the device as a home lab, lots of different ports might be used, so I want the firewall to allow all traffic from my home lan using wlan0