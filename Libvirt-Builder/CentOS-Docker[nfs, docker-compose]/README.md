# What is it?
It's a libvirt image maker<br>
<br>

# Systems and Setting<br>
Checkout Front Page <b>Please!!</b><br>
[https://github.com/tonyhan18/Libvirt-tonyhan/blob/main/README.md](https://github.com/tonyhan18/Libvirt-tonyhan/blob/main/README.md)<br>
<br>

# How to work?

- first todo : git clone
`git clone https://github.com/tonyhan18/Libvirt-tonyhan.git`

or just copy and paste install.sh in your running folder

- second : run this command where install.sh is located

image file will be maken in /shared folder
name as manager.qcow2

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