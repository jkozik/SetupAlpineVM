# SetupAlpineVM
Notes for setting up Alpine in a VirtualBox VM

I have been using Centos 7 for my VMs.  I want to try Alpine, because all my latest VMs have docker containers and I thought Alpine would be lighter.  But Alpine is differnet and thus I need to record notes on setting it up.  

```
# download standard alpine distro to VirtualBox host
$ wget http://dl-cdn.alpinelinux.org/alpine/v3.11/releases/x86_64/alpine-standard-3.11.6-x86_64.iso
```

In VB, create a VM as follows: 
```
Linux/Linux 2.6 / 3.x / 4.x
RAM 1G
Virt HardDisk 8G fixed VDI
Storage controller IDE Optical Drive DVD Image - iso from above
Ram 1G
Network -- Attached to Bridged Adapter
```
Start the VM and run the setup-alpine script
```
login root, no password
# setup-alpine
Keyboard - us / us
host name: alpine1
network eth0
IP addr - 192.168.100.177 -- something on home LAN (bridged mode)
GW - 192.168.100.163 -- my home router
no manuel setup for LAN
DNS name alpine1.kozik.net
DNS nameservers - 8.8.8.8
Set new password for root
Timezone - America/Chicago
chrony ntp client
Disk - sdb, vdi, sys
Eth0 - static IP on my home LAN (bridged VM)
Set passwd
Detect and add fastest mirror
openssh
sda disk used for sys, yes, erase
```
The Alpine OS will install and reboot.  In VB make sure to set the boot order so that you don't reboot from the install CD but from the hard drive. Log in as root and do the following intial setup steps:
```
# apk update
# apk add vim
# vi /etc/apk/repositories # uncomment all of the repositories
# apk update
# adduser jkozik
# apk add sudo
# visudo    # 
```
At this point, alpine is setup just enough that you can login over ssh to user jkozik. Login with ssh to jkozik@192.168.100.176 using password, and setup rsa key.
```
$ mkdir -p ~/.ssh && touch ~/.ssh/authorized_keys
$ chmod 700 ~/.ssh && chmod 600 ~/.ssh/authorized_keys
$ vi ~/.ssh/authorized_keys    # cut and paste a contents of a id_rsa.pub public key file into here
```
At this point, you can ssh into the alpine linux instance with ssh key, no password.  To me this is now ready to be customized.  I want this VM to run docker, so I log back in and continue setting up the image.
```
$ sudo apk add docker docker-compose
$ sudo rc-update add docker boot
$ sudo service docker start
$ sudo service docker status    # verify docker is running
$ sudo docker run hello-world   # this should work

# I need to setup group permissions so I don't need sudo for each docker command per
# http://web.ist.utl.pt/joao.leao.guerreiro/post/alpinedocker/

$ sudo visudo   #uncomment %wheel ALL=(ALL) ALL
$ sudo vi /etc/group  # wheel:x:10:root,jkozik,  and docker:x:102:jkozik
$ docker run hello-world  # this should now work I think you need to logout and log back in to verify.
```
At this point, let's pull in a known working docker repository and test run it.
```
$ sudo apk add git
$ git clone https://github.com/jkozik/InstallNw.com
$ cd InstallNw.com
$ docker build -t jkozik/nw.com .
$ docker run -dit --name nw.com-app -p 8082:80  jkozik/nw.com
$ docker exec -it nw.com-app /bin/bash
```

Side note:  VB lets you share files from the host OS.  Here's the raw steps I followed:
# apk add virtualbox-guest-additions virtualbox-guest-modules-virt
# mkdir /mount
# mkdir /mount/weather
# mkdir /mount/wjr
# mount -t vboxsf weather /mount/weather
# mount -t vboxsf wjr     /mount/wjr
# vi /etc/fstab    #add "weather /mount/weather  vboxsf  defaults 0 0"
                   #add "wjr     /mount/wjr      vboxsf  defaults 0 0"
# vi /etc/modules  #add "vboxfs"
