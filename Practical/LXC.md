# Using LXC
#### **Configuration**
**main link :**

<https://ubuntu.com/server/docs/containers-lxc>

<https://eax.me/lxc/>

**Introduction to linux interfaces for virtual networking**

<https://developers.redhat.com/blog/2018/10/22/introduction-to-linux-interfaces-for-virtual-networking#>

##### ***configure VBox***
**VBox config after patch kernel:** changed the graphics controller to VBoxVGA"

not working with NAT to access internet with mptcp :

*The design of Multipath TCP has been heavily influenced by the middleboxes that have been deployed in a wide range of networks, notably in cellular and enterprise networks. Some of these middleboxes like regular NATs interact correctly with Multipath TCP and many Multipath TCP users work behind NATs. However, some middleboxes, such as firewalls or TCP optimisers, terminate TCP connections or interfere with TCP options and thus interact badly with Multipath TCP.*


working with **Network Bridge** network configuration..

install guest additions :

<https://linuxize.com/post/how-to-install-virtualbox-guest-additions-in-ubuntu/>

##### ***create and configure container***  
find linux container :

<https://us.images.linuxcontainers.org/>

>mint	ulyssa	amd64 -> tested but not working
>mint	sarah	amd64 -> **actually working**

lxc configuration keys

https://linuxcontainers.org/lxc/manpages/man5/lxc.container.conf.5.html

>sudo tunctl -t tap-right
>sudo ifconfig tap-right 0.0.0.0 promisc up

**create linux container** with 2 interfaces (see .conf on scratch)

**sudo lxc-create -t download -n mycontainer -- -d ubuntu -r xenial -a amd64 --keyserver hkp://keyserver.ubuntu.com**

>sudo chroot /var/lib/lxc/mp-left/rootfs/ passwd
>sudo lxc-start -n left


##### ***configure existing LXC containers***  
> sudo nano /var/lib/lxc/right/config
> sudo nano /var/lib/lxc/left/config

##### ***lxc-chekconfig :***
`	`**see required modules : [https://programmersought.com/article/78551456854/**](https://programmersought.com/article/78551456854/)**

Note : Before booting a new kernel, you can check its configuration

usage : CONFIG=/path/to/config /usr/bin/lxc-checkconfig

##### ***lxc-copy :***
copy container lxc-copy -n C1  -NC2

***execute script on startup***

main discussion:  

<https://github.com/lxc/lxc/issues/496>

**crete script for linux containers**, and Make it executable (chmod 755): add hook to config file of container

lxc.hook.mount = /var/lib/lxc/mp-right/ifup.sh
sudo xed /var/lib/lxc/mp-left/ifup.sh

**OR add script to file on container**
sudo nano /etc/rc.local


##### ***Limitations***

**Not every device is able to connect to LXC from NS3 (irrelevant if use FdNetDev)**

*use **promiscuous mode in the bridged device t**o receive packets destined for the different MAC*
*SendFrom() which allows a caller to "spoof" or change the source MAC address to match the different Linux MAC address*
*allows us to use Send() on the ns-3 device side which is available on all ns-3 net devices.*

*Linux host writes to one of the /dev/tap devices,the write is redirected into the TapBridge that lives in the ns-3 world*

*only ns-3 net devices that support SendFrom() and have a hookable promiscuous receive callback are allowed to participate in UseBridge mode TapBridge configurations.*
#### **Routing**
**see route**
>ip route show
>route
>netstat -nr

***add route***
> sudo ip route add 172.16.0.0/24 via 192.168.122.1 dev ens3
> sudo route add -net 10.0.0.0/8 gw 192.168.1.1 eth0
> sudo ip route add default via 192.168.1.254

**add default route  to lxc container configuration file** 

> lxc.net.1.ipv4.gateway = 10.0.1.1

**delete route**
sudo route del -net 192.168.3.0 gw 192.168.1.1 netmask 255.255.255.0 dev eth0

