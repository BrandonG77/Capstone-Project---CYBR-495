# SSRF - Advent of Cyber 2023

**6:25am**
<br> **Mar 1st 2026** 

**Attack Box IP:** 10.64.72.42 
<br>**Target IP address:** 10.64.143.139 

**SSRF – Server Side Request forgery** occurs when an attacker tricks a web application into making unauthorized requests to internal or external resources on the server's behalf. 

To be able to exploit/prerequisites: Vulnerable input points/ lack of input validation 

For a target to be exploitable using SSRF, we need to use some vulnerable input to forge the request to the server.  

For the attack we are trying ot gain control of the McGreedy Comand and Control (C2) server.
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/AOC1.png)

By clicking through the "Accessing through API" link, we are redirected to the endpoints API. Where I could request unauthorized resources and exploit responses. 
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/AOC2.png)

When I type this link [ http://10.64.143.139/getClientData.php?url=file:////var/www/html/index.php ] I am able to gain access to the index.php file. The index.php is a crucial file in many PHP based web applications and websites. It serves as the default page that is loaded when a user visits a website or a directory within a website. This file is typically the entry point for the application and can perform various functions depending on the site's requirements. 
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/AOC3.png)

From the index.php file we can see there is a config file as well most probably containing the login credentials to the server so if I have the url to the config.php instead of index.php I am able to view the username and password of the server and log into it through that. There by gaining access to the McGreedy server.  

![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/AOC4.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/AOC5.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/AOC6.png)

## Mitigation Measures 

- To prevent SSRF exploitation, the following mitigations are suggested: 
- Employing strict input validation and sanitization to prevent malicious input. 
- Using allow lists to control which domains and IPs the application can access. 
- Applying network segmentation to restrict requests to authorized resources. 
- Following the principle of least privilege, granting the minimum permissions required for system operations. 

 **End Time: 7:11 am** 
<br>**March 1st 2026**

 ## THREAT INTEL REPORT (MITRE ATTACK MAPPING) 

**Initial Access:**
  - T1190 - Exploit Public-Facing Application  
**Execution:** 
  - T1059.007 - Command and Scripting Interpreter: JavaScript  
**Persistence:** 
  - T1505.003 - Server Web Shell (similar concept for Stored XSS)  
**Collection:**
  - T1185 - Browser Session Hijacking (potential)  

 
