# How to test with our emulation setup : 

run emulation setup : namespaces + ns3 + CORE

1. initialize pere configured namespaces 
`sudo sh ./examples/multipath/namespace-setup.sh` 

2. Inside ‘left’  NS start mptcp deamon with our plugin. 
Plugin will **start to listen** commands on FIFO pipe, commands are specified on mptcp_ns3.h inside mptcpd project directory..   
By receiving Test_Cmd from ns3, **plugin start tcpdump session**, and s**ave dump to files**  

`./mptcpd-0.8/build/src/mptcpd -d --plugin-dir=/home/vad/mptcpd-0.8/build/plugins/path_managers/.libs --path-manager=sspi`

3. Run ns3 :

**exemple how to run**
> `sudo ./waf --run "v2i-multipath --simTime=60 --sumo-gui=false  --use-mptcpd --iperf=30 --iperfStart=10 --tcpdump"`

run `./waf --run "v2i-multipath --PrintHelp"`  to see all options 

> -simTime, Total duration of the simulation [s]
> 
> -sumo-gui, "Use SUMO gui or not", 
>
> --maxSpeed: initial max veh speed [km/h] (defaul 25)
>
> --changeVehMaxSpeed:  Change vehicle maximum speed randomly from [10:100]km/h [false]
> 
> –use-mptcp : set if you need to use netlink mptcp PM plugin (threashold -75 dBm, change backup flag of WLAN)
> 
> –-iperf=30  : if you want that _LEFT_ run iperf session for 30 sec (defaul 0)
> 
> --iperfStart: Set initial time to generate traffic, default 1
>
> --iperfRepeat:  Repeat iperf sessions umtil simulation end, the time of sessio is random value [10:100]sec [false]
> 
> --"tcpdump", Capture traffic on vehicle interfaces (client namespace) during **simTime-1** 
>
> --"verbose", Print debug data on console every sec, default _true_ 
>
> 
4. After simulation end on ‘left’ NS, **merge dump** of 2 ifaces : 
`mergecap dump-0.pcap dump-1.pcap -w /home/vad/ns-3-dev/dump-merged.pcap`

5. Analyze , **Plot** captured mptcp session with. Limitation: just for one connection see issue  

`~/multipath-tcp-tools/network-traffic-analysis-tools/mptcpplot -yx  ~/ns-3-dev/dump-merged.pcap`

# Genarate MPTCP traffic 

simple client/server MPTCP traffic generator to measure one way application delay 
Files in : _/home/vad/mptcp-traffic-gen/_




