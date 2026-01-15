### Tailscale

Install Tailscale directly ( not docker)

```
sudo apk add tailscale

rc-update add tailscale
rc-service tailscale start

sudo reboot

sudo tailscale up
```