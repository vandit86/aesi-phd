# MPTCP v1

## About

RFC 8684 : https://www.rfc-editor.org/rfc/rfc8684.html#name-requesting-a-change-in-a-pa 

available in mainline kernel : https://github.com/multipath-tcp/mptcp_net-next/wiki 

instalation is similar to mptcp v0

### MPTCP.v0 vs MPTCP.v1  
My question on mptcpv1 repo github : explanation about upstream and out-of-tree implementation of mptcp 
https://github.com/multipath-tcp/mptcp_net-next/issues/251 

In short:
MPTCPv1 is the protocol defined in RFC8684, MPTCPv0: RFC6824 (deprecated by MPTCPv1)

There are two major implementations of MPTCP in the Linux kernel:
an out-of-tree implementation (fork), aka mptcp.org: https://github.com/multipath-tcp/mptcp and an upstream version (this repo).

The out-of-tree supports MPTCPv0 from the beginning and MPTCPv1 since v0.96 (kernel 5.4). https://github.com/multipath-tcp/mptcp/tree/mptcp_v0.96 
The upstream version only supports MPTCPv1.

The upstream implementations has for the moment only one packets scheduler inspired from BLEST model.
No coupled congestion controls are supported (yet), regular ones per subflow are used..
 

- ``MPTCP.v1``: 
    - isolated sysctl calls in namespaces 
    - included in upstream kernel
    - less modification to TCP stack 
    - easy maintenance 
    - intel gays  


## Usage 

use_mptcp :   https://github.com/pabeni/mptcp-tools 

An utility to force a non MPTCP-enabled application to use MPTCP instead of TCP.
Usage: ./use_mptcp.sh <app> <app command line>
It will build on the fly wrapper library to hijack the socket() libcall, requires gcc and make

`sh /home/vad/mptcp-tools/use_mptcp/use_mptcp.sh  iperf3 -s &`
q
  
## Configuration 

  **/proc/sys/net/mptcp/*** Variables   → configuration variables 
https://www.kernel.org/doc/html/v5.15-rc6/networking/mptcp-sysctl.html 

- **enabled** - BOOLEAN Control whether MPTCP sockets can be created. MPTCP sockets can be created if the value is 1. This is a per-namespace sysctl. Default: 1 (enabled) 
- **add_addr_timeout** - INTEGER (seconds) Set the timeout after which an ADD_ADDR control message will be resent to an MPTCP peer that has not acknowledged a previous ADD_ADDR message. The default value matches TCP_RTO_MAX. This is a per-namespace sysctl. Default: 120 

- **checksum_enabled** - BOOLEAN Control whether DSS checksum can be enabled. DSS checksum can be enabled if the value is nonzero. This is a per-namespace sysctl. Default: 0 

- **allow_join_initial_addr_port** - BOOLEAN Allow peers to send join requests to the IP address and port number used by the initial subflow if the value is 1. This controls a flag that is sent to the peer at connection time, and whether such join requests are accepted or denied. Joins to addresses advertised with ADD_ADDR are not affected by this value. This is a per-namespace sysctl. Default: 1

- **stale_loss_cnt** - INTEGER The number of MPTCP-level retransmission intervals with no traffic and pending outstanding data on a given subflow required to declare it stale. The packet scheduler ignores stale subflows. A low stale_loss_cnt value allows for fast active-backup switch-over, an high value maximize links utilization on edge scenarios e.g. lossy link with high BER or peer pausing the data processing. This is a per-namespace sysctl. Default: 4


## Configuration with iptools2 

  iproute2 new version (supports mptcp)-> https://git.kernel.org/pub/scm/network/iproute2/iproute2.git/ 
  
  `man ip mptcp` : ip mptcp (man page)-> https://man7.org/linux/man-pages/man8/ip-mptcp.8.html  

(iproute2 how to use) https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_and_managing_networking/getting-started-with-multipath-tcp_configuring-and-managing-networking 
  
## Netlink PM (mptcpd) 

git -> https://github.com/intel/mptcpd
  
`./mptcpd  --debug --path-manager=sspi  --addr-flags=subflow  
`
- "addr-flags" are used when announcing an IP address (See the ip-mptcp(8))

- "notify-flags" provides for further control over how plugins are
  notified of changes to local IP addresses.  See the mptcpd(8) man
  page shipped with this mptcpd release for further details.

running  mptcpd with --addr-flags=subflow, will change limits of mptcp protocol : limits are changed to add_addr_accepted 2 subflows 2

**install from .tar.gz (release 0.8) :** 
```
mkdir the_build
cd the_build
../configure
make
```
  
**uninstall:** in folder og mptcpd : 
```  
make uninstall  	
make distclean
```
https://gist.github.com/ruario/a36052a1ae1de4edbc6ad39fe39e5385 

/usr/bin/mkdir -p '/usr/local/etc/mptcpd'
/usr/bin/install -c -m 644 mptcpd.conf '/usr/local/etc/mptcpd'
default config file 

### Control 

`sudo systemctl daemon-reload`
`sudo systemctl start mptcp.service`

  **systemctl list-units -a**  → see running services 
 
  **journalctl | tail** → error apears on journal iof service not started 

 Libraries have been installed in: **/usr/local/lib/mptcpd**


while trying to **run bitwarden_rs in a LXC container** and solved it by removing the LIMITNPROC line in the systemd file
#LimitNPROC=1

**libtool: finish:** PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin:/sbin" ldconfig -n /usr/local/lib

If you ever happen to want to link against installed libraries
in a given directory, LIBDIR, you must either use libtool, and
specify the full pathname of the library, or use the '-LLIBDIR'
flag during linking and do at least one of the following:
   - add LIBDIR to the 'LD_LIBRARY_PATH' environment variable
     during execution
   - add LIBDIR to the 'LD_RUN_PATH' environment variable
     during linking
   - use the '-Wl,-rpath -Wl,LIBDIR' linker flag
   - have your system administrator add LIBDIR to '/etc/ld.so.conf'

See any operating system documentation about shared libraries for
more information, such as the ld(1) and ld.so(8) manual pages.
  
