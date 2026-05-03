# Passwords - A cracking Chirstmas + Ransomware
**Attack Start Date:** 4/8/2026 
<br>**Attack Start Time:** 2:40 PM 
<br>**Attack End Date**: 4/8/2026 
<br>**Attack End Time:** 2:57 PM 
<br>**Target IP Address:** 10.65.180.63 

 **Tools Used:** pdfcrack, zip2john, john 

On April 8, 2026 at 2:40 PM, ransomware was deployed on the target system (10.65.180.63) that encrypted sensitive files using password protection: - flag.pdf → password-protected PDF - flag.zip → password-protected archive 

A ransom note was created demanding 0.5 BTC payment within 48 hours.  

The attacker claimed the encryption was "military-grade" and "impossible to crack." VICTIM RESPONSE: PASSWORD CRACKING RECOVERY ATTEMPT . 

Rather than paying the ransom, the security team attempted to recover the files by cracking the passwords... 

**Objective:** Cracking passwords on encrypted files that were encrypted by ransomware .  

## Attack 1 – Attack on the Encrypted Pdf file.  
I used the tool pdfcrack for this task. On the terminal in the correct directory where the encrypted file is stored, I ran the command [ pdfcrack –f flag –w /usr/share/wordlists/rockyou.txt ]  
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/pass1.png)

With the results of the command, I was able to use the password to unlock the encrypted pdf file and see the content in the file.  
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/pass1.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/pass1.png)

## Attack 2 – Attack on the encrypted Zip File 
For this attack, I used zip2john and john as tools to make the attack successful. I ran the command [zip2john flag.zip > ziphash.txt] to create a hash of that john would understand and if john finds a password it will report it.  

I then ran the command [ john –wordlist=/usr/share/wordlists/rockyou.txt ziphash.txt ] and was able to get the password of the zip file which I then used to open the zip file and check the contents.  
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/pass1.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/pass1.png)
![des](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Attack/Screenshots/pass1.png)

## Threat Intel (MITRE Mapping)
| Tactic | Technique ID | Technique Name |
|--------|-------------|----------------|
| Impact | T1486 | Data Encrypted for Impact |
| Credential Access | T1110.002 | Brute Force: Password Cracking |
| Credential Access | T1110.002 | Brute Force: Password Cracking |
| Collection | T1005 | Data from Local System |



