# Work in Progress!!!

```
services:
    llm:
        image: "ghcr.io/abetlen/llama-cpp-python:latest"
        container_name: llm
        network_mode: host
        ports:
            - 8000:8000
        volumes:
            - /home/diegoaguilera/llmModels:/models
        environment:
            - MODEL=/models/someModelName
        restart: unless-stopped
        privileged: true
```
