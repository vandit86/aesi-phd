# Using NS3

Installation in chroot jail https://www.projectguideline.com/installing-ns3-35-in-debian-10-chroot-jail-under-debian-11-host-os-or-any-version-of-linux-host/ 

## Compilation
### optimized 
> ./waf configure --build-profile=optimized --out=build/optimized --enable-examples --disable-python --enable-tests --enable-sudo --disable-werror  

### debug 
> ./waf configure --build-profile=debug --out=build/debug --enable-examples --disable-python --enable-tests --enable-sudo --disable-werror  

## Common 
### Redirect to file 
> ./waf --run "m_wave_80211p" > log.out 2>&1

### find text in code 
> find . -name '*.cc' | xargs grep NS_LOG_COMPONENT_DEFINE | grep -i WifiNetDevice
> find . −name ’ ∗ . cc ’ | xargs grep CourseChange | grep Connect

### debuging GDB NS3  
Start with: to load it in gdb:
> ./waf --run "mp-tap-wifi-lte --simTime=60" --gdb

**Quick commands:**
Type "run" to start execution. When it fails, type "bt" to get backtrace on where you were going. Restart, but this time before "run" set breakpoints, e.g. break mysource.cc:1234 to stop execution at that point. Then you can either type n or s to step line-by-line through the execution (s steps into function calls, n does not). You can use 'print varname' to check contents of variables and pretty much anything else (for class variables, you might need to use 'print this->classvar').

## Interact with the real world
different possibilities : https://www.nsnam.org/wiki/HOWTO_make_ns-3_interact_with_the_real_world 

Virtual Machine: https://www.nsnam.org/wiki/HOWTO_use_VMware_to_set_up_virtual_networks_(Windows)

LInux Containers https://www.nsnam.org/wiki/HOWTO_Use_Linux_Containers_to_set_up_virtual_networks

Combine with CORE network emulator: https://www.nsnam.org/wiki/HOWTO_Use_CORE_to_test_ns-3_protocols
	
ns3-lxc project, released on June 17, 2017.	https://github.com/buzz66boy/ns3-lxc/wiki/Release-v0.1

## NS3 Tools

integration with mininet : https://github.com/mininet/mininet/wiki/Link-modeling-using-ns-3 

3d visualizer (new):  https://github.com/usnistgov/NetSimulyzer

Topology gen : https://github.com/idaholab/Topology_Generator

### ns-3 V2X TOOLS 
ns-3 modules to build and simulate ETSI-compliant VANET (V2X) applications using SUMO (v-1.6.0+) and ns-3 (v-3.33), with the possibility of easily switching stack and communication technology. https://github.com/marcomali/ms-van3t

Vehicle simulation program capable of exporting traces in ns2/ns3 format https://github.com/DenisCobeti/Mogen

Co-simulate MATLAB with NS-3 network simulator, combining the powers of MATLAB and NS-3 (Supports modeling WLAN network and 802.11p based V2X scenarios) https://github.com/vkrepo/MATLAB-NS3

Direct Code Execution (DCE) is a framework for ns-3 that provides facilities to execute, within ns-3, existing implementations of userspace and kernelspace network protocols or applications without source code changes.
support only  Ubuntu 16.04 and equivalents (gcc-5.4, libc-2.23)
https://github.com/direct-code-execution/ns-3-dce


