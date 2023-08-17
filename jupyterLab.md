# JupyterLab
Uses the standard [Jupyter Stacks](https://jupyter-docker-stacks.readthedocs.io/en/latest/index.html).
Specifically uses the scipy-notebook which matches most of the packages I normally use. Additionally, pip packages can be specified to be installed after jupyterlab is installed.

## Installation flow
To be able to add custom Pip packages, a Dockerfile needs to be created. Because the Dockerfile needs the network arguement, the flow to build this is more complicated.
- Create jupyterlab directory in /opt directory
- Copy Dockerfile to directory (add/remove pip packages as needed)
- Build the Dockerfile. (See instructions below)
- Build the container using Portainer Stack using the docker-compose.yml file. The image name is specified and must match the -t arguement in docker build.

## Comments/Hints
Docker-compose maps the volume to a the /home/diegoaguilera/Projects folder on the local system. This must be created beforehand (I think).

It also uses the user diegoaguilera and GID=1003. This can be gotten from the /etc/password file. All files created in Jupyter will appear as if created by diegoaguilera and group.

Additional pip packages can be added to the Dockerfile.

## Dockerfile
```
FROM jupyter/scipy-notebook:latest

#Install pip packages
RUN pip install plotly jupyter_ai huggingface_hub pillow

#Install jupyter extensions - Being deprecated. Should be installed with pip in the future.
#RUN jupyter labextension install @jupyterlab/toc
```
## Build Dockerfile
Build the Dockerfile first. I had to add --network=host. I believe I have some setting that isn't correct in Docker. Once a container is built, it doesn't have access to the internet unless I tell it to use the host network.
The -t arguement sets the name. It must match the docker-compose.yml file. After running this, the image shows up in Portainer (under images).	
```sudo docker build - < Dockerfile --network=host -t jupyterlabpluspipextras```

## Build Container
Use Portainer Stacks. Copy docker-compose.yml. No need to pull as you've built it in the step above.

## docker-compose.yml
```
version: '3.3'
services:
  jupyter-notebook:
    build: .
    image: jupyterlabpluspipextras
    ports:
      - '8888:8888'
    user: root
    environment:
      - NB_USER=diegoaguilera
      - NB_GID=1003
      - CHOWN_HOME=yes
      - RESTARTABLE=yes
    volumes:
      - '/home/diegoaguilera/Projects:/home/diegoaguilera'
    network_mode: host
    restart: unless-stopped
```
## Back ups (Still needs to be done).
This is a multistep process.
- Prerequs
- Create script to mount drive
- Create udev rule to trigger script
- Create script to back up folder to drive
- Create cron job

### Prerequisits
- If using VM, ensure rule is configured to attach HD
- Use udev rule to consistently indentify drive when plugged in and/or rebooting
  - Many ways to do this. One way is to plug in the HD and run ```udevadm info -a -n /dev/sdb | less```
  - Grab one or more ATTR to identify the device. Example: ATTRS{product}=="FreeAgent Go" or ATTRS{serial}=="2GE798MA"
- Probably need to create the mount point in the script below.

### Mount Script
Save script in ```/usr/local/bin/mount_backup.sh```
Script:
```
#!/bin/bash

# Set your mount point
MOUNT_POINT="/mnt/backup"

# Mount the drive
mount /dev/sdb1 $MOUNT_POINT

# If needed, you might also want to specify a particular filesystem type
# mount -t ext4 /dev/sdb1 $MOUNT_POINT
```
Make it executable: ```chmod +x /usr/local/bin/mount_backup.sh```
 
### udev rules
- Open or create /etc/udev/rules.d/99-mount-backup.rules
- Add line: ACTION=="add", ATTRS{serial}=="2GE798MA, RUN+="/usr/local/bin/mount_backup.sh"

### Reload udev rules
udevadm control --reload-rules

### Backup Script
See: https://smarttech101.com/backup-and-restore-your-computer-using-rsync/ for some good settings

### Cron Job


