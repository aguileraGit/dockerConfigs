# Home Assistant Docker

## Docker Compose
```
version: '3'
services:
    homeassistant:
        image: "ghcr.io/home-assistant/home-assistant:stable"
        container_name: homeassistant
        network_mode: host
        volumes:
            - /home/diegoaguilera/hassConfig:/config
            - /etc/localtime:/etc/localtime:ro
        restart: unless-stopped
        privileged: true
```

The *config* directory is mapped to the /home/diegoaguilera/hassConfig directory. This needs to be backup'ed.
