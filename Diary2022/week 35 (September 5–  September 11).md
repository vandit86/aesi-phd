
### PhD tasks 

#### 1. Improve PM algorithm 

Studdy possible solutions : 	
1. [AHP](https://github.com/vandit86/aesi-phd/issues/34)  
2. Writing **[paper 4](https://docs.google.com/document/d/1mFZpZ3p3tSh_LPt7hqiq2izenE61Lz8Hmpi8ZL4zCyI/edit#heading=h.k2pg3nxayr3t)**  

#### 2. Development

**mptcpd**
 1. implement and test basic functionality of userspace pm (sf_create, sf_backup)..
 2. create record about potential subflow, mark sf as 'non active', so we can use it to create new subflows when decision algorithm order it..
 3. Get data for ecision process (data rate, rtt, jitter, loss-rate, velocity, acc, RSU-rss, RSU-position... )
 4. Implement fuzzy ahp solution plugin for mptcpd    

 code in  _mptcpd/fuzzy-ahp-pm_ branch 

**ns-3**
 1. new _send_data_to_plugin_ function is created to send all ns3 data to mptcpd plugin:  data is successfully received on sspi plugin
 2. new --inter-data parameter introduced to configure interval in [ms] to send data message to mptcpd plugin: 
 3.  _Flow monitor_ source code was extende in order to allow capture metrics of flows created outside the simulator.. 
should install monitor on both ghosts.  the forwarded addresses of containers are  hardcoded

see data aquisition [TASK ](https://github.com/vandit86/aesi-phd/projects/1#card-85421755)

#### 3. Other tasks 
 - server shut down resolved  
 - VM beckup maded 


### Sprints

1. Task 1 (++)  
2. Task 2 (+++++++++++++)
3. Task 3 () 


### Obs


Focus on FAHP and how to include vehicle mobility into decision process -> solution based on time vehicle pass in coverage area of RSU 
Pyhton lybrary was found with multiple **MCDM** algorithms to calculate weights vector:  _from pyDecision.algorithm import ahp_method_
