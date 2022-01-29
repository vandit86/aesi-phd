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

## Tools
interact with kernel module : https://developer.ibm.com/articles/control-linux-kernel-extensions/ 

netlink : https://insujang.github.io/2019-02-07/implementing-a-new-custom-netlink-family-protocol/ 

Timekeeper : https://github.com/Vignesh2208/TimeKeeper/tree/TimeKeeper-4.0

**ProgMP: Programmable MultiPath TCP Scheduling**
https://progmp.net/
https://github.com/vandit86/mptcp_progmp/tree/progmp_master

**TCP_INFO socket option :** 
	https://linuxgazette.net/136/pfeiffer.html 
