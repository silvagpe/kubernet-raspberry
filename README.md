# kubernets raspberry

## First Step

Download iso from  
https://www.raspberrypi.org/software/operating-systems/#raspberry-pi-os-32-bit

Before use the https://www.balena.io/etcher/ to write on SD Card


## Before remove SD Card

### Configure hostname and hosts

In SD Card locate the directory below
```
sudo nano /media/$USER/rootfs/etc/hostname
sudo nano /media/$USER/rootfs/etc/hosts
```

### Configure boot options


In SD Card locate the directory below and add the configuretion in then end of the same line 
```bash
#this config
cgroup_enable=cpuset cgroup_memory=1 cgroup_enable=memory

sudo nano /media/$USER/boot/cmdline.txt

```

### Configure SSH access

This config create a file with zero bytes to enable ssh service on first start
```
sudo touch /media/$USER/boot/ssh

```


## Start raspberry

Remove SD Card, put in Rasp and start your device

## Update and upgrade 

```
sudo apt update && sudo apt dist-upgrade
```







### References
https://wiki.learnlinux.tv/index.php/How_to_build_your_own_Raspberry_Pi_Kubernetes_Cluster
