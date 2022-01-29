# MPTCP 
## instalation 
**MultiPath TCP** - Linux Kernel implementation Release0. 95 : site with a lot of information about MPTCP usage, configuring, installing, code , and tools links 
http://multipath-tcp.org/pmwiki.php?n=Main.Release95

**github** page of MULTIPATH TCP https://github.com/orgs/multipath-tcp/repositories 

**site of ip networking lab** (developers of MPTCP kernel implementation) with many userful software and links:  
https://inl.info.ucl.ac.be/softwares.html

**Installation** from source official DIY : https://multipath-tcp.org/pmwiki.php/Users/DoItYourself

**Tuttorial** https://github.com/qdeconinck/sigcomm20_mptp_tutorial 

## Configure MPTCP 
http://blog.multipath-tcp.org/blog/html/index.html
http://multipath-tcp.org/pmwiki.php/Users/ConfigureMPTCP (CONFUGURE MPTCP)
http://book.itep.ru/depository/mptcp/MultiPath_TCP-Linux_Kernel_implementation_Main-50_Gbps_browse.html
https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_and_managing_networking/getting-started-with-multipath-tcp_configuring-and-managing-networking

**Config files located on :** 
> ll /proc/sys/net/mptcp/
> cat /boot/config-4.19.126.mptcp | grep -i mptcp
> sudo sysctl -a | grep mptcp

**enable/disable mptcp** 
> ssctl -w net.mptcp.mptcp_enabled=1
> dmesg | grep MPTCP
> sudo sysctl -a | grep mptcp
> less /boot/config-4.14.24.mptcp
> find /lib/modules/$(uname -r) -name "*lia.ko"
 
mptcp path manager: 'fullmesh' must be used, 'default' do not do nothing

**‘ndiffports’**  many subflows acros the same pair of IP 
> sudo sysctl net.mptcp.mptcp_path_manager  
> /sys/module/mptcp_fullmesh/parameters/num_subflows (value > 1)

**mptcp scheduler** 
> sudo sysctl net.mptcp.mptcp_scheduler

### Disable one interface for MPTCP (or put it in backup-mode)
Note that it is also possible not to use the modified version of IPRoute2 and append the flag 0x80000 (Multipath Off) and 0x100000 (backup) in `/sys/class/net/<INTERFACE>/flags`
check backup  flag on the  SYN packet on wireshark 
 
enable backup  lXC container :  `echo 0x101003 > /sys/class/net/eth0/flags`

revert to default : `echo 0x1003 > /sys/class/net/eth0/flags`

## Problem to resolve 
Problem issue : mptcp stuff is configured kernel wide and not namespace aware 
https://issueexplorer.com/issue/lxc/lxc/3869 


