# install-windows-kvm
Install KVM on Ubuntu 20.04 with windows 10 vm

Install kvm packages

```
apt update
apt install dnsmasq cpu-checker qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils virt-manager qemu-efi qemu-utils virt-viewer ovmf
ufw allow 5900
ufw allow 3389

```

Edit default.xml and add this line inside dhcp section, below the 'range start' section.

use command: virsh net-edit default

```
<host mac='00:0C:24:4F:88:7C' name='Windows10' ip='192.168.122.2'/>
```

Start virsh default network interface

```
virsh net-destroy default
virsh net-autostart default
virsh net-start default
```

Start virtual machine by this command

```
virt-install --name='Windows10' \
--description='Windows10' \
--ram=12288 \
--vcpus 6 \
--disk path=/var/lib/libvirt/images/Windows10/Windows10.qcow2,size=256 \
--cdrom /var/lib/libvirt/images/Windows10/Windows10.iso \
--graphics vnc,listen=0.0.0.0,passwd='password' \
--hvm \
--arch x86_64 \
--sound \
--accelerate \
--cpuset=0,1,2,3,4,5 \
--network default,mac=00:0C:24:4F:88:7C \
--os-variant win10 \
--boot uefi
```

Now connect over vnc, and finish installation.

If you want more then 2 cores, use "virsh edit Windows10", and edit the following below "features" section:


```
<cpu mode='host-passthrough' check='none'>
<cache mode='passthrough'/>
<topology sockets='1' cores='4' threads='2'/>
</cpu>
  
```

If you want to portforward to your kvm clients, do the following:

1. Make script inside /opt/scripts

```
cd /opt && mkdir scripts && cd scripts && nano iptables-forward.sh

```

copy the following inside:

```
# /etc/rc.local


(
# Make sure the libvirt has started and has initialized its network.
while [ `ps -e | grep -c libvirtd` -lt 1 ]; do
        sleep 1
done
sleep 10
# Set up custom iptables rules.
iptables -t nat -I PREROUTING -p tcp -d YOUR_IP_ADDRESS --dport 3389 -j DNAT --to-destination 192.168.122.2:3389
iptables -t nat -I PREROUTING -p tcp -d YOUR_IP_ADDRESS --dport 9998 -j DNAT --to-destination 192.168.122.2:9998
iptables -I FORWARD -m state -d 192.168.122.0/24 --state NEW,RELATED,ESTABLISHED -j ACCEPT
) &

```

(change "YOUR_IP_ADDRESS" with your actual ip address, and change your desired ports accordingly)

2. make crontab

```
nano crontab -e

```

Type the following:

```
@reboot /opt/scripts/iptables-forward.sh

```

Reboot...
Done!
