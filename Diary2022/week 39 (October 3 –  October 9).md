
### PhD tasks 

#### 1. Improve PM algorithm 

Studdy possible solutions : 	
1. Define FAHP logic, network selection algorithms, papers reading, solution comparation  
2. [MCDM-AHP](https://github.com/vandit86/aesi-phd/issues/34)  
3. Writing **[paper 4](https://docs.google.com/document/d/1mFZpZ3p3tSh_LPt7hqiq2izenE61Lz8Hmpi8ZL4zCyI/edit#heading=h.k2pg3nxayr3t)**  

#### 2. Development

**mptcpd**

 1. Implement .py script to define consistent pairwaise comparation matrix, and calculate weights vector, using dedicated pyhton lybrary 
 2. Implement fuzzy ahp solution plugin for mptcpd    
 3. **dwell time calculation**: How to include Mobility attribute** into decision process ?? see: [TASK](https://github.com/vandit86/aesi-phd/issues/34#issuecomment-1210557880)

**ns-3**
1. function to estimate dwell time (connecteion time to RSU) is implemented.. Function is based on veh/rsu position, bearing angles and velocity of veh.. coverage RADIUS is pre defined at the momment  
 
#### 3. Other tasks

  1. Classes CC (2 turnos)

### Sprints

1. Task 1 (+++++)  
2. Task 2 (++++++)
3. Task 3 (++++) 


### Obs
Focus on FAHP and how to include vehicle mobility into decision process -> solution based on time vehicle pass in coverage area of RSU 
Pyhton lybrary was found with multiple **MCDM** algorithms to calculate weights vector:  _from pyDecision.algorithm import ahp_method_
