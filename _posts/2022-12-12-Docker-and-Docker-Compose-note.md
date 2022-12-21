---
title: "Docker & Docker Compose notes"
tags:
  - Docker
  - Docker Compose
---

### prevent a ubuntu container in Docker Compose from stop
[Running a ubuntu container in background using docker compose](https://stackoverflow.com/questions/60260437/running-a-ubuntu-container-in-background-using-docker-compose)  
```shell
# So if you want to keep your ubuntu container alive and don't want to attach it to a terminal, you'll have to use a process that will stay alive for as long as you want.
# Below is an example with sleep infinity as your main process

version: "3"
services:
  ubuntu:
    container_name: ubuntu
    image: ubuntu
    restart: on-failure
    command: ["sleep","infinity"]
```

### learning Docker Compose
After gaining an understanding of some basic concepts, I realize that different Compose file version has different feature, so I tend to start from latest version as [`Compose file format 3.9`](https://docs.docker.com/compose/compose-file/compose-file-v3/) at 2022.12.13.   
