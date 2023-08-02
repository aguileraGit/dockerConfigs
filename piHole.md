# PiHole Docker

I lost the docker file currently running. Pasting below an older file from Franklin.

I believe the only addition to this file is the WEBPASSWORD. I couldn't get the PASSWORD_FILE variable to work.
```WEBPASSWORD = p!H0le```
I know it's bad practice to expose passwords, but exposure/risk for this is low/none and I don't care.

```
services:
  pihole:
   container_name: pihole
   image: pihole/pihole:latest
   ports:
     - 53:53/tcp # DNS
     - 53:53/udp # DNS
   environment:
     TZ: American/New_York
     WEBPASSWORD:
     WEBPASSWORD_FILE: /opt/pihole/secrets/admin_pw.txt
     FTLCONF_BLOCK_ICLOUD_PR: false
   volumes:
     - /opt/pihole/pihole:/etc/pihole
     - /opt/pihole/dnsmasq:/etc/dnsmasq.d
   restart: unless-stopped
```
