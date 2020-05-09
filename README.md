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
```
At this point, alpine is setup just enough that you can login over ssh to user jkozik

# Setup ssh access.  The terminal window in VB is clunky for me
$ apk add openssh
$ rc-update add sshd
$ rc-status
$ /etc/init.d/sshd start
$ adduser jkozik
