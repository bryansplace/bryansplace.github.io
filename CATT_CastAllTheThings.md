### Introduction
Immich is a popular selfhosted replacement for Goolge photos. 

Immich kiosk provides  highly configurable slideshows for displaying the Immich photos on browsers and devices.

This guide shows how to use CATT ( Cast All The Things) to chromecast Immich Kiosk.

### CATT

Cast All The Things allows you to render websites as explained [here](https://github.com/skorokithakis/catt)

Although it can be installed and run 'normally', I prefer to use docker containers to keep my host system 'clean'.

I'll presume you are fairly familar with Docker.

### Create  Dockerfile
  ```
 FROM python:3.11-slim
RUN pip install --no-cache-dir catt
ENTRYPOINT ["catt"]
CMD ["--help"]
 ```
 ### Build the Image
```docker build -t catt . ```
 
 ### Scan for your chromecast devices
  
  Run the catt container using command scan to list the chromecast devices on your LAN. 
  ```docker run --net=host --rm catt scan```
 Note:
   --net=host mode to fully connect to your LAN 
   --rm to remove container when finished
  
###  Cast Kiosk to a specific device

Use command cast_site and the -d option to cast your Immich Kiosk web site to a specific device.

As explained in the Kiosk documentation, URL queries can be used to customise the display.
  
As an specific example:
Suppose you have set up your Immich Kiosk on  host 192.168.68.111 port 3000.
To display images, with a duration of 15 seconds, using fade transition, of two specific people, and specific dates, the command would be:
```
docker run --net=host --rm catt -d 'Salon' cast_site 'http://192.168.68.111:3000?duration=15&transition=fade&person=25204c5c-e232-4488-b97c-c3f06f5cd195&person=25204c5c-e232-4488-b97c-c3f06f5cd195&date_filter=1982-01-01_to_2000-02-01'
```

