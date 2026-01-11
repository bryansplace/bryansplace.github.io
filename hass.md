### Home assistant

The Home Assistant site gives instructions how to set up using docker-compose;  <https://www.home-assistant.io/installation/linux/#docker-compose>.

I'll not confuse things by repeating that here.

I think key points to note are:
1) For docker to 'see' storage outside its containers, 'volumes' need to be mapped.  
2) Home Assistant Container installations don’t have access to add-ons. For example, MQTT.
3) The container must use 'host' mode to access your lan. The ports mentioned in older documentation is now not allowed when usin network mode hosts.
  theory, you can follow those instructions. However, I think it is better to create storage volumes first because, if you let Docker make them, you will not have permissions to modify them outside of Home assistant.
  
 For info, here is my docker-compose.yaml
 ```
services:
  homeassistant:
    container_name: homeassistant
    image: "ghcr.io/home-assistant/home-assistant:2026.1.0"
    volumes:
      - /home/bryan/hass/volumes/hass_config:/config
      - /etc/localtime:/etc/localtime:ro
      - /run/dbus:/run/dbus:ro
      - /var/run/dbus:/run/dbus:ro
    network_mode: host
    privileged: true
    restart: unless-stopped
    depends_on:
      - mqtt

  mqtt:
    container_name: mqtt    
    image: "eclipse-mosquitto:1.6.13"
    volumes:
      - /home/bryan/hass/volumes/mosquitto/config:/mosquitto/config
      - /home/bryan/hass/volumes/mosquitto/log:/mosquitto/log
      - /home/bryan/hass.volumes/mosquitto/data:/mosquitto/data
      - /etc/localtime:/etc/localtime:ro
    network_mode: host
    restart: unless-stopped
 ```

Note: The mosquitto MQTT broker needs a config file, create it with :

    nano ~/volumes/mosquitto/config/mosquitto.conf

Set the configuration as:

    allow_anonymous true
    listener 1883 0.0.0.0
    # logging
    log_type none  
( Ctrl- O & Ctrl-X to save and exit)
