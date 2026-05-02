# Attacking Kerberos
**Attack Start Date:** 4/9/2026 
<br>**Attack Start Time:** 10:01 AM 
<br>**Attack End Date:** 4/9/2026 
<br>**Attack End Time:** 12:36PM 

**Target IP Address:** 10.66.190.63 
<br>**Attacker IP Address:** 10.66.117.194 

**Tools Used:** Kerbrute, Rubeus, hashcat, mimikatz 

**Objective:** Attacking Kerberos and taking over and control of a network. 

## ONE --- Enumerating the users with Kerbrute 
I downloaded kerbrute onto my attacker machine and then I downloaded the wordlist I wanted to enumerate into the machine as well and used this command: ./kerbrute userenum --dc CONTROLLER.local -d CONTROLLER.local User.txt  to brute force user accounts from a domain controller using the supplied wordlist. 

Kerberos is the default authentication service for Microsoft Windows domains.  
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/AK1.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/AK1.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/AK1.png)

## TWO --- Harvesting and Brute forcing Tickets with Rubeus 
On the target machine, I harvested/gathered tickets that are being transferred to the KDC and save them for use in other attacks like pass the ticket attack. 
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/AK1.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/AK1.png)

Before attempting to brute force/ password spray with rubeus on the target machine, I added the domain controller domain name to the windows host file, navigated to the directory where rubeus was located and wrote a commans where a password is taken and S"sprayed" against all the found users and then given to .kirbi TGT for that user. 
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/AK1.png)

## THREE --- Kerberoasting 
On the target machine, I used rubeus to dump the kerberos hash of any kerberoastable users.
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/AK1.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/AK1.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/AK1.png)

I stored the hashes for these users in separate text files and used hashcat to crack the passwords for the user on my attacker machine. 
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/AK1.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/AK1.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/AK1.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/AK1.png)

## Kerberoasting Mitigation - 
- Strong Service Passwords - If the service account passwords are strong then kerberoasting will be ineffective 
- Don't Make Service Accounts Domain Admins - Service accounts don't need to be domain admins, kerberoasting won't be as effective if you don't make service accounts domain admins. 
## FOUR --- Dumping KRABASREP5 hashes 
AS-REP Roasting dumps the krbasrep5 hashes of user accounts that have kerberos pre-authentication disabled. So I used rubeus to find those users and their hashes. 
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/AK1.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/AK1.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/AK1.png)

Once I got the hashes, I save the hash of each user to their own separate file, and on the terminal made sure the hashes were on one line so hashcat can crack the hash. I was able to see the password of each user. 
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/AK1.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/AK1.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/AK1.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/AK1.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/AK1.png)

## AS-REP Roasting Mitigations -  
- Have a strong password policy. With a strong password, the hashes will take longer to crack making this attack less effective 
- Don't turn off Kerberos Pre-Authentication unless it's necessary there's almost no other way to completely mitigate this attack other than keeping Pre-Authentication on. 

## FIVE --- Passing the Ticket w/ mimikatz 
Pass the ticket works by dumping the TGT from the LSASS memory of the machine. The Local Security Authority Subsystem Service (LSASS) is a memory process that stores credentials on an active directory server and can store Kerberos ticket along with other credential types to act as the gatekeeper and accept or reject the credentials provided.  

In my target machine, I ran mimikatz and checked if I have admin privileges. Since I do, I exported all of the.kirbi tickets into my downloads directory.
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/AK1.png)

Kirbi Tickets in downloads folder/directory
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/AK1.png)

As I am looking to impersonate a ticket, I used an administrator ticket from the krbtgt. I ran the command: kerberos::ptt <ticket> inside of mimikatz with the tickets I harvested earlier so that it will cache and impersonate a ticket. 
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/AK1.png)

Using the command [klist], I can confirm whether I successfully impersonated the ticket by listing the cached tickets. 
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/AK1.png)

Since I have an impersonated ticket, which gives me the same right as the person I am impersonating, I could look at the admin share.  
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/AK1.png)

## Pass the Ticket Mitigation - 
Let's talk blue team and how to mitigate these types of attacks.  
  - Don't let your domain admins log onto anything except the domain controller - This is something so simple however a lot of domain admins still log onto low-level computers leaving tickets around that we can use to attack and move laterally with. 

## SIX --- Creating a Golden/Silver Ticket Attacks 
After navigating to mimikatz, I entered the command : lsadump::lsa /inject /name:krbtg  to dump the hash as well as the security identifier needed to create a Golden ticket. 
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/AK1.png)

The command: Kerberos::golden /user:Administrator /domain:controller.local /sid: /krbtgt: /id: was used for creating a golden ticket to create a silver ticket simply by purring in a service NTLM Hash, the correct sid and id. I believe id 500 creates a golden ticket and the id 1103 creates a silver ticket. 
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/AK1.png)

A ticket is generated and by using the command [misc::cmd] I can open a new elevated command prompt with the given ticket in mimikatz and access the machines that I want depending on the privileges of the user that I decided to take the ticket from. 
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/AK1.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/AK1.png)

## SEVEN --- Kerberos Backdoor 
The Kerberos backdoor works by implanting a skeleton key that abuses the way that the AS-REQ validates encrypted timestamps. A skeleton key only works using Kerberos RC4 encryption.  
To install the skeleton key, I use the command misc::skeleton 
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/AK1.png)

With that my default credentials will be mimikatz and by using commands like : net use c:\\DOMAIN-CONTROLLER\admin$ /user:Administrator mimikatz  the share will now be accessible without the need for an admin password. Or the command: dir \\Desktop-1\c$ /user:Machine1 mimikatz  I can access another desktop without knowing the user who has accessed desktop 1 

## THREAT INTEL (MITRE MAPPING)



