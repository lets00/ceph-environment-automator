# Introduction

This project aims to create a full ceph environment in a KVM-based Virtual Machines (VMs). It creates VMs that are used to build the ceph environment.

[![asciicast](https://asciinema.org/a/182339.png)](https://asciinema.org/a/182339)


# What will I have when executing this?

This role installs the **virt packages** and It creates (by default) 3 VMs (**ceph-mon**, **ceph-osd0** and **ceph-osd1**). Those VMs will be available to be managed using the **Virsh** tool and you will be able to access it using the followin command:

```bash
virt-manager
```

# Requirements

- Ansible;
- **lxcbr0** bridge interface in machines that will run the VMs;
- **ceph0** bridge interface in machines that will run the VMs;

# How to create the bridge interfaces

First of all, install the **bridge-utils** package using the **Package Manager** available on your system.
Second, Create the bridges using the `brctl` command. The list of commands below show how to create and bring up the bridge interfaces. Run the commands and answer then, if asked:

```bash
brctl addbr ceph0
brctl addbr lxcbr0
ip link set up dev ceph0
ip link set up dev lxcbr0
```

# Executing this playbook
## Using Packer

*TO DO*

## Using cloud-init

Cloud-init images are available for some linux server distributions. Usually, they are small sized images that have the cloud-init lib installed and configured to start at boot time and wait for the **cloud-config** file. The loud-config file allows you to configure several options like users, passwords, ssh keys, routers and more.

The cloud-config file in this repository is set to use **user/password** as authentication method for the VMs. You may change the cloud-config file in the `roles/ansible_vms/templates/cloud-config.j2` Jinja2 file if you want to use ssh keys instead.

Some images that are prepared to use cloud-init:
- **ubuntu-16.04**: [https://cloud-images.ubuntu.com/xenial/current/xenial-server-cloudimg-amd64-disk1.img
](https://cloud-images.ubuntu.com/xenial/current/xenial-server-cloudimg-amd64-disk1.img)
- **centos-7.0**: [https://cloud.centos.org/centos/7/images/CentOS-7-x86_64-GenericCloud-1802.qcow2](https://cloud.centos.org/centos/7/images/CentOS-7-x86_64-GenericCloud-1802.qcow2)

if you want to change the default behavior, you need to override the default value of the variables. The variables are in the `roles/ansible_vms/vars/main.yml` Yaml file and are described below:

### VM cloud init image link
**vm_cloud_image**: "https://cloud-images.ubuntu.com/xenial/current/xenial-server-cloudimg-amd64-disk1.img"

### Name of vm cloud init image that will be defined
**vm_image_name**: "ubuntu-16.04"

### Password used for default user
**vm_user_password**: "str0ng_p@ssw0rd"

### Cloud config templates used to configure VMs
**vm_templates**: "/home/eduardo/Projects/ceph-environment-automator/raw_imgs"

# Running

Define locals where VMs will be configured in **inventory** file. If the inventory is not changed the VMs will be configure in the **localhost**.

Copy the Master's ssh key on the servers that will hold the VMs using:

> The Master's ssh key is the public part of the private key of the machine from which the main ansible script is being ran from.

```bash
ssh-copy-id <user>@<ip>
```

After that, you can run the playbook using:

```bash
ansible-playbook -i inventory prepare-hosts.yml -b --ask-sudo-pass
```

Enter the user's password (the user needs to have sudo permissions) and wait the playbook to finish
.

# Install ceph

TO DO

## Improvements

When I wrote this role/playbook, I needed some modules that are not implemented on Ansible yet. To use the functions I needed, I used the shell module to execute some commands.

Those modules are:
- **qemu-img**: A module to allow the creation of VM images.
- **packer**: A module to work with Packer.
- **cloud-localds**: A module to prepare cloud-config.cdrom images.
- **cloud-config**: A module to create cloud-config files.
- **iproute2**: A module to manage volatile virtual network interfaces (*This module is being created by me, the project is a **WIP***)
.
