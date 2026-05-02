# OWASP JUICE SHOP

**Attack Box IP:** 10.67.108.55 
<br>**Target IP Address:** 10.67.131.171 
<br>**Start time:** 2:48pm Feb 11 2026
<br>**Target Website:** OWASP Juice Shop 
<br>**Tool Used:** BurpSite 

## Walking through the application (Reconnaissance)
The website "OWASP Juice Shop" seems to be a shopping site. From the home page I can see that you can purchase things from fruits to artwork. The website is very simple and easy to navigate. 

![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/OWJ1.png)

The administartor email address: Admin@juice-sh.op 
<br>Parameter used for searching:  q 
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/OWJ2.png)

## Injection Vulnerabilities
- Injection vulnerabilities can cause downtime and/or loss of data. This is particularly dangerous for small businesses or startups that may not recover from such attacks.  
- Identifying injection points is easy as most return an error. We are focusing on SQL Injection for now. 
- SQL Injection is when an attacker enters a malicious or malformed query to either retrieve or tamper data from a database. And in some cases log into accounts.  
- So I set up burp suite from my browser to make sure I am getting some logs. I went to the login panel and added some data before clicking submit to log in I turned intercept mode on burpsuite
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/OWJ3.png)

Viewing the raw format of the login request so I can edit them. 
I changed the email to [' or 1=1--] and used burb suite to forward it to the browser and was able to get logged in as he administrator through [Admin@juice-sh.op ]  
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/OWJ4.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/OWJ5.png)

I tried again with this time  a user account [bender@juice-sh.op ]. This time when editing the raw login packet I put a [ '--] at the end and forwarded it tp the browser through burp suite and I was ale to log in.
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/OWJ6.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/OWJ7.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/OWJ8.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/OWJ9.png)

## Flaws within Authentication
I will be exploiting: 
 - Weak passwords in high privileged accounts 
 - Forgotten password pages

### Brute forcing the admin account password
I was able to try and log in with the admin email and an unknown password so I can do an injection through burp suite for the login details. There I started the process of a brute force attack. I used a text file with common passwords and waited for the password that will have a status code of 201. The password with a status code of 201 is the admin password. With that I was able to log in and have access to the admin password.
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/OWJ10.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/OWJ11.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/OWJ12.png)
Started the brute force attack
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/OWJ13.png)
[admin123] had a status code of 201 so that is the right password
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/OWJ14.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/OWJ15.png)
Gained access to the administrator account
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/OWJ16.png)

### Resetting a password
A user Jim left a review for one of the products at the Juice shop.  
When clicking the forgot password mechanism, the security question is "Your eldest siblings' middle name". From Jim's review he mentioned a replicator 
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/OWJ17.png)

A google search you can find out that a replicator is a machine and it has something to do with star trek. By search "Jim Star Trek" a Wikipedia page comes up and it list Jim's family members and their names. From there I can get his eldest sibling middle name and change his password and have access to his account.
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/OWJ18.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/OWJ19.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/OWJ20.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/OWJ21.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/OWJ22.png)

Gained access to Jim account and changed his password
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/OWJ23.png)

## Sensitive Data Exposure
Web applications should be able to store and transmit data safely. Sometimes developers may not correctly protect sensitive data, which makes the application vulnerable. Or sometimes data protection is not applied consistently across the web application making certain pages that are to be private accessible to the public. Sometimes information can be leaked to the public without the developer knowing.  

Below is a screen shot of the juice shop terms of service/ legal information. In the URL there is /ftp in it which is not supposed to be there. By searching the website till ftp I am brought to a directory where there is sensitive information stored. I could just download these information to my personal computer. 
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/OWJ24.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/OWJ25.png)

## THREAT INTEL REPORT (MITRE MAPPING)

**Reconnaissance:** 
 - T1592.004 - Gather Victim Identity Information: Credentials  
**Initial Access:** 
 - T1190 - Exploit Public-Facing Application  
**Credential Access:** 
 - T1110.001 - Brute Force: Password Guessing  
 - T1555.003 - Credentials from Password Stores  
 - T1589.001 - Gather Victim Identity Information: Credentials  
**Collection:** 
 - T1213 - Data from Information Repositories  
**Exfiltration:** 
  - T1567 - Exfiltration Over Web Service  
 





