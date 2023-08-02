# Docker setup
This is a guide Franklin gave me. It really helped getting me going.

## Notes
- In the future, I would look at Docker documentation for installing the GPG key and Repo.
- Docker Engine and Docker Compose are installed first. Then Portainer. The containers.
- The Docker Compose file has portainter and pihole in one file. This is a stack in portainer.
- Everything is really done in the /opt directory.
- Targets a RPi. If not using the pi user, be sure to give privileges to the user.

```
1. Set static IP
vi ~/etc/dhcpcd.conf

 

2. Reboot
sudo reboot

 

3. Update
sudo apt update && sudo apt upgrade -y

 

4. Install vim
sudo apt install vim

 

5. Install Docker Engine

 

5.1 Set up the repository. Update the apt package index and install packages to allow apt to use a repository over HTTPS:

 




sudo apt-get update
sudo apt-get install ca-ce




 

 

 

 

5.2 Add Docker’s official GPG key

 




sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/raspbian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg




 

 

 

 

5.3 Use the following command to set up the repository

 




echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/raspbian \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null




 

 

 

 

5.4 Install Docker Engine, containerd, and Docker Compose.

 




sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin




 

 

 

 

5.5 Verify that the Docker Engine installation is successful by running the hello-world image

 




sudo docker run hello-world
This command downloads a test image and runs it in a container. When the container runs, it prints a confirmation message and exits.




 

 

 

 

6. Set the current user (in my case "franklin") in the group "docker"
sudo usermod -aG docker $USER

 

7. Logout to update privileges 
logout

 

8. Verify current user is in the groups. you should see "docker" in the list
groups

 

9. Go to the /opt/ directory
cd ~/opt/

 

10. Make sure current user has privileges to create a "docker-compose.yaml" file. Currently, mine was set where only "root" had permissions.
sudo chown franklin:franklin .
sudo chown franklin:root containerd/

 

11. Create your docker compose file
vi docker-compose.yaml

 

12. Paste this in the file

 




services:
  portainer:
   container_name: portainer
   image: portainer/portainer-ce:latest
   ports:
     - 9000:9000 # Web Interface
   volumes:
     - /opt/portainer:/data
     - /var/run/docker.sock:/var/run/docker.sock:ro
   restart: unless-stopped

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




 

 

12. Make directories for the containers you want to run
mkdir portainer
mkdir pihole

 

13. Create an admin_pw.txt file to hold your Pihole's login. HEADS UP! I figured out you can't do this for the first time running pihole, so when you are configuring your "docker-compose.yaml" file, do not have the "WEBPASSWORD_FILE" env variable set. Instead, set your pw directly in the .yaml file.
cd ~/opt/pihole
mkdir secrets
vi admin_pw.txt

 

14. Run Docker.
docker compose pull
docker compose up -d

 

15. Make sure docker service will start if the RPi is every rebooted

sudo systemctl enable docker

 

16. Reboot

sudo reboot
```
