# How to test with our emulation setup : 

run emulation setup : namespaces + ns3 + CORE

1. initialize pere configured namespaces 
`sudo sh ./examples/multipath/namespace-setup.sh` 

2. Inside ‘left’  NS start mptcp deamon with our plugin. 
Plugin will **start to listen** commands on FIFO pipe, commands are specified on mptcp_ns3.h inside mptcpd project directory..   
By receiving Test_Cmd from ns3, **plugin start tcpdump session**, and s**ave dump to files**  

`./mptcpd-0.8/build/src/mptcpd -d --plugin-dir=/home/vad/mptcpd-0.8/build/plugins/path_managers/.libs --path-manager=sspi`

3. Run ns3 :

> `sudo ./waf --run "v2i-multipath --simTime=60 --sumo-gui=false  --use-mptcpd --iperf=30"`
> 
> -simTime, Total duration of the simulation [s]
> 
> -sumo-gui, "Use SUMO gui or not", 
> 
> –use-mptcp : set if you need to use netlink mptcp PM plugin (threashold -75 dBm, change backup flag of WLAN)
> 
> –iperf=30  : if you want  that mptcpd run iperf session for 30 sec
> 
> -iperfStart: Set initial time to captute traffic, default 1
> 
> -"tcpdump", Capture traffic on vehicle interfaces (client namespace) during **simTime-1** 
> 
> run `./waf --run "v2i-multipath --PrintHelp"`  to see all options 
> 
4. After simulation end on ‘left’ NS, **merge dump** of 2 ifaces : 
`mergecap dump-0.pcap dump-1.pcap -w /home/vad/ns-3-dev/dump-merged.pcap`

5. Analyze , **Plot** captured mptcp session with. Limitation: just for one connection see issue  

`../network-traffic-analysis-tools/mptcpplot -yx  ~/ns-3-dev/dump-merged.pcap`
