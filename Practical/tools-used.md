- SW tools
    - [Mwasure latency](#latency) 
    - [Emulation ](#emulation)
    - [Link statistics](#link)
    - [Traffic Generator](#traffic)
    - [Mptcp analysis tools](#anal)
    - [Other](#other)
- [HW tools](#hw)

# Tools

## SW Tools
#### GIT

configure access to repo with new token system: 
<https://stackoverflow.com/questions/68775869/support-for-password-authentication-was-removed-please-use-a-personal-access-to>

#### Grep

find a pattern in source code :
`grep --include=\\*.{c,h} -rn '/path/to/somewhere/' -e "pattern"`

### Measure latency <a name="latency"></a>
#### Ping
> -i  :  interval  
> -s : packet size  
> -f : flooding mode  
> -D : put timestamp on output  

#### CAIA
We calculated RTT using the S**ynthetic Packet Pair (SPP**). <http://caia.swin.edu.au/tools/spp/>

### Emulation config <a name="emulation"></a> 

- <https://habr.com/ru/post/24046/>  (**Эмуляция влияния глобальных сетей**)

#### netem / tc command
linux traffic control : to vary latency and packet loss rate
- <https://wiki.linuxfoundation.org/networking/netem>
- <https://netbeez.net/blog/how-to-use-the-linux-traffic-control/>

 `tc qdisc add dev eth0 root netem delay 200ms` - Network delay 

 `tc qdisc show  dev eth0`  - display active rules

`tc qdisc del dev eth0 root` **delete all rules**  : 

`tc qdisc add dev eth0 root netem loss` 10% **Packet loss :**   
`tc qdisc add dev eth0 root tbf rate 1mbit burst 32kbit latency 400ms `**bandwidth limit :** 

combine multiple impairments ..
<https://www.excentis.com/blog/use-linux-traffic-control-impairment-node-test-environment-part-2>

##### Dummynet
like tc tool
<http://info.iet.unipi.it/~luigi/dummynet/>


### Link statistics <a name="link"></a>

#### Tstat tool  
<http://tstat.polito.it/>

#### tcpdump
Capture traffic using tcpdump. I would capture in 3 terminals or by backgrounding the command with &
The flags -nn turns off dns resolution for speed, -s 0 saves the full packet and -w writes to a file.

`tcpdump -i wan0 -nn -s 0 -w wan0.dump`

I would **then merge** the files with the mergecap command from wireshark:

`mergecap -w merged.dump wan0.dump wan1.dump lan0.dump`

#### _SS_ tool   
<https://www.tecmint.com/ss-command-examples-in-linux/>

After the connection is established, verify the ss output to see the subflow-specific status:
`ss -nti '( dport :4321 )' dst 192.0.2.1`

get TCP related values using `ss` tool :
`ss -ite 'src 1.1.1.1:1234 and dst 2.2.2.2:1234'`  

the values to be monitored (see ``man ss`` for explanation) :
- rcv_rtt
- bytes_sent
- bytes_receive
- **in fly packets  on each subflow??**  
- ...

#### Netstat
- `netstat -tulpen` -you can also check if port is open or not
- `netstat -i` - see the number of packets sent from each interface
- `netstat -s`  -statistics 


### NIC control (Ethertool)

**switch off** tcp checksum
`ethtool --offload  eth0  rx off  tx off`

ack (and thus retransmission) are caused by actual drop at the NIX RX queue level:
<https://github.com/multipath-tcp/mptcp_net-next/issues/253>
the following workaround is effective in my setup:

_ethtool -G enp37s0f0np0 rx 4096_


### Traffic Generator  <a name="traffic"></a>

***fallocate***   
create file with arbitrary size
`*fallocate -l 10M gentoo\_root.img*`

#### ***Netperf***
is able to fully use one interface; netserver  on server side

`netperf -t omni -H 10.1.10.2 -l 30 -T 1/1  -c -C -- -m 512k -V`

What are these options?
- -t omni specifies the netperf-test to use. This sends a single data-stream between two hosts.
- -H 10.1.10.2 the destination-server's IP address
- -l 30 the duration of the test (30 seconds)
- -T 1/1 to pin the applications on the client and the server side to CPU 1.
- -c -C output statistics on the CPU-usage at the end of the test
- -m 512k specifies the size of the send-buffer
- -V use zero-copy send and receive

#### ***Netcat***

Start by using nc to listen on a specific port, with output captured into a file:

`nc -l 1234 > filename.out`

Using a second machine, connect to the listening nc process, feeding it the file which is to be transferred

`nc host.example.com 1234 < filename.in`

After the file has been transferred, the connection will close automatically.

 **monitor throughput**  <https://saurorja.org/2011/07/25/network-throughput-measurement-using-netcat/>
 
`nc -l -p 1852 > /dev/null` 			# server  

`cat /dev/zero | pv -r | nc 192.168.1.200 1852` 	# client


#### ***Iperf (v2, v3)***
[**https://fasterdata.es.net/performance-testing/network-troubleshooting-tools/iperf/**](https://fasterdata.es.net/performance-testing/network-troubleshooting-tools/iperf/)

**Data traffic is generated using the iperf tool** : which allows the adjustment of TCP-related parameters like receiver buffer size and data volume size

**iperf3 :** Iperf3 is used to generate traffic but also to record the reached throughput. It furthermore exposes some important options that allow us to specify what congestion control algorithm it should use and how frequent it should report the current throughput

`iperf3 -s`   				# server side
`iperf3 -c 192.0.2.1`  			# client side
`iperf3 -c 14.0.0.2 -C olia -t 30 -R  -Z`

**Comparison iperf vs iperf3:**  <https://iperf2.sourceforge.io/IperfCompare.html>  

**iperf**   is better for mptcpd (no additional traffic is generated when connection is closed)

<https://netbeez.net/blog/linux-iperf-2-release/>

**/proc/net/dev** and **netstat** report the expected performance superiority


#### ***tens: new traffic generation tool***
<https://github.com/vmware/te-ns>

#### ***TRex***
Realistic Traffic Generator <https://trex-tgn.cisco.com/>



### Mptcp analysis tools <a name="anal"></a>
- (bad) **mptcptrace** : [mptcptrace](https://bitbucket.org/bhesmans/mptcptrace)  last commit 2014/15 (not verified)
- (not testet with v1) **Mptcpanalyzer** is a python tool conceived to help with MPTCP pcap analysis (as [mptcptrace](https://bitbucket.org/bhesmans/mptcptrace) for instance).  Paper : Passive analysis for multipath TCP <https://github.com/teto/mptcpanalyzer>
- (Good!) **A collection tools for analysis and configuration of MPTCP**): <https://github.com/nasa/multipath-tcp-tools/>
- (Not tested )**de Qoick** <https://github.com/multipath-tcp/mptcp-analysis-scripts>

- `tcptrace -S -zxy merged-1.pcap`   # generate time-seq graph
- `tcptrace -T -A50 -y -zxy merged-1.pcap` # generate tput graphs  
- visualize with **xplot.org**


### Other <a name="other"></a>

**bwm tool**
which is a bandwidth monitoring tool for Linux

**bmon (Bandwidth monitor tool)**
`bmon -p eth0,eth1 -b`

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

**Tstat** <http://tstat.polito.it/measure.shtml>




## **Hardware Tools** <a name="hw"></a>
- USB network adapters. ( Huawei LTE adapter to connect to the real LTE network)
- The eNodeBs are software-defined radios
- access points (wireless routers) are dual-band and capable of supporting IEEE 802.11a/g/n/ac

**Run traffic through VirtualBox VM with mptcp kernel ;**
<https://github.com/multipath-tcp/mptcp-virtual>

**OpenMPTCProuter: image to run on VM or raspberry**  
<https://www.openmptcprouter.com/>

OpenMPTCProuter is an open source solution to aggregate multiple internet connections using Multipath TCP (MPTCP) on OpenWrt
<https://github.com/Ysurac/openmptcprouter>

**Similar solution with open source images :** <https://github.com/ovh/overthebox>








