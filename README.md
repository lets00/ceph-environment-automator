# Introduction

This project aims to create a full ceph environment in Virtual Machines KVM based. It will create VMs that will be used to prepare ceph environment.

[![asciicast](https://asciinema.org/a/182339.png)](https://asciinema.org/a/182339)

# What will I have when execute it?

This role will install virt packages and It will create (for default) 3 VMs (**ceph-mon**, **ceph-osd0** and **ceph-osd1**). This VMs will be added in virsh and you can access it using this command:

```shell
$ virt-manager
```

# Requirements

- Ansible;
- **lxcbr0** bridge interface in machines that will running VMs;
- **ceph0** bridge interface in machines that will running VMs;

## How to create bridge interfaces

First, install **bridge-utils** package using the Package Manager available on your system.
After that, you can create bridge using **brctl** command. The command below shows how to create and up bridge interfaces:

```shell
# brctl addbr ceph0
# brctl addbr lxcbr0
# ip link set up dev ceph0
# ip link set up dev lxcbr0
```


# Executing this playbook
## Using Packer

TO DO

## Using cloud-init

Cloud-init images are available in some linux server distributions. Usually, they are small size images that contains cloud-init lib installed/configured to start at boot time and wait **cloud-config** file. Cloud-config file allows to configure several options like users, passwords, ssh keys, routers and others.

Cloud-config file is prepared to use user/password to log in VMs. You can change cloud-config file in **roles/ansible_vms/templates/cloud-config.j2** Jinja2 file if you want to use ssh keys.

Some images that is prepared to use cloud-init:
- **ubuntu-16.04**: https://cloud-images.ubuntu.com/xenial/current/xenial-server-cloudimg-amd64-disk1.img
- **centos-7.0**: https://cloud.centos.org/centos/7/images/CentOS-7-x86_64-GenericCloud-1802.qcow2

You need define(or override the default value) some variables if you want to change the default behavior. The variables are in **roles/ansible_vms/vars/main.yml** and are described below:

```
# VM cloud init image link
vm_cloud_image: "https://cloud-images.ubuntu.com/xenial/current/xenial-server-cloudimg-amd64-disk1.img"
# Name of vm cloud init image that will be defined
vm_image_name: "ubuntu-16.04"
# Password used for default user
vm_user_password: "str0ng_p@ssw0rd"
# Cloud config templates used to configure VMs
vm_templates: "/home/eduardo/Projects/ceph-environment-automator/raw_imgs"
```
### Running

First, define locals where VMs will be configured in **inventory** file. For default, It will install in localhost.

Copy ssh key on servers that will configure VMs using:

```shell
$ ssh-copy-id <user>@<ip>
```

After that, you can run playbook using:

```shell
$ ansible-playbook -i inventory prepare-hosts.yml -b --ask-sudo-pass
```

Enter with sudo password and wait playbook finish
its execution.

# Install ceph

TO DO

# Notes

## OSDs and Journals

This playbooks does not implement idempotence to create OSDs and Journals images (Because it use shell module). Every time that you run this playbook it will **override** OSDs and Journals images. So, **Be careful**.

## Improvements

When I wrote this role/playbook, I needed some modules that still not implemented on Ansible. To use functions that I needed, I used shell module to execute some commands.
This modules are:
- **qemu-img**: A module to allow create VM images.
- **packer**: A module to work with Packer.
- **cloud-localds**: To prepare cloud-config.cdrom images.
- **cloud-config**: To create cloud-config files.
- **iproute2**: To manage volatile virtual network interfaces (This module is been created by me, and project is **WIP**)
