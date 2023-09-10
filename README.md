# learn-terraform
a hello world project for terraform. Will use terraform to provision an NGINX server using docker.

## My environment
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
