# JupyterLab
Uses the standard (Jupyter Stacks)[https://jupyter-docker-stacks.readthedocs.io/en/latest/index.html].
Specifically uses the scipy-notebook which matches most of the packages I normally use.

## Portainer
To be able to add custom Pip packages, a Dockerfile needs to be created. Because the Dockerfile needs the network arguement, the flow to build this is more complicated.
- Create jupyterlab directory in /opt directory
- Copy docker-compose.yml to directory
- Copy Dockerfile to directory
- (Unsure if needed) Build the Dockerfile.
- Build the container. In theory, this calls the dockerfile. However, I don't know how I would pass the network arguement.

## Comments/Hints
Docker maps the volume to a the /home/diegoaguilera/Projects folder on the local system. This must be created.

It also uses the user diegoaguilera and GID=1003. This can be gotten from the /etc/password file.

To install user pip packages, two files are needed: Compose (yaml) and Dockerfile. Place both files in the same directory.

## Back ups (Still needs to be done).
Still need a way to save (rsync) Projects folder to external drive. This should be broken down into 2 steps.
1. Write udev rule to map external drive.
2. Create cron job to sync folder

## docker-compose.yml
```
version: '3.3'
services:
    jupyter-notebook:
	build:
	    context: .
	    dockerfile: Dockerfile
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
        #image: jupyter/scipy-notebook:latest
        network_mode: host
        restart: unless-stopped
```

## Dockerfile
```
FROM jupyter/scipy-notebook:latest

#Install pip packages
RUN pip install plotly jupyter_ai huggingface_hub pillow

#Install jupyter extensions - Being deprecated. Should be installed with pip in the future.
#RUN jupyter labextension install @jupyterlab/toc
```
## Build Dockerfile
**May not need to build. Maybe compose up will build?!**
Build the Dockerfile first. I had to add --network=host. I believe I have some setting that isn't correct in Docker. Once a container is built, it doesn't have access to the internet unless I tell it to use the host network.
```sudo docker build - < Dockerfile --network=host```

## Build Container
Create container using ```docker compose up```.
