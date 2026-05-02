# 11 - Credential Harvesting

**Attack Date:** April 12, 2026  
**Severity:** Critical  
**Detection Rate:** 13 out of 15 rules confirmed  
**MITRE ATT&CK:** T1003.001, T1003.002, T1003.003, T1003.004, T1021, T1550.002  

---

## What Happened?

Credentials — usernames and passwords — are the keys to everything in a modern organisation. If an attacker can get hold of them, they do not need to break down doors. They can just walk in using the front entrance.

Credential harvesting is the process of extracting stored passwords and password hashes from a compromised system. In this attack, multiple techniques were used to pull credentials from a Windows machine:

- **LSASS Memory Dump** — Windows stores active user credentials in a process called LSASS (Local Security Authority Subsystem Service). MimiKatz accessed this process's memory directly and extracted password hashes from it. This is the most common credential theft technique on Windows.
- **SAM Database Dump** — the Security Account Manager (SAM) database stores local account password hashes. The attacker used registry commands to copy it to disk and then read the hashes.
- **Vault Credential Dumping** — Windows Credential Manager stores saved passwords for websites and network resources. MimiKatz's `vault::list` command extracted these.
- **LSA Secrets** — the Local Security Authority stores cached domain credentials so users can log in even when the domain controller is unavailable. The attacker used `lsadump::secrets` to pull these out.
- **Comsvcs MiniDump** — a stealthy technique that uses a legitimate Windows DLL to dump LSASS memory, avoiding antivirus tools that might flag MimiKatz directly.
- **Lateral Movement** — using the stolen credentials, the attacker moved to other machines on the network using PsExec and network logon events were captured showing access from attacker IPs.

---

## What Signs Did This Attack Leave Behind?

- Any process accessing LSASS memory — 1,486 events detected using Sysmon Event ID 10
- MimiKatz appearing in process creation logs or PowerShell script block logs
- Registry access to the SAM hive — the database that stores local password hashes
- Credential files like `lsass.dmp` being created in temp folders
- The command `rundll32.exe comsvcs.dll MiniDump` being executed — a near-zero false positive indicator
- Network logon events from unexpected IP addresses — 17 events showing lateral movement from attacker IPs

---

## Detection Rules

**Detect LSASS memory access — the most important rule:**
```
index=attack_logs EventID=10
| search TargetImage="*lsass*"
| table _time, host, SourceImage, TargetImage, GrantedAccess
```
Result: 1,486 events — this is the highest volume alert in this entire report.

**Detect MimiKatz execution:**
```
index=attack_logs
| search Image="*mimikatz*" OR CommandLine="*mimikatz*" OR Payload="*mimikatz*"
| table _time, host, Image, CommandLine, Payload
```

**Detect SAM database access:**
```
index=attack_logs
| search TargetObject="*SAM*" OR CommandLine="*reg save*"
| table _time, host, TargetObject, CommandLine
```

**Detect credential files dumped to temp folder:**
```
index=attack_logs
| search TargetFilename="*\\Temp\\*.dmp" OR TargetFilename="*\\Temp\\secrets*"
| table _time, host, Image, TargetFilename
```

**Detect fileless LSASS dump using comsvcs — near-zero false positives:**
```
index=attack_logs
| search CommandLine="*comsvcs*" AND CommandLine="*MiniDump*"
| table _time, host, Image, CommandLine
```

**Detect lateral movement using stolen credentials:**
```
index=attack_logs EventID=4624
| search LogonType="3"
| table _time, host, SubjectUserName, IpAddress, LogonType
```

---

## What To Do If This Happens To You

1. **Any LSASS access alert should be treated as critical** — legitimate software rarely needs to access LSASS memory directly
2. **Reset all passwords immediately** on any system where credential dumping is detected
3. **Rotate all service account passwords** as these are often stored in LSA secrets
4. **Check for lateral movement** — if credentials were stolen, the attacker may have already used them to access other systems
5. **Look for dump files** in temp folders — files with `.dmp` extensions in user temp directories are a strong indicator of credential theft

---

## How To Prevent This

- **Enable Protected Users security group** — adding privileged accounts to this group prevents their credentials from being cached in LSASS memory in a form that MimiKatz can extract
- **Enable Credential Guard** on Windows 10 and later — this isolates LSASS in a virtualised container that MimiKatz cannot access
- **Deploy Sysmon** with rules that alert on any process accessing LSASS — this is your best early warning system for this class of attack
- **Monitor for the creation of `.dmp` files** in temp directories — this is a near-zero false positive indicator that something is trying to dump credentials
- **Restrict who can run PowerShell** and enable PowerShell script block logging so you can see exactly what commands are being run

---

## Key Takeaway

Once an attacker has administrative access to a Windows machine, credential harvesting is almost inevitable — the credentials are there, and the tools to extract them are freely available. The only way to limit the damage is to detect it quickly and have strong monitoring on LSASS access and unusual authentication events. Every minute the attacker has those credentials, they can use them to spread further through your network.
