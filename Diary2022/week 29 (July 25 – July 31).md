


### PhD tasks 

#### Read/Wite
1. Study to find possible sollution for **inteligent network selection mechanism **

2. Fuzzy logic
	- [Paper in rus example fuzzy logic ](https://www.ispras.ru/proceedings/docs/2002/3/isp_3_2002_121.pdf)
	- [youtube video rus theory](https://www.youtube.com/watch?v=bv0WjMGQK6Q)  	


3. Writing paper 4 : _An efficient radio access selection mechanism for hybrid vehicular networks_  

#### Development

1. enable **CA service** on RSU and vehicles 
	* new branch -> modified SimpleCamSender-gps-tc.cc 	 	
	* **Resolved** : need to set device explicitely on SimpleCamService.. our vehicle and RSU used device "1" and other vehciles device "0"
	* new programm attribute _--send-cam_ is used if you whant that all vehciles and rsu send a CAM
	* branch merged into main  

2. **Define vehicle in simulation limits** 
	* Tets **maximum number** of vehicles running in simulation with CA servise is **22 veh** + rsu, jitter is above 100ms. 
	* With CA service disabled the maximum vehicles is around 40.. connected to respective ns-3 node..  

#### Other tasks 

1. conference SoftCom


### Sprints

total 3 

1. read/wite    (++)  
2. development  (+++++)
3. Other	() 


### Obs

**Fuzzy logic**   

fuzzy lite library , use directly in ns3  



