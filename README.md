# kubernets raspberry

## First Step

Download iso from  
https://www.raspberrypi.org/software/operating-systems/#raspberry-pi-os-32-bit

Before use the https://www.balena.io/etcher/ to write on SD Card


### Before remove SD Card

#### Configure hostname and hosts

In SD Card locate the directory below
```
sudo nano /media/$USER/rootfs/etc/hostname
sudo nano /media/$USER/rootfs/etc/hosts
```

#### Configure boot options


In SD Card locate the directory below and add the configuretion in then end of the same line 
```bash
#this config
cgroup_enable=cpuset cgroup_memory=1 cgroup_enable=memory

sudo nano /media/$USER/boot/cmdline.txt

```

#### Configure SSH access

This config create a file with zero bytes to enable ssh service on first start
```
sudo touch /media/$USER/boot/ssh

```


## Start raspberry

Remove SD Card, put in Rasp and start your device

### Update and upgrade 

```
sudo apt update && sudo apt dist-upgrade
```

### Disable swap 

```
sudo dphys-swapfile swapoff
sudo dphys-swapfile uninstall
sudo apt purge dphys-swapfile
```

### Reboot

```
sudo reboot
```


## Install Docker 


https://get.docker.com/

```bash
#Option 1)
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh

#Options 2)
curl -sSL get.docker.com | sh


#Run the follow command to run Docker non-privileged user.
sudo groupadd docker
sudo usermod -aG docker $USER
newgrp docker 

# --> logout
# Test
docker run hello-world

```

## Install Docker-compose (optional)

```bash
sudo apt-get install libffi-dev libssl-dev
sudo apt install python3-dev
sudo apt-get install -y python3 python3-pip

sudo pip3 install docker-compose

```

## Setup Docker deamon options


```bash
 sudo nano /etc/docker/daemon.json
```

```json
{
    "exec-opts": ["native.cgroupdriver=systemd"],
    "log-driver": "json-file",
        "log-opts": {
        "max-size": "100m"
    },
    "storage-driver": "overlay2"
}

```
Alternative Option

```bash

cat > /etc/docker/daemon.json <<EOF
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2"
}
EOF

```
### Reboot Raspberry again



## Add Kubernetes repository


### Create file and add rep
```bash

sudo nano /etc/apt/sources.list.d/kubernetes.list

#add 
deb http://apt.kubernetes.io/ kubernetes-xenial main

```

### Add the GPG Key ti the pi
```bash

curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -

sudo apt update
```

### Install Kubernetes

```bash

sudo apt install kubeadm kubectl kubelet
```

### Only Master Node - Initialize K8S

```bash

sudo kubeadm init --pod-network-cidr=10.244.0.0/16
```
#### Output
```bash

#To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config


#To join nodes
#---------------
kubeadm join 192.168.1.211:6443 --token qtm201.vls9s8qlll7lldl5 \
	--discovery-token-ca-cert-hash sha256:0708c316a97bb90aa5afa884964095dd3dc598dc2b7cf4f0d307ceff2223233a 
```


### Only Master Node - Install flannel

```bash

kubectl apply -f https://github.com/coreos/flannel/raw/master/Documentation/kube-flannel.yml


```

## Checking everything is ok


```bash
kubectl get pods --all-namespaces

# Output
NAMESPACE     NAME                                         READY   STATUS    RESTARTS   AGE
kube-system   coredns-78fcd69978-f5l9p                     1/1     Running   0          5m38s
kube-system   coredns-78fcd69978-t8q25                     1/1     Running   0          5m38s
kube-system   etcd-kube-control-panel                      1/1     Running   0          5m48s
kube-system   kube-apiserver-kube-control-panel            1/1     Running   0          5m48s
kube-system   kube-controller-manager-kube-control-panel   1/1     Running   0          5m48s
kube-system   kube-flannel-ds-r9b2v                        1/1     Running   0          83s
kube-system   kube-proxy-cc9kj                             1/1     Running   0          5m38s
kube-system   kube-scheduler-kube-control-panel            1/1     Running   0          5m48s




kubectl get nodes

#Output
kube-control-panel   Ready    control-plane,master   6m15s   v1.22.0

```
## Configure Autocomplete 


```bash

#bash
source <(kubectl completion bash) # configuração de autocomplete no bash do shell atual, o pacote bash-completion precisa ter sido instalado primeiro.
echo "source <(kubectl completion bash)" >> ~/.bashrc # para adicionar o autocomplete permanentemente no seu shell bash.

#ZSH
source <(kubectl completion zsh)  # configuração para usar autocomplete no terminal zsh no shell atual
echo "if [ $commands[kubectl] ]; then source <(kubectl completion zsh); fi" >> ~/.zshrc # adicionar auto completar permanentemente para o seu shell zsh

```

## Run pods on master



```bash

#Remove taint 'NoSchedule'
kubectl taint node mymasternode node-role.kubernetes.io/master:NoSchedule-

#Restore taint 'NoSchedule'
kubectl taint node mymasternode node-role.kubernetes.io/master:NoSchedule

```

## Run first test



```bash
kubectl run nginx --image nginx
kubectl get pods
kubectl describe pod nginx
kubectl get events
kubectl get pod nginx -o yaml
kubectl delete pod nginx

kubectl run meu-nginx --image nginx --dry-run=client -o yaml > pod-template.yaml


```





### References
https://wiki.learnlinux.tv/index.php/How_to_build_your_own_Raspberry_Pi_Kubernetes_Cluster


https://www.youtube.com/watch?v=B2wAJ5FLOYw
