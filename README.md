# learn terraform docker provider
This is a detailed note taken when I follow https://developer.hashicorp.com/terraform/tutorials/docker-get-started to learn terraform docker provider.

## My environment
In this project, I'll create 2 Ubuntu virtual machines on my Windows 11 Pro laptop to practice terraform.

* mylinux
    * runs terraform
    * Ubuntu 22.04
* mylinux2
    * target machine, to be provisioned by terraform
    * Ubuntu 22.04
    * docker 20.10.24

## Create virtual machines

### Install multipass
Follow the instruction on https://multipass.run/ to install multipass on Windows 11 Pro.

### Create Ubuntu virtual machines
Issue below commands in Windows Terminal
```
> multipass launch -n mylinux
> multipass launch -n mylinux2
```

### Add mylinux's ssh key to mylinux2
On mylinux
```
$ ssh-keygen -t ed25519
```

On mylinux2
```
$ cd
$ mkdir .ssh
$ touch ~/.ssh/authorized_keys
```
Append the content of mylinux's `~/.ssh/id_ed25519.pub` to mylinux2's `~/.ssh/authorized_keys`.

## Setup mylinux

### Install terraform
do you know why there is a --classic at the end?
```
$ sudo snap install terraform --classic
```
### Verify the installation
```
$ terraform --help
$ terraform -help plan 
```
## Setup mylinux2

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
zhongzhu@mylinux2:~/workspace$ docker ps
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

## Let's run the code
On mylinux
```
$ git clone https://github.com/zhongzhu/learn-terraform.git
$ cd learn-terraform
$ terraform init
$ terraform plan
$ terraform apply
```

Below is the execution output.

```
zhongzhu@mylinux:~/workspace/learn-terraform$ terraform apply

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # docker_container.nginx will be created
  + resource "docker_container" "nginx" {
      + attach                                      = false
      + bridge                                      = (known after apply)
      + command                                     = (known after apply)
      + container_logs                              = (known after apply)
      + container_read_refresh_timeout_milliseconds = 15000
      + entrypoint                                  = (known after apply)
      + env                                         = (known after apply)
      + exit_code                                   = (known after apply)
      + hostname                                    = (known after apply)
      + id                                          = (known after apply)
      + image                                       = (known after apply)
      + init                                        = (known after apply)
      + ipc_mode                                    = (known after apply)
      + log_driver                                  = (known after apply)
      + logs                                        = false
      + must_run                                    = true
      + name                                        = "tutorial"
      + network_data                                = (known after apply)
      + read_only                                   = false
      + remove_volumes                              = true
      + restart                                     = "no"
      + rm                                          = false
      + runtime                                     = (known after apply)
      + security_opts                               = (known after apply)
      + shm_size                                    = (known after apply)
      + start                                       = true
      + stdin_open                                  = false
      + stop_signal                                 = (known after apply)
      + stop_timeout                                = (known after apply)
      + tty                                         = false
      + wait                                        = false
      + wait_timeout                                = 60

      + ports {
          + external = 8000
          + internal = 80
          + ip       = "0.0.0.0"
          + protocol = "tcp"
        }
    }

  # docker_image.nginx will be created
  + resource "docker_image" "nginx" {
      + id           = (known after apply)
      + image_id     = (known after apply)
      + keep_locally = false
      + name         = "nginx"
      + repo_digest  = (known after apply)
    }

Plan: 2 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

docker_image.nginx: Creating...
docker_image.nginx: Still creating... [10s elapsed]
docker_image.nginx: Still creating... [20s elapsed]
docker_image.nginx: Creation complete after 26s [id=sha256:f5a6b296b8a29b4e3d89ffa99e4a86309874ae400e82b3d3993f84e1e3bb0eb9nginx]
docker_container.nginx: Creating...
docker_container.nginx: Creation complete after 2s [id=7d9e4e5eb1939119bdb19f39381f51b004d7ad3ce42fa77e87432c7cd3e9a00f]

Apply complete! Resources: 2 added, 0 changed, 0 destroyed.
```

After terraform apply done, we can test our provision. As the NGINX docker container is started on mylinux2, you can check mylinux2's IP address using `ip a`. Then on Windows host, fire up your browser to access `http://mylinux2.mshome.net:8000`. If you see
```
Welcome to nginx!
If you see this page, the nginx web server is successfully installed and working. Further configuration is required.

For online documentation and support please refer to nginx.org.
Commercial support is available at nginx.com.

Thank you for using nginx.
```

then it means the provision is done successfully. Congrats!

## Destroy the NGINX server
once you've done with the NGINX server, you can use terraform to destroy it.

on mylinux
```
$ terraform destroy
```
