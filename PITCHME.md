---
marp: true
title: Docker, Your Shell and Security
description: Some ideas on using Docker to improve security workflows
theme: haskell
class:
- lead
paginate: true
_paginate: false
---

# <!--fit--> Docker, Your Shell and Security

#### *Ideas on using Docker to improve security workflows*  

__https://heywoodlh-saintcon2019.netlify.com__

-------------------------------------------------

### Spencer Heywood
*Security Analyst @ Utah Education Network*

__https://github.com/heywoodlh/__


-------------------------------------------------
# Docker Basics

### What is Docker? 
A cross-platform container service that can run Linux and Windows Applications.

### What are containers?
High-level overview of a container: a tiny machine with pre-packaged software. I often compare containers to _very_ tiny and single-purpose virtual machines.

-------------------------------------------------
# Why I use Docker?

- Cross-platform

- Simplifies installation of command line and server applications

- Widely used

- User-friendly and well documented

- Free and open-source

-------------------------------------------------

# A Simple Dockerfile:

###### A Dockerfile contains the build instructions when a Docker image is created.

```bash
FROM ubuntu    ### Use the official Ubuntu Docker image as the base

RUN apt update && apt install telnet netcat -y    ### Install telnet and netcat

CMD "/bin/bash"    ### Set the default command to run in the container
```


-------------------------------------------------

# Some Docker Command Examples:

Pull (download) a Docker image from hub.docker.com for later use:

```bash
docker pull ubuntu
```

Run an Ubuntu container that executes the command `echo "hello world"`:

```bash
docker run ubuntu echo "hello world"
```

Get an interactive BASH shell in a newly created Ubuntu container and automatically remove the container ( the `--rm` flag) when the shell exits:

```bash
docker run --rm -it --name my_ubuntu ubuntu bash
```

-------------------------------------------------

List running containers:

```bash
docker ps
```

Stop and remove a container:

```bash
docker stop <container id> && docker rm <container id>
```

Start a currently stopped container:

```bash
docker start <container id>
```

-------------------------------------------------

# Common Container Design Principles

-------------------------------------------------
### Minimal:

- Containers run on tiny Linux (or Windows) distributions.
- Provide the minimum required kernel/OS functionality (networking, package manager, etc.) to run an application.
- Common Linux tools not required by the kernel or application are usually not installed.


-------------------------------------------------

### Single-Purpose:

- Containers are usually built to run a single piece of software.
- For applications that require multiple pieces of software to run, often the deployment requires multiple linked containers.

_I.E. for a web application, such as Wordpress, there is often a container dedicated to running your web server _and another separate_ container providing database functionality for the application._

-------------------------------------------------

### Secure:

- Containers provide a layer of isolation from the host running the containers.

- Containers are isolated from other containers, unless explicitly linked.

- Container networking is isolated by default (no exposed ports unless configured otherwise).


-------------------------------------------------
# Docker for Security

-------------------------------------------------

# Run Security Tools in Docker Containers!

- Keep your security tools isolated from your host (less clutter, more security).

- Have the same security workflows on Linux, MacOS and Windows (if using WSL 2).

- Use resources more efficiently than with a virtual machine.


-------------------------------------------------
# Running Security Tools in Docker via Your Preferred Shell


Use BASH or Powershell aliases, Fish functions or a similar mechanism in your shell to shorten long Docker commands.

For example, the following `nmap` command in Docker:

```bash
docker run --rm --net host --privileged booyaabes/kali-linux-full nmap -sS 192.168.1.0/24
```

Could be made into a BASH alias:

```bash
alias nmap="docker run --rm --net host --privileged booyaabes/kali-linux-full nmap $@"
```

Which would then allow you to run the `nmap` Docker alias like so:

```bash
nmap -sS 192.168.1.0/24
```


-------------------------------------------------
# BASH Aliases and Fish Function

### Start Using Docker+BASH/Fish Today:

A compiled list of common security tool Docker aliases/functions is available for use today in the Github repository of this presentation (look at the README for the links):


__https://github.com/heywoodlh/SAINTCON2019__


BASH Aliases: __https://github.com/heywoodlh/SAINTCON2019/blob/master/security-functions.sh__

Fish Functions: __https://github.com/heywoodlh/SAINTCON2019/blob/master/security-functions.fish__

-------------------------------------------------
# Some Example BASH Aliases
Nmap:
```bash
alias nmap="docker run --rm --net host --privileged booyaabes/kali-linux-full nmap $@"
```

Metasploit:
```bash
alias msfconsole="docker run -it --rm --net host booyaabes/kali-linux-full msfconsole $@"
```

Deploy OpenVAS for vulnerability scanning:
```bash
alias openvas="docker run -d -p 443:443 -p 9390:9390 --name openvas mikesplain/openvas"
```



-------------------------------------------------
# With Great Power Comes Great Responsibility!

Docker can be easily weaponized:
- Docker runs as a privileged service.
- As a privileged service, Docker can do things like access and manipulate network devices and access files/folders that are normally restricted.
- There are _tons_ of potentially dangerous tools available at hub.docker.com that could easily be pulled down and used maliciously.

###### This doesn't mean you shouldn't use Docker, but you should know the risks before you rely solely on it.

-------------------------------------------------

# Some Security Suggestions:

- If Docker won't be used regularly, don't install it.

- Don't be careless with access to the Docker daemon (I.E. on a shared Linux system, don't add users to the `docker` group).

- Use verified/official images from Docker Hub.

- If using a third party's Docker container look at the Dockerfile when possible. Understand how the container is built and what each command in the Dockerfile is doing. If you aren't sure, don't use the container.

###### Docker is a virtualization tool: a lot of the same risks (and benefits) come with other virtualization tools (Virtualbox, VMWare, etc).

-------------------------------------------------

# That's all!
