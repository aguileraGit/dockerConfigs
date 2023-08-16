# JupyterLab
Uses the standard (Jupyter Stacks)[https://jupyter-docker-stacks.readthedocs.io/en/latest/index.html].
Specifically uses the scipy-notebook which matches most of the packages I normally use.

Docker maps the volume to a the /home/diegoaguilera/Projects folder on the local system. This must be created.

It also uses the user diegoaguilera and GID=1003. This can be gotten from the /etc/password file.

Still need a way to save (rsync) Projects folder to external drive. 

To install user pip packages, two files are needed: Compose (yaml) and Dockerfile. Place both files in the same directory.

(docker-compose.yml)
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

Dockerfile:
```
FROM jupyter/scipy-notebook:latest

#Install pip packages
RUN pip install plotly jupyter_ai huggingface_hub pillow

#Install jupyter extensions
RUN jupyter labextension install @jupyterlab/toc
```
