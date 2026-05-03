# Vulnversity
**Attack Start Date:** 4/11/2026 
<br>**Attack Start Time:** 3:20 PM 

**Attack End Date:** 4/11/2026 
<br>**Attack End Time:** 5:10 PM 

**Target IP Address:** 10.67.133.136 
<br>**Attacker IP Address:** 10.67.89.50 

**Tools Used:** Nmap, Gobuster, BurpSuite 

## Reconaissance
Using the command [ nmap -sV 10.67.133.136. ]  I attempted to determine the version of the services that are running. I also used nmap with the flag [-p-400] to scan for ports/open ports. 
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/VUL1.png)

## Locating Directories Using Gobuster
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/VUL2.png)
---- A screen shot of the target website.  

I used the tool Gobuster to locate a directory to which I can use to upload a shell. I used the command [ gobuster -u http://10.67.133.136:3333 -w /usr/share/wordlists/dirbuster/directory-list-1.0.txt ]  
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/VUL3.png)

From the output in the terminal, I can see that the directory that has an upload form page is /internal.  

## Compromising the Webserver
Fuzzing the upload form to identify which extensions are not blocked.  
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/VUL4.png)

I created a wordlist of PHP extensions to fuzz the upload form and identify which file types were not blocked by the server. 
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/VUL5.png)

Using BurpSuite's Intruder tool, I intercepted the file upload request and configured a Sniper attack to test each extension against the upload form. I set the payload position on the file extension in the filename parameter. 
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/VUL6.png)

The results showed that .phtml and .php4 returned a different response length (774) compared to the others (773), indicating these extensions were not blocked by the server. I proceeded with .phtml for the reverse shell. 
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/VUL7.png)

I downloaded a PHP reverse shell from PentestMonkey and edited the IP address to my attacker machine IP (10.67.89.50) and set the port to 1234.

![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/VUL8.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/VUL9.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/VUL10.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/VUL11.png)

After uploading the .phtml reverse shell and navigating to its URL, I received a connection on my netcat listener running on port 1234. I successfully obtained a shell on the target machine as www-data. 

## Privilege Escalation 
Escalating our privileges and becoming the superuser/root 

I ran the command find / -perm -u=s -type f 2>/dev/null to search for all files with the SUID bit set. The file /bin/systemctl stood out as unusual since systemctl should not have SUID permissions in a secure environment. 
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/VUL12.png)

Using the GTFOBins technique for systemctl SUID abuse, I created a malicious service file that executed a command to copy the root flag to a readable location. I then used /bin/systemctl to link and enable the service, which ran with root privileges and allowed me to read the root flag. This maps to MITRE ATT&CK T1548.001 - Abuse Elevation Control Mechanism: SUID and SGID. 
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/VUL13.png)

## Threat Intel (MITRE Mapping)
| Tactic | Technique ID | Technique Name | How It Was Used |
|--------|-------------|----------------|-----------------|
| Reconnaissance | T1595 | Active Scanning | Used Nmap with -sV and -p-400 flags to scan the target for open ports and running service versions |
| Discovery | T1083 | File and Directory Discovery | Used Gobuster to enumerate directories on the web server and identified /internal/ as the upload endpoint |
| Initial Access | T1190 | Exploit Public-Facing Application | Exploited the unrestricted file upload form at /internal/ to upload a PHP reverse shell |
| Defense Evasion | T1036.008 | Masquerade File Type | Fuzzed the upload form using Burp Suite Intruder to identify that .phtml bypassed the extension filter, then used it for the reverse shell |
| Execution | T1059.004 | Command and Scripting Interpreter: Unix Shell | Uploaded a .phtml reverse shell from PentestMonkey, set attacker IP and port, and triggered it to get a shell as www-data |
| Command and Control | T1095 | Non-Application Layer Protocol | Caught the reverse shell on a Netcat listener on port 1234 using raw TCP |
| Privilege Escalation | T1548.001 | Abuse Elevation Control Mechanism: SUID/SGID | Used find to identify /bin/systemctl with SUID bit set, then created a malicious service file using the GTFOBins technique to copy the root flag to a readable location |
| Collection | T1005 | Data from Local System | Read the root flag from /root/root.txt by executing it through the malicious systemctl service |
