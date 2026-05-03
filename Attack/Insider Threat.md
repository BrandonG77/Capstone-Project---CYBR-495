# Insider Threat

## INSIDER THREAT ATTACK SCENARIO 

**Malicious Employee Data Theft and System Compromise**
<br>**Date Range:** February - April 2026 

**Threat Actor:** Disgruntled Employee (Insider) 

**Victim Organization:** Corporate Environment 

**Attack Duration:** 8 weeks 

**Detection Status:** Undetected until forensic analysis 

## EXECUTIVE SUMMARY 
This scenario documents a sophisticated insider threat attack where a malicious employee with legitimate system access systematically compromised organizational security, exfiltrated sensitive data, and maintained persistent access over an 8-week period. The insider leveraged valid credentials and authorized access to evade detection while conducting activities that would typically trigger alerts if performed by an external attacker. 

**Key Findings:** 
- Insider abused legitimate credentials to access sensitive systems 
- Used authorized tools to avoid detection 
- Exfiltrated customer data and credentials 
- Established persistence mechanisms for future access 
- Covered tracks by blending with normal user activity 

**Impact:** HIGH - Complete compromise of customer database, credential theft, potential for future unauthorized access 

## THREAT ACTOR PROFILE 

**Insider Type:** Malicious Employee 

**Access Level:** Standard User with Database Access 

**Motivation:** Financial gain / Intent to sell data 

**Technical Sophistication:** Medium-High 

**Detection Risk Awareness:** High (insider knows security controls) 

## ATTACK TIMELINE & KILL CHAIN 

### Phase 1: Initial Reconnaissance (Week 1-2) 
February 2026 

**Activity:** Insider explored internal systems to identify valuable targets 

**Actions Taken:** 

- Mapped internal web applications (OWASP Juice Shop environment) 
- Identified database access points 
- Located credential storage locations 
- Assessed security monitoring capabilities 

**Legitimate Cover:** Normal system administration and testing activities 

**Evidence from Previous Attacks:** 

- OWASP Juice Shop reconnaissance (Attack #1) 
- Identified admin email: Admin@juice-sh.op 
- Located exposed FTP directory with sensitive files 
- Discovered multiple injection points 

## MITRE ATT&CK: 
- T1087: Account Discovery 
- T1046: Network Service Scanning 
- T1083: File and Directory Discovery 

### Phase 2: Credential Harvesting (Week 2-3) 
**February 2026**

**Activity:** Insider exploited web application vulnerabilities to extract credentials 

**Actions Taken:** 

- SQL Injection Attack: 
  - Used SQL injection in login forms to bypass authentication 
  - Payload: ' OR 1=1-- to gain unauthorized admin access 
  - Extracted user credentials from database 
  - Obtained 12 user accounts including administrator 

- Brute Force Attack: 
  - Used legitimate security testing tools (under guise of authorized testing) 
  - Cracked admin password using common password lists 
  - Admin password: admin123 (weak password discovered) 

- Password File Access: 
  - Accessed password-protected files containing credentials 
  - Used pdfcrack to decrypt protected documents 
  - Cracked passwords: "naughtylist", "THM{Cr4ck1n6_z1p$_1s_345y}" 

Retrieved additional system credentials 

**Legitimate Cover:** "Security testing" and "vulnerability assessment" 

**Evidence from Previous Attacks:** 
- OWASP Juice Shop SQL injection (Attack #1) 
- Password cracking attack (Attack #6) 
- Credential harvesting techniques (Attack #7)

**Data Compromised:** 
- 12 user credentials 
- Administrator access 
- Password-protected document contents 

## MITRE ATT&CK: 
- T1078: Valid Accounts 
- T1110.002: Brute Force: Password Cracking 
- T1552.001: Unsecured Credentials: Credentials In Files 
- T1213: Data from Information Repositories 

### Phase 3: Privilege Escalation (Week 3-4) 
**February-March 2026**

**Activity:** Insider escalated privileges on Windows systems 

**Actions Taken:** 
- Exploited Windows service misconfigurations 
- Abused SUID binaries on Linux systems (Vulnversity) 
- Gained SYSTEM/root level access on multiple machines 
- Created backdoor accounts for future access 
- Legitimate Cover: System maintenance and troubleshooting 

**Evidence from Previous Attacks:**
- Windows PrivEsc exploitation (Attack #10) 
- Vulnversity privilege escalation (Attack #9) 

**Systems Compromised:**
- Windows servers (SYSTEM access) 
- Linux servers (root access) 
- Multiple workstations 

## MITRE ATT&CK: 
- T1068: Exploitation for Privilege Escalation 
- T1548: Abuse Elevation Control Mechanism 
- T1136: Create Account 

### Phase 4: Lateral Movement (Week 4-6) 
**March 2026** 

**Activity:** Insider moved across network to access additional systems 

**Actions Taken:** 

- **Post-Exploitation Movement:**
  - Used legitimate remote access tools (SSH, RDP) 
  - Leveraged stolen credentials for lateral movement 
  - Established connections to file servers 
  - Accessed HR and financial databases 

- **Kerberos Attacks:** 
  - Performed Kerberoasting to extract service account credentials 
  - Obtained domain admin credentials 
  - Full domain compromise achieved 

**Legitimate Cover:** Normal administrative tasks and remote troubleshooting 

**Evidence from Previous Attacks:** 
- Post-Exploitation Basics (Attack #8) 
- Attacking Kerberos (Attack #9)

**Network Spread:** 
- Compromised 15+ additional systems 
- Gained domain admin access 
- Access to all file servers and databases

## MITRE ATT&CK:
- T1021: Remote Services 
- T1570: Lateral Tool Transfer 
- T1558.003: Kerberoasting 
- T1550: Use Alternate Authentication Material 

### Phase 5: Data Collection & Staging (Week 6-7) 
**March-April 2026**

**Activity:** Insider systematically collected and prepared data for exfiltration 

**Actions Taken:** 

- Queried customer databases for sensitive information 
- Collected employee PII (names, SSNs, addresses) 
- Gathered financial records and intellectual property 
- Compressed and encrypted stolen data to avoid detection 
- Staged files in inconspicuous system folders 

**Legitimate Cover:** Database maintenance and backup operations 

**Evidence from Previous Attacks:** 

- Database access via SQL injection (Attack #1) 
- FTP directory sensitive file access (Attack #1) 
- Credential database extraction (Attack #1) 

**Data Collected:** 
- 50,000+ customer records 
- Employee PII database 
- Financial reports (Q1-Q4 2025) 
- Proprietary source code 
- Total size: ~5 GB compressed 

## MITRE ATT&CK:
- T1005: Data from Local System 
- T1039: Data from Network Shared Drive 
- T1213.002: Data from Information Repositories: Sharepoint 
- T1074: Data Staged 
- T1560: Archive Collected Data 

### Phase 6: Data Exfiltration (Week 7-8) 
**April 2026** 

**Activity:** Insider exfiltrated stolen data using covert channels 

**Actions Taken:**

- Steganography Exfiltration: 
  - Embedded stolen data in image files 
  - Used steganography tools to hide data in employee photos 
  - Uploaded "harmless" images to personal cloud storage 
  - Data hidden in images appeared as normal business photos 

- DNS Tunneling (Alternative Method): 
  - Exfiltrated data via DNS queries (theoretical capability) 
  - Small chunks of data encoded in DNS requests 
  - Bypassed web proxies and firewalls 
  - Traffic appeared as legitimate DNS lookups 

**Legitimate Cover:** Normal file uploads and network activity 

**Evidence from Previous Attacks:**
- Data exfiltration via steganography (Attack #11) 

## MITRE ATT&CK: 
- T1048.003: Exfiltration Over Unencrypted/Obfuscated Non-C2 Protocol 
- T1567: Exfiltration Over Web Service 
- T1071: Application Layer Protocol 
- T1001.002: Data Obfuscation: Steganography

**Data Exfiltrated:**
- All collected data (5 GB) 
- Successfully transferred to external storage 
- Zero detection by security tools 

**Exfiltration Methods:** 

- Steganography (images uploaded to personal Dropbox) 
- Encrypted archives sent to personal email 
- USB device transfer (during "remote work" days) 

### Phase 7: Persistence & Cover-Up (Week 8) 
**April 2026** 

**Activity:** Insider established backdoors and attempted to cover tracks 

**Actions Taken:**

- **Persistence Mechanisms:**
  - Created hidden admin accounts on compromised systems 
  - Installed web shells on internal applications 
  - Scheduled tasks for maintaining access 
  - Deployed remote access trojans disguised as legitimate software 

- **Anti-Forensics:** 
  - Deleted web server logs containing attack evidence 
  - Cleared Windows Event Logs on compromised systems 
  - Modified timestamps on accessed files 
  - Blended malicious activity with legitimate work 

**Legitimate Cover:** System maintenance and log rotation policies 

## MITRE ATT&CK: 
- T1136.001: Create Account: Local Account 
- T1505.003: Server Software Component: Web Shell 
- T1053: Scheduled Task/Job 
- T1070.001: Indicator Removal on Host: Clear Windows Event Logs 
- T1070.006: Indicator Removal on Host: Timestomp 

**Persistence Established:** 
- 5 backdoor accounts created 
- 3 web shells deployed 
- Scheduled tasks on 10 systems 

**Remote access maintained even after potential account termination**

## CONCLUSION 

This insider threat scenario demonstrates how a malicious employee with legitimate access can systematically compromise an organization over an extended period while evading traditional security controls. By leveraging valid credentials, authorized tools, and knowledge of security monitoring, the insider successfully: 

- Escalated privileges across Windows and Linux systems
- Moved laterally through the network 
- Exfiltrated 5 GB of sensitive customer and employee data 
- Established persistent backdoor access 
- Covered tracks to avoid detection 

**Key Takeaway:** Insider threats require a fundamentally different detection and prevention approach compared to external attacks. Organizations must implement behavioral analytics, strict access controls, comprehensive logging, and a culture of security awareness to effectively combat insider threats. 

**Impact:** This attack scenario resulted in complete data breach, potential regulatory fines (GDPR/CCPA), loss of customer trust, and ongoing security remediation costs estimated at $500K+. 


**Date: April 23, 2026**

 

 

 
