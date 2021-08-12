# install-windows-kvm
Install KVM on Ubuntu 20.04 with windows 10 vm

Install kvm packages

```
apt update
apt install dnsmasq cpu-checker qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils
ufw allow 5900
ufw allow 3389

```

Edit default.xml and add this line inside dhcp section, below the 'range start' section.

use command: virsh net-edit default

```
<host mac='00:0C:24:4F:88:7C' name='Windows10' ip='192.168.122.2'/>
```

Start virsh default network

```
virsh net-autostart default
virsh net-start default
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
--cpuset=0,1,2,3,4,5 \
--network default,mac=00:0C:24:4F:88:7C
```

Now connect over vnc, and finish installation.
