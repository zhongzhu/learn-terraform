# learn-terraform
a hello world project for terraform. Will use terraform to provision an NGINX server using docker.

## My environment
I have one Window 11 laptop. In this project, I'll create 2 linux virtual machines on my laptop to practice terraform.

* mylinux
    * Ubuntu 22.04 VM created by Multipass on Windows 11
    * runs terraform
* mylinux2
    * target machine, to be provisioned by terraform
    * Ubuntu 22.04 VM created by Multipass on Windows 11

## Install mylinux

### Install terraform
```
# do you know why there is a --classic at the end?
$ sudo snap install terraform --classic
```
### Verify the installation
```
$ terraform --help
$ terraform -help plan 
```
## Install mylinux2
### Install docker engine
```
$ sudo snap install docker
```
### Verify the installation
```
$ sudo docker ps
```

### Allow non-root user to run docker commands
when you see below issue
```
zhongzhu@mylinux:~/workspace$ docker ps
Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get "http://%2Fvar%2Frun%2Fdocker.sock/v1.24/containers/json": dial unix /var/run/docker.sock: connect: permission denied
```
It's a sign that you should `sudo docker ps` but you forgot. With terraform docker provider, you'll probably run terraform with a non-root user like "zhongzhu". so it's important to do below to allow non-root user to run docker commands.

Check out link https://docs.docker.com/engine/install/linux-postinstall/ for more detailed instruction.

```
$ sudo addgroup docker
$ sudo adduser $USER docker
$ newgrp docker
$ sudo snap disable docker
$ sudo snap enable docker
```
