# 12 - Insider Threat

**Scenario Type:** Malicious Employee - Scenario Based Detection  
**Date Range:** February 2026 to April 2026  
**Attack Duration:** 8 weeks  
**Severity:** Critical  
**MITRE ATT&CK:** 29 techniques across the full kill chain  

---

## What Makes This Different?

Every other attack in this playbook came from outside the organisation. An attacker found a weakness, exploited it, and got in. This one is different. The threat actor here is already inside. They have a valid username and password. They are authorised to access many of the systems they are targeting. And they know exactly what the security team monitors.

This is what makes insider threats so hard to detect. Traditional security tools are designed to catch outsiders. They look for unknown IP addresses, unusual login attempts, and suspicious traffic patterns. An insider generates almost none of those signals. They look just like a normal employee because they are one.

Detection here has to focus on **behaviour** — not what systems they accessed, but whether the way they accessed them makes sense for their job.

---

## What Happened?

A disgruntled employee with standard user access and database permissions carried out a systematic attack over 8 weeks. Their goal was financial — they planned to steal and sell customer data.

**Week 1-2: Reconnaissance**
The insider quietly mapped internal systems, identified database access points, located where credentials were stored, and assessed what security monitoring was in place. This all looked like normal curiosity from an employee doing their job.

**Week 2-3: Credential Harvesting**
Using SQL injection on internal web applications and brute force tools disguised as "authorised security testing," the insider extracted credentials for 12 accounts including the administrator. They also cracked password-protected documents using pdfcrack and john, recovering passwords like `naughtylist` and `winter4ever`.

**Week 3-4: Privilege Escalation**
The insider escalated their access on both Windows and Linux servers by exploiting service misconfigurations and SUID binaries. They created backdoor accounts for future access, covering it as routine system maintenance.

**Week 4-6: Lateral Movement**
Using stolen credentials and legitimate remote access tools like RDP and SSH, the insider moved across the network. They also performed Kerberoasting to extract service account credentials and crack them offline, eventually gaining full domain admin access.

**Week 6-7: Data Collection and Staging**
The insider queried the customer database systematically, collecting over 50,000 customer records, employee PII, financial reports, and proprietary source code. The data was compressed and encrypted and hidden in inconspicuous system folders — total size approximately 5GB.

**Week 7-8: Data Exfiltration**
The data was moved out using steganography — hiding it inside image files that were uploaded to personal cloud storage. Encrypted archives were also sent to personal email. No security tool flagged any of this at the time.

**Week 8: Persistence and Cover-Up**
The insider created hidden admin accounts, installed web shells on internal applications, set up scheduled tasks for future access, and deleted logs to cover their tracks.

---

## What Signs Did This Attack Leave Behind?

These are the behavioural indicators that, if monitored, would have raised flags at each phase:

**Reconnaissance:**
- A single account accessing an unusually high number of internal systems in a short period
- Access to systems and applications outside the user's normal job role

**Credential Harvesting:**
- SQL injection patterns appearing in internal web application logs
- Password cracking tools like pdfcrack or john running on a workstation
- Access to wordlist files like rockyou.txt

**Privilege Escalation:**
- Service modifications by a non-admin account
- New local user accounts created outside of normal change procedures
- Registry modifications to AutoRun locations

**Lateral Movement:**
- One account authenticating to more than 10 different systems in a single day
- Kerberos service ticket requests for multiple privileged accounts from one user

**Data Collection:**
- Database queries returning thousands of rows from a user who normally returns far fewer
- Large compressed archive files appearing in user or temp directories
- After-hours database access from a user workstation

**Exfiltration:**
- Large file uploads to cloud storage services from a work device
- Image files with disproportionately large file sizes
- Steganography tools running on a workstation

**Cover-Up:**
- Windows Event Logs being cleared (Event ID 1102) — a near-zero false positive
- Log gaps on servers the user recently accessed

---

## Detection Rules

**Detect user accessing unusual number of systems:**
```
index=web_logs
| stats dc(uri) as unique_pages, count by user, src_ip
| where unique_pages > 50
| sort -unique_pages
```

**Detect SQL injection in internal applications:**
```
index=web_logs
| search (uri="*'*" OR uri="*--*" OR uri="*OR+1=1*")
| stats count by src_ip, user, uri
| where count > 5
```

**Detect password cracking tools running:**
```
index=process_logs
| search process_name="pdfcrack" OR process_name="john" OR process_name="zip2john"
| stats count by user, host, process_name
```

**Detect excessive database queries:**
```
index=database_logs
| stats sum(rows_returned) as total_rows by user, table_name
| where table_name="customers" OR table_name="employees"
| where total_rows > 5000
| sort -total_rows
```

**Detect large uploads to cloud storage:**
```
index=network_logs
| search (dest_domain="dropbox.com" OR dest_domain="drive.google.com" OR dest_domain="gmail.com")
| stats sum(bytes_out) as total_bytes by user, src_ip, dest_domain
| where total_bytes > 50000000
| sort -total_bytes
```

**Detect log clearing — critical alert:**
```
index=windows_logs EventID=1102 OR EventID=104
| stats count by host, user, _time
| sort _time
```

**Detect lateral movement — one account on many systems:**
```
index=authentication
| search (action="login" OR action="rdp_session")
| stats dc(dest_host) as systems_accessed by user
| where systems_accessed > 10
```

---

## What To Do If This Happens To You

1. **Do not alert the employee immediately** — involve HR and legal first before taking any action
2. **Preserve all evidence** before disabling any accounts or systems — forensic evidence is needed for potential legal action
3. **Disable all access** for the employee including VPN, email, and physical access simultaneously once legal has approved
4. **Review the last 90 days of activity** for the suspected account across all systems
5. **Identify what data was accessed** and whether customer notification is required under data protection laws
6. **Check for backdoor accounts** and web shells that may have been left behind

---

## How To Prevent This

- **Implement User and Entity Behaviour Analytics (UEBA)** — this type of tool builds a baseline of normal behaviour for each user and alerts when something deviates
- **Enable database activity monitoring** with row-level auditing on sensitive tables like customer records
- **Apply least privilege** — employees should only have access to the systems and data they need for their specific job, nothing more
- **Conduct regular access reviews** to catch accounts that have accumulated permissions beyond what their role requires
- **Implement Data Loss Prevention (DLP)** on endpoints to detect and block large file transfers to personal cloud services
- **Set up immediate alerts** for Windows Event ID 1102 — log clearing is almost always malicious

---

## Key Takeaway

Insider threats are the hardest attacks to detect and the most damaging when they succeed. Unlike external attackers, insiders do not trigger the usual alarms. The only way to catch them is through behavioural monitoring — understanding what normal looks like for each user and alerting when something does not fit that pattern.

For a small business, the most practical first step is applying strict least privilege access. If employees can only access what they genuinely need, the damage any one person can do is significantly limited even if they go rogue. Regular access reviews, offline backups, and even basic database query monitoring can make the difference between a contained incident and a full data breach that costs hundreds of thousands of dollars in fines and remediation.
