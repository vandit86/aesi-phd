# Linux

## Kernel  

### install from source (New) 
  (this is good one): https://askubuntu.com/questions/718381/how-to-compile-and-install-custom-mainline-kernel/718662#718662 
  
  **differences in config files**:  ``` scripts/diffconfig ```  

**use just modules actually loaded** : `make localmodconfig`  

 **listen kernel** 
  ```dmesg | tail -n1```

### compile kernel (old)

> 	http://mitchtech.net/compile-linux-kernel-on-ubuntu-12-04-lts-detailed/
> 	
> 	https://wiki.ubuntu.com/KernelTeam/GitKernelBuild

**if error on build:** 
```
cp /boot/config-`uname -r` .config 
yes '' | make oldconfig make clean 
#remove the non git files 
git clean -f 
rm vmlinux-gdb.py 
#make sure there is no linux*.tar too 
rm ../linux*.gz 
s# make -j `getconf _NPROCESSORS_ONLN` deb-pkg LOCALVERSION=-custom 
cd ..
sudo dpkg -i linux-image-2.6.24-rc5-custom_2.6.24-rc5-custom-10.00.Custom_i386.deb
sudo dpkg -i linux-headers-2.6.24-rc5-custom_2.6.24-rc5-custom-10.00.Custom_i386.deb
sudo dpkg -i linux-*.deb 
sudo update-grub
```



**Note:** Before booting a new kernel, you can check its configuration
`CONFIG=/path/to/config /usr/bin/lxc-checkconfig`

### Load modules to kernel 
see available modules: `cat /lib/modules/5.4.0-80-generic/modules.order `

**insert modules using make menuconfig :** 
> modules for bridge criation → veth                   
> modules for netem/tc  → sch_netem              24576  2

### Remove kernel : 
`sudo apt-get remove linux-image`
`sudo apt-get autoremove --purge`
https://www.cyberciti.biz/faq/debian-redhat-linux-delete-kernel-command/ 

### kernel boot order : 
Also look in the `/boot/grub/grub.cfg` file for your kernel entry 

change : 
> 	`sudo nano /etc/default/grub`
> 	GRUB_DEFAULT="gnulinux-advanced-018d23c0-5a19-42e8-b64b-2c5c557c839a>gnulinux-5.4.0-mptcp-advanced-018d23c0-5a19-42e8-b64b-2c5c557c839a"
> #GRUB_DEFAULT=0
>  `sudo update-grub`

## Process priority policies 
How to Run A Command with a Given Nice Value: [-20 to 19]
https://www.tecmint.com/set-linux-process-priority-using-nice-and-renice-commands/

$ nice -n niceness-value [command args] 
OR
$ nice -niceness-value [command args] 	#it’s confusing for negative values
OR
$ nice --adjustment=niceness-value [command args]

Change priority of running process : 
> $ renice -n  -12  -p 1055		# for specific process 
> $ renice -n -2  -u apache   		# for user apache

## **TCP tuning**

configure TCP RTO-related values (If nedded..) https://docs.oracle.com/cd/E19120-01/open.solaris/819-2724/chapter4-69/index.html 
https://www.kernel.org/doc/Documentation/networking/ip-sysctl.txt 

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

**TCP_INFO socket option :** 
	https://linuxgazette.net/136/pfeiffer.html 

