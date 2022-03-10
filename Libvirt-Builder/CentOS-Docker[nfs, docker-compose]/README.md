# What is it?
It's a libvirt image maker<br>
<br>

# Systems and Setting<br>
Checkout Front Page <b>Please!!</b><br>
[https://github.com/tonyhan18/Libvirt-tonyhan/blob/main/README.md](https://github.com/tonyhan18/Libvirt-tonyhan/blob/main/README.md)<br>
<br>
<br>

And Make Like this<br>
the DB and KVM1, KVM2 is linked by nfs system<br>
![../images/system.png](../images/system.png)<br>
<br>

if you don't please follow the Front Page's settings
[https://github.com/tonyhan18/Libvirt-tonyhan/blob/main/README.md](https://github.com/tonyhan18/Libvirt-tonyhan/blob/main/README.md)<br>
<br>

# How to work? - Manager

- first todo : git clone
`git clone https://github.com/tonyhan18/Libvirt-tonyhan.git`

or just copy and paste install.sh in your running folder

- second : run this command where install.sh is located

image file will be maken in /shared folder
name as manager.qcow2
from DB

```bash
virt-builder centos-7.4 -o /shared/manager.qcow2 --size 20G \
--hostname manager --root-password password:test123 \
--run install.sh \
--mkdir /nfs \

--firstboot-command 'sudo mount -t nfs 211.183.3.199:/instance /nfs' \
--run-command 'sudo systemctl enable httpd' \
--run-command 'sudo systemctl disable firewalld' \
--run-command 'sudo systemctl enable docker' \
--run-command 'sudo docker swarm init --advertise-addr=eth0:2377 > /nfs/manager-token.txt' \

docker swarm init --advertise-addr 211.183.3.100
--selinux-relabel
```

- third : run this command anywhere

VMWare Spec
vcpus : 1
ram : 2048MB
disk : 20G

```bash
virt-install --name manager \
--vcpus 1 --ram 2048 \
--network bridge:br0,model=virtio \
--disk /cloud/dockertest.qcow2 --import \
--noautoconsole --os-variant rhel7
```

# How to work? - Worker
### Worker1 - KVM1
virt-builder centos-7.4 -o /shared/worker1.qcow2 \
--size 20G --hostname worker1 --mkdir /nfs \
--root-password password:test123 --run install_worker.sh \
--run-command 'sudo systemctl enable httpd' \
--run-command 'sudo systemctl disable firewalld' \
--run-command 'sudo systemctl enable docker' \
--firstboot-command 'sudo mount -t nfs 211.183.3.199:/instance /nfs' \
--firstboot-command 'sudo $(cat /nfs/manager-token.txt | grep "SWMTKN") ' \
--selinux-relabel

### Worker2 - KVM2
virt-builder centos-7.4 -o /shared/worker2.qcow2 \
--size 20G --hostname worker2 --mkdir /nfs \
--root-password password:test123 --run install_worker.sh \
--run-command 'sudo systemctl enable httpd' \
--run-command 'sudo systemctl disable firewalld' \
--run-command 'sudo systemctl enable docker' \
--firstboot-command 'sudo mount -t nfs 211.183.3.199:/instance /nfs' \
--firstboot-command 'sudo $(cat /nfs/manager-token.txt | grep "SWMTKN") ' \
--selinux-relabel

# Finish
![result.png](result.png)