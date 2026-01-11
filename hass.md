### Home assistant

Use docker-compose to install Home Assistant along with an MQTT broker.

The Home Assistant site gives instructions how to set up using docker-compose [here](https://www.home-assistant.io/installation/linux/#docker-compose).

In theory, you can follow those instructions. However, I think it is better to create storage volumes first because, if you let Docker make them, you will not have permissions to modify them outside of Home assistant.

### 6.1) Create storage volumes

So, to create the storage volumes on the PostmarkerOS phone I suggest:

    mkdir ~/volumes
    mkdir ~/volumes/hass
    mkdir ~/volumes/mosquitto
    mkdir ~/volumes/mosquitto/config
The mosquitto MQTT broker needs a config file, create it with :

    nano ~/volumes/mosquitto/config/mosquitto.conf

Set the configuration as:

    allow_anonymous true
    listener 1883 0.0.0.0
    # logging
    log_type none  
( Ctrl- O & Ctrl-X to save and exit)

### 6.3) docker-compose

The docker-compose.yml file groups all the docker commands needed to load the systems we want; currently HomeAssistant and MQTT.

Where you put this file isn't critical; to keep things simple I suggest you create it in the home directory (~).

Note: To create a text file, the Linux command line editors (eg nano) are ...what can I say..... geeky . You can use Nano that was installed during the earlier setup or install another editor. I personally use 'CX file explorer' on my normal android phone and connect via SFTP.

Whatever, create the file 'docker-compose.yml' and insert the following for homeassistant and mqtt services

    version: '3'
    services:
      homeassistant:
        container_name: homeassistant
        image: "ghcr.io/home-assistant/home-assistant:2022.7.1"
        volumes:
          - /home/bryan/volumes/hass:/config
          - /etc/localtime:/etc/localtime:ro
          - /run/dbus:/run/dbus:ro
          - /sys:/sys
        network_mode: host
        restart: unless-stopped
        depends_on:
          - mqtt

      mqtt:
        container_name: mqtt    
        image: "eclipse-mosquitto:1.6.13"
        volumes:
          - /home/bryan/volumes/mosquitto/config:/mosquitto/config
          - /home/bryan/volumes/mosquitto/log:/mosquitto/log
          - /home/bryan/volumes/mosquitto/data:/mosquitto/data
          - /etc/localtime:/etc/localtime:ro
        network_mode: host
        restart: unless-stopped
Obviously, change 'bryan' to your user name.

The versions (eg :1.6.13) are optional, but I like to 'freeze' the versions to avoid surprises

This contains the instructions to pull ( download) the standard docker 'image' to build your working 'container'. The volumes tell it where to store data and the network mode 'host' tells it not to isolate containers in a separate network as Docker would normally do.

### 6.4) Docker-compose up

When run for the first time, the following instruction will cause docker to pull (download) and extract the files. This is time consuming and needs the battery charged.

    sudo docker-compose pull

When everything finishes downloading, run

    sudo docker-compose  up -d 

This will start Home Assistant; the -d flag is detached mode which gives you back control of the console.

Wait a few minutes as things take a little time to set up the first time.

To access Home Assistant, from any machine on your local network, use an internet browser to connect to 192.168.X.XXX/8123.

You should have access to the normal home assistant log-in screen.