# Package Kali Linux for use with Vagrant

Kali Linux is a Debian-derived Linux distribution designed for digital
forensics and penetration testing

The default [Virtualbox](https://www.virtualbox.org/) image provided by
[Offensive Security](https://www.offensive-security.com) does not provide 'out
the box' support for VM management using [Vagrant](https://www.vagrantup.com/),
lacking a provisioning user, sshd or Virtualbox Guest Additions.

> Note that there is a supported box hosted on Atlas -

    https://atlas.hashicorp.com/Sliim/boxes/kali-2016.2-amd64

## How-To

Download Kali Linux Virtualbox Images from [Offensive Security](https://www.offensive-security.com/kali-linux-vmware-virtualbox-image-download/)

Convert ova to box

    vboxmanage import Kali-Linux-2016.2-vbox-amd64.ova --vsys 0 --eula accept
    vagrant package \
      --base $(vboxmanage list vms | awk '/Kali-Linux-2016.2-vbox-amd64/ {print $2}' | tr -d {}) \
      --output Kali-Linux-2016.2-vbox-amd64.box

Install box

    vagrant box add --name kali-linux-base Kali-Linux-2016.2-vbox-amd64.box

Install virtualbox expansion pack for usb support

    wget http://download.virtualbox.org/virtualbox/5.1.18/Oracle_VM_VirtualBox_Extension_Pack-5.1.18-114002.vbox-extpack
    vboxmanage extpack install Oracle_VM_VirtualBox_Extension_Pack-5.1.18-114002.vbox-extpack

Provision base box

    vagrant init kali-linux-base
    vagrant up

Log onto kali-linux-base as root to

- install virtualbox guest additions
- add vagrant user
- enable sshd


    apt-get update
    apt-get install -y virtualbox-guest-x11
    useradd -mG sudo -s /bin/bash vagrant
    passwd vagrant
    systemctl enable sshd.service

Package as a vagrant box we can provision

    vagrant package --output Kali-Linux-2016.2-vbox-amd64-vagrant.box
    vagrant box add --name kali-linux Kali-Linux-2016.2-vbox-amd64-vagrant.box
    vagrant init kali-linux

## Roadmap

- [x] manual conversion and packaging
- [ ] generate key
- [ ] ansible playbook to provision base Kali Linux vm box
- [ ] script me
