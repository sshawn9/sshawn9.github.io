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
<embed src="https://github.com/sshawn9/my_scripts/blob/master/utilities/ssh_for_remote_dev.sh" type="text/html" />  
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



