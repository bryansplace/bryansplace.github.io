### Tailscale

Install Tailscale directly ( not docker)

Install Tailscale (on Alpine Linux)
```
# apk add tailscale
```
Use OpenRC to enable and start the service
```
# rc-update add tailscale
# rc-service tailscale start
```
Authenticate and connect your machine to your Tailscale network
```
tailscale up
```
Connected! 

You can find theTailscale IPv4 address by running:
```
tailscale ip -4

```
