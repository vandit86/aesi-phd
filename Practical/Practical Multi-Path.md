
#####
##### ***First Part :***
<https://docs.google.com/document/d/1ClFLDEqQJJe-UF3-zD4Sz9NmSTUouwgvg0nsBznWal4/edit#>

##### ***courses :***

microsoft summer : <https://www.microsoft.com/en-ie/training-days?activetab=0%3aprimaryr4&wt?ocid=eml_pg288392_gdc_comm_az&mkt_tok=MTU3LUdRRS0zODIAAAF-gSIJc48InHqFII4lkhewsuYziK8rxSY9-6o4YF2IBw9GrpmvsYylncawUQaSoa_ptipXHg08zBZoS7HKESaNDWvQSfgqnCEX9slLkA5vHnRtRHyUyQPQUe4S>

google, inro to ML :

<https://developers.google.com/machine-learning/crash-course/ml-intro>

# Tools
## **SW Tools**
##### ***GIT***

configure access to repo with new token system

<https://stackoverflow.com/questions/68775869/support-for-password-authentication-was-removed-please-use-a-personal-access-to>

#####
##### ***Grep***

find a pattern in source code :

grep --include=\\*.{c,h} -rn '/path/to/somewhere/' -e "pattern"

##### ***Ping***
`	`-i  :  interval  

`	`-s : packet size

`	`-f : flooding mode

`	`-D : put timestamp on output

` `We calculated RTT using the S**ynthetic Packet Pair (SPP**). <http://caia.swin.edu.au/tools/spp/>

##### ***Examples of use***

- <https://habr.com/ru/post/24046/>  (**Эмуляция влияния глобальных сетей**)
- <https://www.jtouzi.net/measuring-network-throughput-netperf-iperf/> (**Measuring Network Throughput using NetPerf & iPerf** )


##### ***SS***  
<https://www.tecmint.com/ss-command-examples-in-linux/>

After the connection is established, verify the ss output to see the subflow-specific status:
##### `		`*ss -nti '( dport :4321 )' dst 192.0.2.1*
get TCP related values using `ss` tool :

\```sh

\# se all general

ss -i   

\# see tcp, established

ss -ite 'src 1.1.1.1:1234 and dst 2.2.2.2:1234'  


the values to be monitored (see ``man ss`` for explanation) :

\- rcv\_rtt

\- bytes\_sent

\- bytes\_receive

\- **in fly packets  on each subflow??**  

\- ...

##### ***ethertool***
`	`**switch off** tcp checksum

ethtool --offload  eth0  rx off  tx off

ack (and thus retransmission) are caused by actual drop at the NIX RX queue level:

<https://github.com/multipath-tcp/mptcp_net-next/issues/253>

the following workaround is effective in my setup:

*ethtool -G enp37s0f0np0 rx 4096*


##### ***tcpdump***
Capture traffic using tcpdump
##### `		`*tcpdump -tnni interface tcp port 4321*

I would capture in 3 terminals or by backgrounding the command with &

The flags -nn turns off dns resolution for speed, -s 0 saves the full packet and -w writes to a file.

tcpdump -i wan0 -nn -s 0 -w wan0.dump

tcpdump -i wan1 -nn -s 0 -w wan1.dump

tcpdump -i lan0 -nn -s 0 -w lan0.dump

I would then merge the files with the mergecap command from wireshark:

mergecap -w merged.dump wan0.dump wan1.dump lan0.dump


##### ***Netstat***
- *you can also check if port is open or not*
##### ` `netstat -tulpen
- see the number of packets sent from each interface
##### netstat -i
`		`netstat -ie

`		`netstat -r

##### ***netem / tc command***
`	`linux traffic control : to vary latency and packet loss rate

<https://wiki.linuxfoundation.org/networking/netem>

<https://netbeez.net/blog/how-to-use-the-linux-traffic-control/>

**Network delay** : tc qdisc add dev eth0 root netem delay 200ms

**display active rules : tc qdisc show  dev eth0**

**delete all rules**  : tc qdisc del dev eth0 root

**Packet loss :** tc qdisc add dev eth0 root netem loss 10%

**bandwidth limit :** tc qdisc add dev eth0 root tbf rate 1mbit burst 32kbit latency 400ms

combine multiple impairments ..

<https://www.excentis.com/blog/use-linux-traffic-control-impairment-node-test-environment-part-2>
##### *** 
##### ***Dummynet***
like tc tool

<http://info.iet.unipi.it/~luigi/dummynet/>

##### ***fallocate***   
*create file with arbitrary size*
##### *fallocate -l 10M gentoo\_root.img*

##### ***bwm tool***
which is a bandwidth monitoring tool for Linux

##### ***bmon (Bandwidth monitor tool)***
bmon -p eth0,eth1 -b

#### Traffic Generator
##### ***Netperf***
is able to fully use one interface

\# netserver  on server side

netperf -t omni -H 10.1.10.2 -l 30 -T 1/1  -c -C -- -m 512k -V

What are these options?

- -t omni specifies the netperf-test to use. This sends a single data-stream between two hosts.
- -H 10.1.10.2 the destination-server's IP address
- -l 30 the duration of the test (30 seconds)
- -T 1/1 to pin the applications on the client and the server side to CPU 1.
- -c -C output statistics on the CPU-usage at the end of the test
- -m 512k specifies the size of the send-buffer
- -V use zero-copy send and receive

##### ***Netcat***
Start a server that listens to TCP port 4321:

nc -4 -l 4321

nc -4 192.0.2.1 4321

Hello world 1



Start by using nc to listen on a specific port, with output captured into a file:

` `$ nc -l 1234 > filename.out

Using a second machine, connect to the listening nc process, feeding it the file which is to be transferred

` `$ nc host.example.com 1234 < filename.in

After the file has been transferred, the connection will close automatically.

\# **monitor throughput**

nc -l -p 1852 > /dev/null 			# server  

cat /dev/zero | pv -r | nc 192.168.1.200 1852 	# client

<https://saurorja.org/2011/07/25/network-throughput-measurement-using-netcat/>

##### ***Iperf (v2, v3)***
[**https://fasterdata.es.net/performance-testing/network-troubleshooting-tools/iperf/**](https://fasterdata.es.net/performance-testing/network-troubleshooting-tools/iperf/)

**Data traffic is generated using the iperf tool** : which allows the adjustment of TCP-related parameters like receiver buffer size and data volume size

**iperf3 :** Iperf3 is used to generate traffic but also to record the reached throughput. It furthermore exposes some important options that allow us to specify what congestion control algorithm it should use and how frequent it should report the current throughput
##### *iperf3 -s   				# server side*
##### *iperf3 -c 192.0.2.1  			# client side*
`	`iperf3 -c 14.0.0.2 -C olia -t 30 -R  -Z

**Comparison iperf vs iperf3:**  <https://iperf2.sourceforge.io/IperfCompare.html>  

**iperf**   is better for mptcpd (no additional traffic is generated when connection is closed)

<https://netbeez.net/blog/linux-iperf-2-release/>

**/proc/net/dev** and **netstat** report the expected performance superiority


##### ***tens: new traffic generation tool***
<https://github.com/vmware/te-ns>

##### ***TRex***
Realistic Traffic Generator <https://trex-tgn.cisco.com/>



#### Other

**MPTCP BW/Delay Benefit Measurements:**

<https://inl.info.ucl.ac.be/softwares/mptcp-bwdelay-benefit-measurements.html>

**Controllable per-Flow Load Balancing**

consists of an invertible mechanism that allows sources to easily steer packets over any load-balanced path :

<https://inl.info.ucl.ac.be/cflb.html>

**MI-LXC : Mini-Internet using LXC  <https://github.com/flesueur/mi-lxc>**

**QEMU** is a generic and open source machine emulator and virtualizer.

<https://www.qemu.org/>

**Microsoft Visio**: Microsoft Visio is a diagramming and vector graphics application

**EVE-Emulated Virtual Environment ?**

Tstat

<http://tstat.polito.it/measure.shtml>
#### Mptcp analysis tools
1) mptcptrace : [mptcptrace](https://bitbucket.org/bhesmans/mptcptrace)  last commit 2014/15 (not verified)

git clone https://bitbucket.org/bhesmans/mptcptrace.git

cd mptcptrace

git checkout dvlp\_branch

` `./autogen.sh

./configure --prefix=whatever/

make CPPFLAGS="-DUSE\_HASHTABLE -DENDCONN"

make install

1) Mptcpanalyzer is a python tool conceived to help with MPTCP pcap analysis (as [mptcptrace](https://bitbucket.org/bhesmans/mptcptrace) for instance).  Paper : Passive analysis for multipath TCP <https://github.com/teto/mptcpanalyzer>
1) **A collection tools for analysis and configuration of MPTCP**): <https://github.com/nasa/multipath-tcp-tools/>
1) Tstat tool : <http://tstat.polito.it/>
1) <https://github.com/multipath-tcp/mptcp-analysis-scripts>
1) visualize with **xplot.org**

**example :**
**


## **Hardware Tools**
- USB network adapters. ( Huawei LTE adapter to connect to the real LTE network)
- The eNodeBs are software-defined radios
- access points (wireless routers) are dual-band and capable of supporting IEEE 802.11a/g/n/ac

**Run traffic through VirtualBox VM with mptcp kernel ;**

<https://github.com/multipath-tcp/mptcp-virtual>

**OpenMPTCProuter: image to run on VM or raspberry**  

<https://www.openmptcprouter.com/>

OpenMPTCProuter is an open source solution to aggregate multiple internet connections using Multipath TCP (MPTCP) on OpenWrt

<https://github.com/Ysurac/openmptcprouter>

**Similar solution with open source images :**

<https://github.com/ovh/overthebox>






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

`	`mint	ulyssa	amd64 -> tested but not working

`	`mint	sarah	amd64 -> **actually working**

lxc configuration keys

https://linuxcontainers.org/lxc/manpages/man5/lxc.container.conf.5.html

`	`lxc.uts.name = left

`	`lxc.net.0.type = veth

`	`lxc.net.0.flags = up

`	`lxc.net.0.link = br-left

`	`lxc.net.0.ipv4.address = 10.0.0.1/24

**Configure interfaces** on host machine to connect lxc containers

sudo brctl addbr br-left

sudo brctl addbr br-right

sudo tunctl -t tap-left

sudo tunctl -t tap-right

sudo ifconfig tap-left 0.0.0.0 promisc up

sudo ifconfig tap-right 0.0.0.0 promisc up

sudo brctl addif br-left tap-left

sudo ifconfig br-left up

sudo brctl addif br-right tap-right

sudo ifconfig br-right up

**sudo brctl show**

**create linux container** with 2 interfaces (see .conf on scratch)

**sudo lxc-create -t download -n mycontainer -- -d ubuntu -r xenial -a amd64 --keyserver hkp://keyserver.ubuntu.com**

sudo chroot /var/lib/lxc/mp-left/rootfs/ passwd

sudo lxc-start -n left

ping -I eth0 -c 1 10.0.1.2

ifconfig eth1 down

ifconfig eth1 up

##### ***configure existing LXC containers***  
sudo nano /var/lib/lxc/right/config

sudo nano /var/lib/lxc/left/config

\# Distribution configuration

lxc.include = /usr/share/lxc/config/common.conf

\# For Ubuntu 14.04

lxc.mount.entry = /sys/kernel/debug sys/kernel/debug none bind,optional 0 0

lxc.mount.entry = /sys/kernel/security sys/kernel/security none bind,optional 0 0

lxc.mount.entry = /sys/fs/pstore sys/fs/pstore none bind,optional 0 0

lxc.mount.entry = mqueue dev/mqueue mqueue rw,relatime,create=dir,optional 0 0

lxc.arch = linux64

lxc.uts.name = mp-left

\# Network configuration

\# 1 interface

lxc.net.0.type = veth

lxc.net.0.flags = up

lxc.net.0.link = br-left

lxc.net.0.name = eth0

#lxc.net.0.hwaddr = 4a:49:43:49:x:x

lxc.net.0.ipv4.address = 11.0.0.2/8

#lxc.net.0.ipv4.gateway = 11.0.0.1

\# 2 interface

lxc.net.1.type = veth

lxc.net.1.flags = up

lxc.net.1.link = br-left-1

lxc.net.1.name = eth1

#lxc.net.1.hwaddr = 4b:49:43:49:x:x

lxc.net.1.ipv4.address = 15.0.0.2/8

#lxc.net.1.ipv4.gateway = 11.0.0.2

lxc.rootfs.path = dir:/var/lib/lxc/mp-left/rootfs

\# hooks

lxc.hook.mount = /var/lib/lxc/mp-left/ifup.sh


##### ***lxc-chekconfig :***
`	`**see required modules : [https://programmersought.com/article/78551456854/**](https://programmersought.com/article/78551456854/)**

Note : Before booting a new kernel, you can check its configuration

usage : CONFIG=/path/to/config /usr/bin/lxc-checkconfig

##### ***lxc-copy :***
copy container lxc-copy -n C1  -NC2
#####
##### ***provide  internet access to lxc container***
\# need to add link to

\# Network configuration

#lxc.net.0.type = veth

#lxc.net.0.link = lxcbr0

#lxc.net.0.flags = up

#lxc.net.0.hwaddr = 00:16:3e:41:1f:da

##### ***execute script on startup***

main discussion:  

<https://github.com/lxc/lxc/issues/496>

**crete script for linux containers**, and Make it executable (chmod 755): add hook to config file of container

lxc.hook.mount = /var/lib/lxc/mp-right/ifup.sh

`	`#!/bin/sh

#echo "ip route add 15.0.0.0/8 via 14.0.0.1 dev eth1"

ip route add 15.0.0.0/8 via 14.0.0.1 dev eth1

ip route add 11.0.0.0/8 via 13.0.0.1 dev eth0

\# do not need line below , just to ping test  

ip route add 16.0.0.0/8 via 14.0.0.1 dev eth1

sudo xed /var/lib/lxc/mp-left/ifup.sh

`	`#!/bin/sh

#echo "ip route add 15.0.0.0/8 via 14.0.0.1 dev eth1"

ip route add 14.0.0.0/8 via 15.0.0.1 dev eth1

ip route add 13.0.0.0/8 via 11.0.0.1 dev eth0


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
##### ***#see route***
ip route show

route

netstat -nr

##### ***#add route***
sudo ip route add 172.16.0.0/24 via 192.168.122.1 dev ens3

sudo route add -net 10.0.0.0/8 gw 192.168.1.1 eth0

\# add default route

sudo ip route add default via 192.168.1.254

\# add default route  to lxc container configuration file :

lxc.net.1.ipv4.gateway = 10.0.1.1

##### ***#delete route***
sudo route del -net 192.168.3.0 gw 192.168.1.1 netmask 255.255.255.0 dev eth0




#
# Using NS3
manual :

<https://www.nsnam.org/docs/dce/manual/ns-3-dce-manual.pdf>

### Installation in ***chroot jail <https://www.projectguideline.com/installing-ns3-35-in-debian-10-chroot-jail-under-debian-11-host-os-or-any-version-of-linux-host/>***
### Compilation
CXXFLAGS="-Wno-error"  

**# optimized**

`    `./waf configure --build-profile=optimized --out=build/optimized --enable-examples --disable-python --enable-tests --enable-sudo --disable-werror  

**# debug build**

./waf configure --build-profile=debug --out=build/debug --enable-examples --disable-python --enable-tests --enable-sudo --disable-werror  
### Common
##### ***Redirect to file***
./waf --run "m\_wave\_80211p" > log.out 2>&1

##### ***find text in code***
find . -name '\*.cc' | xargs grep NS\_LOG\_COMPONENT\_DEFINE | grep -i WifiNetDevice

find . −name ’ ∗ . cc ’ | xargs grep CourseChange | grep Connect

### GDB NS3  
**Start with: to load it in gdb**:

./waf --run "mp-tap-wifi-lte --simTime=60" --gdb

**Quick commands:**

Type **"run"** to start execution. When it fails, type **"bt"** to get backtrace on where you were going. Restart, but this time before "run" set breakpoints, e.g. **break mysource.cc:1234** to stop execution at that point. Then you can either type **n or s to step line-by-line through** the execution (s steps into function calls, n does not). You can use **'print varname**' to check contents of variables and pretty much anything else (for class variables, you might need to use 'print this->classvar').

### Interact with the real world
different possibilities :

<https://www.nsnam.org/wiki/HOWTO_make_ns-3_interact_with_the_real_world>

Virtual Machine

<https://www.nsnam.org/wiki/HOWTO_use_VMware_to_set_up_virtual_networks_(Windows)>

LInux Containers

<https://www.nsnam.org/wiki/HOWTO_Use_Linux_Containers_to_set_up_virtual_networks>

Combine **with CORE** network emulator: <https://www.nsnam.org/wiki/HOWTO_Use_CORE_to_test_ns-3_protocols>



**ns3-lxc project**, released on June 17, 2017.	<https://github.com/buzz66boy/ns3-lxc/wiki/Release-v0.1>
### NS3 Tools
**integration with mininet : [https://github.com/mininet/mininet/wiki/Link-modeling-using-ns-3**](https://github.com/mininet/mininet/wiki/Link-modeling-using-ns-3)**

**3d visualizer (new):**  <https://github.com/usnistgov/NetSimulyzer>

**Topology gen : <https://github.com/idaholab/Topology_Generator>**

##### ***NS3 V2X TOOLS***
1) ns-3 modules to build and simulate ETSI-compliant VANET (V2X) applications using SUMO (v-1.6.0+) and ns-3 (v-3.33), with the possibility of easily switching stack and communication technology. <https://github.com/marcomali/ms-van3t>

1) Vehicle simulation program capable of exporting traces in ns2/ns3 format <https://github.com/DenisCobeti/Mogen>

1) Co-simulate MATLAB with NS-3 network simulator, combining the powers of MATLAB and NS-3 (Supports modeling WLAN network and 802.11p based V2X scenarios) <https://github.com/vkrepo/MATLAB-NS3>

**Direct Code Execution (DCE)** is a framework for ns-3 that provides facilities to execute, within ns-3, existing implementations of userspace and kernelspace network protocols or applications without source code changes.

support only  Ubuntu 16.04 and equivalents (gcc-5.4, libc-2.23)

<https://github.com/direct-code-execution/ns-3-dce>










# Linux
## **Kernel**  
**install mainline kernel tool** : <https://github.com/pimlie/ubuntu-mainline-kernel.sh>

**install from source (this is good one):** <https://askubuntu.com/questions/718381/how-to-compile-and-install-custom-mainline-kernel/718662#718662>


**scripts/diffconfig  →** differences in config files

listen kernel

dmesg | tail -n1
#### **compile kernel**
`	`<http://mitchtech.net/compile-linux-kernel-on-ubuntu-12-04-lts-detailed/>

`	`<https://wiki.ubuntu.com/KernelTeam/GitKernelBuild>

**make localmodconfig** -use just modules actually loaded


error on build.

cp /boot/config-`uname -r` .config

yes '' | make oldconfig make clean

#remove the non git files

git clean -f

rm vmlinux-gdb.py

#make sure there is no linux\*.tar too

rm ../linux\*.gz

s# make -j `getconf \_NPROCESSORS\_ONLN` deb-pkg LOCALVERSION=-custom

cd ..

sudo dpkg -i linux-image-2.6.24-rc5-custom\_2.6.24-rc5-custom-10.00.Custom\_i386.deb

sudo dpkg -i linux-headers-2.6.24-rc5-custom\_2.6.24-rc5-custom-10.00.Custom\_i386.deb

sudo dpkg -i linux-\*.deb

sudo update-grub

**Note** : Before booting a new kernel, you can check its configuration

usage : CONFIG=/path/to/config /usr/bin/**lxc-checkconfig**

insert modules using make menuconfig :

- modules for bridge criation → **veth**                   
- modules for netem/tc  → **sch\_netem**              24576  2

#### **see available modules :**  
cat /lib/modules/5.4.0-80-generic/modules.order 	
#### **Remove kernel :**
sudo apt-get remove linux-image

sudo apt-get autoremove --purge


`	`<https://www.cyberciti.biz/faq/debian-redhat-linux-delete-kernel-command/>

#### kernel boot order :
`	`Also look in the /boot/grub/grub.cfg file for your kernel entry

**change :**

`	`sudo nano /etc/default/grub

`	`GRUB\_DEFAULT="gnulinux-advanced-018d23c0-5a19-42e8-b64b-2c5c557c839a>gnulinux-5.4.0-mptcp-advanced-018d23c0-5a19-42e8-b64b-2c5c557c839a"

#GRUB\_DEFAULT=0

`	`sudo update-grub

## **Process priority policies**
**How to Run A Command with a Given Nice Value: [-20 to 19]**

<https://www.tecmint.com/set-linux-process-priority-using-nice-and-renice-commands/>

$ nice -n niceness-value [command args]

OR

$ nice -niceness-value [command args] 	#it’s confusing for negative values

OR

$ nice --adjustment=niceness-value [command args]

**Change priority of running process :**

$ renice -n  -12  -p 1055		# for specific process

$ renice -n -2  -u apache   		# for user apache


## **Tools**
interact with kernel module :

<https://developer.ibm.com/articles/control-linux-kernel-extensions/>

netlink :

<https://insujang.github.io/2019-02-07/implementing-a-new-custom-netlink-family-protocol/>

**Timekeeper** :

<https://github.com/Vignesh2208/TimeKeeper/tree/TimeKeeper-4.0>

**ProgMP: Programmable MultiPath TCP Scheduling**

<https://progmp.net/>

<https://github.com/vandit86/mptcp_progmp/tree/progmp_master>

**TCP\_INFO** socket option :

`	`<https://linuxgazette.net/136/pfeiffer.html>


# Remote Host UMinho


**access : ssh vad@193.136.9.210  -p 22321**

**gestor vmware : 193.136.9.244**

**ssh** : <https://www.hanselman.com/blog/how-to-set-up-a-tab-profile-in-windows-terminal-to-automatically-ssh-into-a-linux-box>

PUTTY : <https://devops.ionos.com/tutorials/use-ssh-keys-with-putty-on-windows/>


reboot **periodically** : <https://askubuntu.com/questions/13730/how-can-i-schedule-a-nightly-reboot>

**connect VSCode :** <https://medium.com/@sujaypillai/connect-to-your-remote-servers-from-visual-studio-code-eb5a5875e348#:~:text=Connect%20to%20a%20remote%20host,file%20>(%2FUsers%2Fsujay%2F.

**Remote host connection** :

`	`from w8 : mstsc.exe

**check activity on the host** :

`	`/var/log/auth.log | grep sshd

`	`..bash\_history

**Initramfs boot issue :**

<https://linuxhint.com/fix-initramfs-ubuntu/#:~:text=The%20%E2%80%9Cinitramfs%E2%80%9D%20error%20can%20affright,%E2%80%9D%20or%20%E2%80%9Cfsck%E2%80%9D%20utility>.


**Antivirus and rootkits**  



ClamAV <https://upcloud.com/community/tutorials/scan-ubuntu-server-malware/>

use the following command:  *sudo clamscan -r /home*

sudo freshclam && sudo clamscan –log=”/home/vad/ClamAv.log” --infected --recursive --exclude-dir="^/sys" --exclude-dir=”^/home/vad/ns-3-dev”  --exclude-dir=“^/home/vad/linux” /


multiple dir to exclude <https://serverfault.com/questions/957666/how-to-make-clamdscan-exclude-folders-and-only-log-infected>

--exclude-dir="/tmp/testClam/test/|/tmp/testClam/test1/"






# MPTCP
## **Implementations**

**MultiPath TCP - Linux Kernel implementation Release0. 95 :** site with a lot of information about MPTCP usage, configuring, installing, code , and tools links  

<http://multipath-tcp.org/pmwiki.php?n=Main.Release95>

**\*github page of MULTIPATH TCP <https://github.com/orgs/multipath-tcp/repositories>**

**site of ip networking lab (developers of MPTCP kernel implementation) with many userful software and links.**

<https://inl.info.ucl.ac.be/softwares.html>

### Path management
Intel **mptcpd [https://github.com/intel/mptcpd**](https://github.com/intel/mptcpd)**

Userspase path management for the **linux multipath TCP kernel,** starting from version v0.95.

<https://github.com/teto/mptcp-pm>

example (old)

<https://github.com/brenns10/pathmand/tree/master>


## **Installation from source**
official DIY : <https://multipath-tcp.org/pmwiki.php/Users/DoItYourself>

After you've done the git clone ..., you'll have an mptcp directory that has all the kernel sources. **Copy *your* .config into it.**

Then, do all the make xconfig, etc. from the ubuntu guide and follow the DIY to enable the mptcp stuff.

Then, you'll need to do make and make modules, etc., following the instructions for your *distro*

- get kernel source-> go to kernel folder  
- *cp -vi /boot/config-`uname -r` .config*  -> use your default .config from previous kernel
- *make* oldconfig  # old config enable
- *make* localmodconfig  # now disable all the modules not currently loaded.
- *make xconfig*  ->> configure options MPTCP as in instruction additionally on .config file

CONFIG\_LOCALVERSION=""EnterVersionHere""

CONFIG\_LOCALVERSION\_AUTO=y

Additionally, there's a configuration option CONFIG\_LOCALVERSION, to be found under General Setup -> Local version - append to kernel release in make menuconfig that is appended to this.

- *make -j5*     
- *sudo make modules\_install*  or make INSTALL\_MOD\_STRIP=1 modules\_install  (to reduce size)
- *sudo make install*
- *reboot*  to new kernel  

Tuttorial : <https://github.com/qdeconinck/sigcomm20_mptp_tutorial>
## **Configure MPTCP**
- <http://blog.multipath-tcp.org/blog/html/index.html>
- <http://multipath-tcp.org/pmwiki.php/Users/ConfigureMPTCP> (CONFUGURE MPTCP)
- <http://book.itep.ru/depository/mptcp/MultiPath_TCP-Linux_Kernel_implementation_Main-50_Gbps_browse.html>
- <https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_and_managing_networking/getting-started-with-multipath-tcp_configuring-and-managing-networking>


##### ***Config files located on :***
- ll /proc/sys/net/mptcp/
- cat /boot/config-4.19.126.mptcp | grep -i mptcp
- sudo sysctl -a | grep mptcp


- Fulmesh path-manager
- OLIA congestion control:  to respect MPTCP design goals. is also a window-based congestion control algorithm and compensates for different RTTs
- Set routing configuration for two physical interfaces.

*• **Path management** is responsible for detecting and using the available paths between two hosts. It is also responsible for the mechanism of signaling alternative addresses to hosts and to set up new sub-flows joined to an existing Multi-Path TCP connection*

*• **At the packet scheduling**, the connection-level re-ordering is performed when the packets are received from the TCP sub-flows. To allow the correct ordering of the segments sent on the different sub-flows.*

*• **Congestion control** function is responsible for coordinating the congestion control across the sub-flows. This coordination is responsible for scheduling which segments should be sent on which sub-flow and at what rate, is also a part of packet scheduling.*

*• **Sub-flow interface** is responsible to transmit on the specified path, the segments received from the packet scheduling component. Upon receiving a segment, the sub-flow passes the data to the packet scheduling for connection-level reassembly.*

##### ***enable/disable mptcp***
ssctl -w net.mptcp.mptcp\_enabled=1

dmesg | grep MPTCP

sudo sysctl -a | grep mptcp

less /boot/config-4.14.24.mptcp

find /lib/modules/$(uname -r) -name "\*lia.ko"

##### ***mptcp path manager***
**'fullmesh' must be used**

'default' do not do nothing

‘ndiffports’  many subflows acros the same pair of IP

sudo sysctl net.mptcp.mptcp\_path\_manager  

/sys/module/mptcp\_fullmesh/parameters/num\_subflows (value > 1)

##### ***mptcp scheduler***
##### sudo sysctl net.mptcp.mptcp\_scheduler

'default' - lowest RTT first

‘redundant’ - redundant transmit on all paths

ROUNDROBIN

##### ***Disable one interface for MPTCP (or put it in backup-mode)***
Note that it is also possible not to use the modified version of IPRoute2 and append the flag 0x80000 (Multipath Off) and 0x100000 (backup) in /sys/class/net/<INTERFACE>/flags

check **backup  flag** on the  SYN packet on wireshark



**enable backup  lXC container :**  echo 0x101003 > /sys/class/net/eth0/flags

revert to default : echo 0x1003 > /sys/class/net/eth0/flags

#### Problem to resolve
Problem issue : mptcp stuff is configured kernel wide and not namespace aware

[**https://issueexplorer.com/issue/lxc/lxc/3869**](https://issueexplorer.com/issue/lxc/lxc/3869)

**Cannot set specific path manager in specific LXC :**

root@left:/#  sysctl -w net.mptcp.mptcp\_path\_manager=fullmesh

sysctl: cannot stat /proc/sys/net/mptcp/mptcp\_path\_manager: No such file or directory

**different /proc mounted**

vad@mp-vm:~/ns-3-dev$ ll /proc/sys/net/

core/             ipv4/             ipv6/             mptcp/            netfilter/        nf\_conntrack\_max  unix/

root@left:/# /proc/sys/net/

core/      ipv4/      ipv6/      netfilter/ unix/

resolve :

<https://discuss.linuxcontainers.org/t/proc-sys-change-in-unprivilege-container/771>

[~~https://itectec.com/ubuntu/ubuntu-modifying-sysctl-values-inside-lxc-container/](https://itectec.com/ubuntu/ubuntu-modifying-sysctl-values-inside-lxc-container/)  ~~modify conf file~~

1) !

lxc.sysctl.net.mptcp.mptcp\_path\_manager=fullmesh

lxc.sysctl.net.mptcp.mptcp\_enabled=0



1) ?

lxc.mount.entry

LXCFS

sudo apt install tcpdump iperf3

/var/lib/lxc/mycontainer/rootfs/home/ubuntu/

bind mount

<https://discuss.linuxcontainers.org/t/can-application-in-lxc-listen-to-the-netlink-message-from-the-host-kernel/1442>

<https://gist.github.com/julianlam/07abef272136ea14a627>




### **TCP tuning**

<https://fasterdata.es.net/host-tuning/linux/>

The **cwnd of this path should be set as 0** which indicates it is **unable for data transmission**

TCP performance depends on the product of the **transfer rate** and the **round-trip delay**

retransmission timeout (RTO) interval is essential to TCP performance. RTO is determined by estimating the mean and variance of the measured round-trip time (RTT)
##### ***linux network tuning links***

- <https://wenxueliu.github.io/blog/10/10/2014/linux-tuning>			(to read)
- <https://habr.com/ru/company/webo/blog/327050/>
- <https://tools.ietf.org/html/draft-stenberg-httpbis-tcp-03>				(tcp tuning for http)
- <https://fasterdata.es.net/host-tuning/linux/test-measurement-host-tuning/> 	(configure  test host)
- [TCP_NODELAY & Nagle's Algorithm | ExtraHop](https://www.extrahop.com/company/blog/2016/tcp-nodelay-nagle-quickack-best-practices/)  				(TCP\_NODELAY)



##### ***Congestion control***

**# check available congestion control**

sysctl net | grep congestion

**add congestion control**

sudo modprobe mptcp\_balia

add permanently :

vad@vad-VB:~/ns-3-dev$ echo mptcp\_balia | sudo tee -a /etc/modules

vad@vad-VB:~/ns-3-dev$ echo mptcp\_olia | sudo tee -a /etc/modules

vad@vad-VB:~/ns-3-dev$ echo mptcp\_wvegas | sudo tee -a /etc/modules

**set congestion control**

sudo sysctl net.ipv4.tcp\_available\_congestion\_control

net.ipv4.tcp\_congestion\_control=cubic

`	`sysctl net.ipv4.tcp\_congestion\_control=reno
#####
##### ***window scaling***
максимальный размер окна приема в TCP равен 64 КБ, если только не включено масштабирование окна

sudo sysctl -w net.ipv4.tcp\_window\_scaling=0

##### ***socket buffer size***
sysctl net.ipv4.tcp\_rmem

sysctl net.ipv4.tcp\_wmem
#####
For the TCP buffer size, we change the maximum receive/send buffer memory specified in tcp\_rmem and tcp\_wmem

##
## **MPTCP v1**

<https://github.com/multipath-tcp/mptcp_net-next/wiki>

available in mainline kernel

**Net Namespaces configuration** : <https://www.inf.usi.ch/faculty/carzaniga/edu/adv-ntw/mininet.pdf>

**run ns bash**

sudo ip netns exec right bash

**mptcpize**  tool  ??

**use\_mptcp  [https://github.com/pabeni/mptcp-tools**](https://github.com/pabeni/mptcp-tools)**

An utility to force a non MPTCP-enabled application to use MPTCP instead of TCP.

Usage:

**./use\_mptcp.sh <app> <app command line>**

It will build on the fly wrapper library to hijack the socket() libcall, requires gcc and make

sh /home/vad/mptcp-tools/use\_mptcp/use\_mptcp.sh  iperf3 -s &

### path manager
**iproute2** new version (supports mptcp)->

<https://git.kernel.org/pub/scm/network/iproute2/iproute2.git/>

ip mptcp (man page)-> <https://man7.org/linux/man-pages/man8/ip-mptcp.8.html>  

(iproute2 how to use) <https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_and_managing_networking/getting-started-with-multipath-tcp_configuring-and-managing-networking>

1. Set the per connection and IP address limits to 1 on the server:
   # ip mptcp limits set subflow 1
1. Set the per connection and IP address limits to 1 on the client:
   # ip mptcp limits set subflow 1 add\_addr\_accepted 1
1. Add IP address 198.51.100.1 as a new MPTCP endpoint on the server:
   # ip mptcp endpoint add 198.51.100.1 dev enp1s0 **signal**

**signal**, sends an ADD\_ADDR packet after the three-way-handshake is completed (on server)

**subflow**, sends an MP\_JOIN SYN by the client (on client)

**backup**, sets the endpoint as a backup address ()



Usage:  **ip mptcp** endpoint add ADDRESS [ dev NAME ] [ id ID ]

`                                      `[ port NR ] [ FLAG-LIST ]

`        `ip mptcp endpoint delete id ID

`        `ip mptcp endpoint show [ id ID ]

`        `ip mptcp endpoint flush

`        `ip mptcp limits set [ subflows NR ] [ add\_addr\_accepted NR ]

`        `ip mptcp limits show

`        `ip mptcp monitor

FLAG-LIST := [ FLAG-LIST ] FLAG

FLAG  := [ signal | subflow | backup ]



#### **mptcpd**

./mptcpd  --debug --path-manager=sspi  --addr-flags=subflow  

-"**addr-flags**" are used when announcing an IP address (See the ip-mptcp(8))

-"**notify-flags**" provides for further control over how plugins are

`  `notified of changes to local IP addresses.  See the mptcpd(8) man

`  `page shipped with this mptcpd release for further details.

1) running  mptcpd with --addr-flags=subflow, will change limits of mptcp protocol : limits are changed to add\_addr\_accepted 2 subflows 2


git -> <https://github.com/intel/mptcpd>

**install** from .tar.gz (release 0.8) :

`	`mkdir the\_build

cd the\_build

../configure

make

uninstall:  **make uninstall** in folder og mptcpd 	

make distclean

`	`<https://gist.github.com/ruario/a36052a1ae1de4edbc6ad39fe39e5385>

/usr/bin/mkdir -p '/usr/local/etc/mptcpd'

/usr/bin/install -c -m 644 **mptcpd.conf** '**/usr/local/etc/mptcpd'**

default config file

**RUN MPTCP**

sudo ./mptcpd  

**select path manager (plugin dir)**

./mptcpd --plugin-dir=/home/vad/mptcpd-0.8/build/plugins/path\_managers/.libs

**client :**

root@mp-vm:/home/vad/mptcpd-0.8/build/src# ip mptcp limits

add\_addr\_accepted 2 subflows 2

**create new subflow for each  accepted add\_addr option**

./mptcpd -d --path-manager=sspi --plugin-dir=/home/vad/mptcpd-0.8/build/plugins/path\_managers/.libs  --addr-flags=subflow


**outside netns**

sudo ip netns exec left ./home/vad/mptcpd-0.8/build/src/mptcpd -d --plugin-dir=/home/vad/mptcpd-0.8/build/plugins/path\_managers/.libs  --addr-flags=subflow,signal --notify-flags=existing,skip\_link\_local,skip\_loopback


vad@mp-vm:~$ ll /lib/systemd/system | grep mpt

-rw-r--r--  1 root root   407 Sep 23 09:13 **mptcp.service**

**sudo systemctl daemon-reload**

**sudo systemctl start mptcp.service**

journalctl | tail → error apears on journal iof service not started

` `systemctl list-units -a  → see running services


Libraries have been installed in:

`   `**/usr/local/lib/mptcpd**


while trying to run bitwarden\_rs in a LXC container and solved it by removing the **LIMITNPROC** line in the systemd file

**#LimitNPROC=1**


libtool: finish: PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin:/sbin" ldconfig -n /usr/local/lib



If you ever happen to want to link against installed libraries

in a given directory, LIBDIR, you must either use libtool, and

specify the full pathname of the library, or use the '-LLIBDIR'

flag during linking and do at least one of the following:

`   `- add LIBDIR to the 'LD\_LIBRARY\_PATH' environment variable

`     `during execution

`   `- add LIBDIR to the 'LD\_RUN\_PATH' environment variable

`     `during linking

`   `- use the '-Wl,-rpath -Wl,LIBDIR' linker flag

`   `- have your system administrator add LIBDIR to '/etc/ld.so.conf'

See any operating system documentation about shared libraries for

more information, such as the ld(1) and ld.so(8) manual pages.









# QUIC

see <https://docs.google.com/document/d/1ClFLDEqQJJe-UF3-zD4Sz9NmSTUouwgvg0nsBznWal4/edit#heading=h.cud00sdmz1ua>

