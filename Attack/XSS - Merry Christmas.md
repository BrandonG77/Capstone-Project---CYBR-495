# XSS - Merry Christmas
**Start time:** 7:16 am 
<br>**Date:** March 1st 2026 

**Target IP Address:** 10.65.142.72 
<br>**Attack box IP Address:** 10.65.95.6 
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/XSS1.png)

**XSS (Cross Site Scripting)** is a web application vulnerability that lets attackers inject malicious code into input fields that reflect content viewed by other users.  
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/XSS2.png)

**Reflected XSS –** This occurs when a web application includes unvalidated or unsanitzed user input in its immediate response. This typically happens through URL parameters or form submissions. 

On the website, you are able to search for previous messages, I could exploit that by injecting malicious code into the "Search previous Messages" bar. Because the XSS execution was successful an alert box appeared.  
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/XSS3.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/XSS4.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/XSS5.png)

**Stored XSS –** Occurs when malicious script is saved on the server and then loaded for every user who views the affected page. 

The website also has an option to send a message/leave a comment. If I put a malicious code and send the message, I am able to successfully exploit the website and now every time someone clicks the website my malicious code alert will display. 
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/XSS6.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/XSS7.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/XSS8.png)

**Attack end time:** 7:30am 
<br>**End Date:** Match 1st 2026 

## THREAT INTEL REPORT (MITRE ATTACK MAPPING) 
**Initial Access:** 
  - T1190 - Exploit Public-Facing Application  

**Discovery:** 
  - T1083 - File and Directory Discovery  

**Credential Access:** 
  - T1552.001 - Unsecured Credentials: Credentials In Files  

**Lateral Movement:** 
  - T1550.001 - Use Alternate Authentication Material: Application Access Token 

 
