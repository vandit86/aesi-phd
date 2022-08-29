
### PhD tasks 

#### 1. Improve PM algorithm 

Studdy possible solutions : 	
1. [AHP](https://github.com/vandit86/aesi-phd/issues/34)  
2. Writing **[paper 4](https://docs.google.com/document/d/1mFZpZ3p3tSh_LPt7hqiq2izenE61Lz8Hmpi8ZL4zCyI/edit#heading=h.k2pg3nxayr3t)**  

#### 2. Development

 1. Implement fuzzy ahp solution plugin for mptcpd 
 New kernel v5.19 with userspace pm  and new **mptcpd** version with new commands to create or remove subflows.. 
 new repository **intel/mptcpd**, RSSI-based algorithm passed to _master_ verersion of mptcpd..
 analyse mptcpd arch, update class diagramm  
 implement and test basik functionality of userspace pm (sf_create, sf_backup).. code should be passed to later to _fuzzy-ahp-pm_ branch 


#### 3. Other tasks 
 server shut down resolved  


### Sprints

1. Task 1 (+)  
2. Task 2 (++++++++++)
3. Task 3 () 


### Obs


Focus on FAHP and how to include vehicle mobility into decision process -> solution based on time vehicle pass in coverage area of RSU 
Pyhton lybrary was found with multiple **MCDM** algorithms to calculate weights vector:  _from pyDecision.algorithm import ahp_method_
