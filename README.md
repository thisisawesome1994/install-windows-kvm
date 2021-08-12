# install-windows-kvm
Install KVM on Ubuntu 20.04 with windows 10 vm

Install kvm packages

```
apt update
apt install dnsmasq cpu-checker qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils
ufw allow 5900
ufw allow 3389

```

Start virtual machine by this command

```
virt-install --name='Windows10' \
--description='Windows10' \
--ram=12288 \
--smp 6,sockets=1,cores=3,threads=6 \
--disk path=/var/lib/libvirt/images/Windows10/Windows10.qcow2,size=256 \
--cdrom /var/lib/libvirt/images/Windows10/Windows10.iso \
--graphics vnc \
--machine type=q35,accel=kvm \
--sound \
--accelerate \
--cpuset=8
```

Now connect over vnc, and finish installation.
