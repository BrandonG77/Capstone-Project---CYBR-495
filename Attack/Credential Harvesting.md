# Credential Harvesting
**Attack Start Time:** 10:41 PM 
<br>**Attack Start Date:** 4/5/2026 
<br>**Attacker IP:** 10.67.68.172 
<br>**Target IP** (WRK Machine): 10.220.10.20 

**Tools Used:** MimiKatz ,  Secretsdump.py, psexec.py 

**Attack stop time:** 12:13am 
<br>**Attack end date:** 4/6/2026 

**LSASS Memory** --- gaining access to this attackers can extract credentials for lateral movement or privilege escalation. 

**SAM + System Hives** – database for storing password hashes for local user accounts. 

**LSA Secrets** --- contains cached domain credentials for offline logons, cleartext passwords and sometimes RDP session passwords. 

**DPAPI Valut** – for protecting application secrets for each user.  

**NTDS.dit** --- contains domain user accounts, computer objects, service pricipals..etc 

**Objective 1:** Using MimiKatz to collect credentials from various stores. The goal is to go from local admin on a domain- joined workstation (WRK) to getting remote shell access as the local admin on the domain controller.  

On my RDP session, I ran MimiKatz as an Admin and used the command [ vault::list]  to see the availbale vaults the user has.  
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/CH1.png)

From the screenshot above we can see that there is the web credentials vault and the windows credentials vault.  

Next I extracted the credentials using the command [vault :: cred /export]. 
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/CH1.png)
 
I then used mimikatz and the local adminstartor to dump credentials from multiple locations. Dumped plaintext credentials for the domain user [svc-app] and web credentials for the local user [ElonTusk]. Once I have these credentials I can add them to a list of know passwords and use it for password spraying attacks. 

![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/CH1.png)

-- Making a copy of the SAM and System Registry Hives
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/CH1.png)

-- Dumping the SAM registry that contains local user hashes
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/CH1.png)

--Got the credentials for svc-app and a local user. 
<br>Getting Domain Admins.... 
<br>We can use them to crack offline passwords. 

I used the command [ secretsdump.py WRK/Administrator:N3w34829DJdd?1@10.220.10.20 -output local_dump ] to dump some hashes. 

We are interested in MS-Cache v2 (aka DCC2) password hashes. DCC2 hashes are Domain Cached Credentials stored locally on Windows machines. They allow users to log in to a domain account offline, such as on a laptop not currently connected to the domain network 
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/CH1.png)

I then added the hash to a file. Then used this command [john --format=mscash2 dc2_hash.txt --wordlist=/usr/share/wordlists/rockyou.txt  ] to crack the hash. With that I was able to get the password for the user drgonzo. 
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/CH1.png)

I used the password I got to rerun the tool secretsdump.py but this time targeting the domain controller. 
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/CH1.png)

By rerunning the tool I was able to get the domain admin's NTLM hash. 
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/CH1.png)

With drgonzo's plaintext credentials recovered, I verified that this account held Domain Admin privileges within the TRYHACKME.LOC domain, which grants the rights needed to perform a DCSync attack against the domain controller. I then reran secretsdump targeting the DC at 10.220.10.10 using drgonzo's credentials with the -just-dc flag, which uses the DRSUAPI replication method to pull all domain hashes from NTDS.dit without touching the disk. 

I can use the hash to authenticate directly in to the domain control system and I can even navigate through the system to search for files.
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/CH1.png)

## Threat Intel (MITRE Mapping)
| Tactic | Technique ID | Technique Name |
|--------|-------------|----------------|
| Credential Access | T1555.004 | Credentials from Password Stores: Windows Credential Manager |
| Credential Access | T1003.002 | OS Credential Dumping: SAM |
| Credential Access | T1003.004 | OS Credential Dumping: LSA Secrets |
| Credential Access | T1003.003 | OS Credential Dumping: NTDS |
| Credential Access | T1110.002 | Brute Force: Password Spraying |
| Collection | T1005 | Data from Local System |
| Privilege Escalation | T1078.002 | Valid Accounts: Domain Accounts |




