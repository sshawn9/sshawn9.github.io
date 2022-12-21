---
title: "SSH & GUI with Docker for remote development"
tags:
  - SSH
  - GUI
  - Docker
---

## Preface
Docker is amazing.  
This page gives a brief description on setting ssh in docker container for remote development and running gui apps in containers.

## SSH into Docker Container for Remote Development
Just install openssh, config `sshd_config` and set envrionment variables, I did these works by running a [script](https://github.com/sshawn9/my_scripts/blob/master/utilities/ssh_for_remote_dev.sh).  
You may add this script to your dockerfile to produce image equipped with ssh service, for example:
```dockerfile
FROM ubuntu:18.04
COPY ssh_for_remote_dev.sh /
RUN bash ssh_for_remote_dev.sh
...
```
Also, you may want to check my own dockerfile as [here](https://github.com/sshawn9/my_scripts/blob/master/docker/dockerfile/bionic_remote_env.Dockerfile).  
**Notice:** to make envrionment variables effective even if the `bash` is not running interactively, I add `. ~/.bash_env` in front of `.bashrc`, and I create a `.bash_env` file under `$HOME`. Just put your perosonal envrionment variables settings in `.bash_env` file.  
For more infomation, here are some reference:
- <https://github.com/linuxserver/docker-openssh-server>
- <https://github.com/JetBrains/clion-remote>
- <https://www.jetbrains.com/help/clion/remote-projects-support.html>

## running GUI apps in container(macOS)
Actually, this way could be used widely only if your have browser and docker.  
This is also quite easy by using novnc with docker compose, check [here](https://github.com/sshawn9/my_scripts/blob/master/docker/compose/environment_with_novnc.yml).  
You can also copy following code and run a simple example.  
```docker
# docker compose -f docker-compose.yml up -d
# open http://127.0.0.1:8080/vnc.html
# install and run gedit in your ubuntu container and you will see gedit in browser.
version: '3'
services:
  ubuntu:
    image: ubuntu:18.04
    environment:
      - DISPLAY=novnc:0.0
    depends_on:
      - novnc
    command: ["sleep","infinity"]
  novnc:
    image: theasp/novnc:latest
    environment:
      # Adjust to your screen size
      - DISPLAY_WIDTH=1600
      - DISPLAY_HEIGHT=900
      - RUN_XTERM=no
      - RUN_FLUXBOX=yes
    ports:
      - "8080:8080"
```

## running GUI apps in container(Ubuntu)
Just map X11 socket, I will record this later.
