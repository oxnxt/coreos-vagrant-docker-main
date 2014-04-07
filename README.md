# docker-main

This repo provides a Vagrant script to create and initialize a virtual machine that hosts a docker daemon and a docker registry.

The virtual machine is running on CoreOS and is based on the CoreOS Vagrant repo:
https://github.com/coreos/coreos-vagrant

This Vagrantfile works with the VMWare Fusion provider and may not work with VirtualBox.

Ports forwarded to host OS:
```
4243 -> Docker
```

Volumes shared with guest OS:
```
./docker-main/ -> /home/core/share/
```

## Docker

The docker daemon is configured to listen for tcp connections on port 4243, which Vagrant is forwarding to the local operating system.
This allows users of OS X machines to run the docker client locally while talking to the daemon on the virtual machine.
In order for this to work, the DOCKER_HOST environment variable must be set (in .zshrc, for example):

```
## Configure Docker
export DOCKER_HOST=tcp://localhost:4243
```

## Docker Registry

A docker registry is also running on the virtual machine so that images can be pushed locally for testing.
Configuration for the registry is stored in `./docker-main/docker-registry/config.yml` and images are stored in
`./docker-main/docker-registry/images/`.


## Running

vagrant up --provider vmware_fusion

