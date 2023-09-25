# How to test with our emulation setup : 

run emulation setup : namespaces + ns3 + CORE

1. initialize pere configured namespaces 
`sudo sh ./examples/multipath/namespace-setup.sh` 

1a. run interactive mode bash session on left 
> `sudo ip netns exec left bash`

1b. On "left"  enable userspace pm with 
>`echo 1 > /proc/sys/net/mptcp/pm_type`

check mptcp limits  `ip mptcp lim`, not nedded if use sspi plugin 

2. Inside ‘left’  NS start mptcp deamon with our plugin. 
Plugin will **start to listen** commands on FIFO pipe, commands are specified on mptcp_ns3.h inside mptcpd project directory..   
> `/home/vad/mptcpd/build/src/mptcpd --plugin-dir=/home/vad/mptcpd/build/plugins/path_managers/.libs --path-manager=sspi `

now One can send commands "manually" to controll existing subflows, for example :  ... todo ...
this can be used with CORE experiments ... 

3. Run ns3 :

**exemple how to run**
> `sudo ./waf --run "v2i-multipath --simTime=60  --use-mptcpd --iperf=30 --iperfStart=10 --tcpdump"`

run `./waf --run "v2i-multipath --PrintHelp"`  to see all options 

> --simTime, Total duration of the simulation [s]
> 
> --sumo-gui : "Use SUMO gui or not" (false) 
>
> --maxSpeed : initial max veh speed [km/h] (25)
> 
> --send-cam : enable CA service on vehicles and RSU (default **true**), if disabled UDP traffic will be generated just on RSU (beacons) 
> 
> --changeVehMaxSpeed=10 :  This will change the maximul veh speed every 10 sec. randomly from [40:120]km/h. if [0] max speed is not changed during the simulation. to simulate dinamism of vehicle movements.. 
> 
> –-use-mptcpd : set [true] if you whant to use netlink mptcp PM plugin (threashold -75 dBm, change backup flag of WLAN)
> 
> –-iperf=30  : if you want that _LEFT_ run iperf session for 30 sec (defaul 0)
> 
> --iperfStart: Set initial time to _LEFT_ starts to generate the traffic, used in combination with _iperf_ option, (default 1)
>
> --iperfRepeat:  Repeat iperf sessions umtil simulation end, the time of sessio is random value [10:100]sec [false]
> 
> --tcpdump, Capture traffic on vehicle interfaces (client namespace)
>
> --verbose, Print debug data on console every sec [true] 
>
> --inter-data:         interval in ms to send data message to mptcpd plugin [200]
>
> 
4. (MERGE NO MORE NEDDED) After simulation end on ‘left’ NS, **merge dump** of 2 ifaces : 
`mergecap dump-0.pcap dump-1.pcap -w /home/vad/ns-3-dev/dump-merged.pcap`

5. Analyze , **Plot** captured mptcp session with. Limitation: just for one connection see issue  

`~/multipath-tcp-tools/network-traffic-analysis-tools/mptcpplot -yx  ~/ns-3-dev/dump-merged.pcap`

plot with xplot tool 
'xplot.org connection_0-ORIGIN.xpl'

(optional : if needed)
convert to to .plt with xpl2plt tool, and plot with gnuplot , (example in OneDrive/results/xpl/gpl/) 

# Genarate MPTCP traffic 

simple client/server MPTCP traffic generator to measure one way application delay 
Files in : _/home/vad/mptcp-traffic-gen/_




