# 08 - Windows Privilege Escalation

**Attack Date:** April 19, 2026  
**Attack Duration:** 1:05 PM to 2:15 PM  
**Severity:** Critical  
**Detection Rate:** 9 out of 10 queries confirmed (90%)  
**MITRE ATT&CK:** T1059.003, T1105, T1574, T1547, T1552, T1134, T1021.001  

---

## What Happened?

Privilege escalation is when an attacker who already has limited access to a system finds a way to give themselves full administrator or SYSTEM-level control. Think of it like a visitor to your office who only has a guest pass managing to copy themselves a master key.

In this attack, the attacker started with access to a low-privilege user account on a Windows machine and then used a series of techniques to reach SYSTEM level — the highest level of access on a Windows computer. They covered a wide range of methods in a single session:

- **Service exploits** — four different Windows services had misconfigurations that allowed the attacker to replace the program they were running with a malicious one, causing the service to run their code as SYSTEM when restarted
- **Registry attacks** — the attacker modified AutoRun entries (programs that run automatically when any user logs in) to execute their malicious payload, and also exploited a Windows policy called AlwaysInstallElevated that let any user install software as SYSTEM
- **Credential access** — the attacker found the admin password stored in plain text in the Windows registry, used saved Windows credentials left on the machine, and dumped the SAM database to crack password hashes offline using hashcat
- **Token impersonation** — used tools called RoguePotato and PrintSpoofer to capture a SYSTEM-level token from a service account and impersonate it
- **RDP sessions** — connected to the machine multiple times via Remote Desktop to carry out the various escalation techniques

---

## What Signs Did This Attack Leave Behind?

- A reverse shell callback on port 4444 — the target machine connecting back to the attacker
- SMB file transfer on port 445 — the attacker's malicious file being pulled from their own server
- Multiple RDP sessions throughout the attack from the same attacker IP
- NTLMSSP authentication from the target machine to the attacker's server — the low-privilege account authenticating to retrieve the malicious file
- The RDP cookie `mstshash=user` confirming the attacker started with the low-privilege user account

---

## Detection Rules

**Detect reverse shell on port 4444:**
```
index=win_privesc_logs
(tcp_srcport="4444" OR tcp_dstport="4444")
| table frame_time, ip_src, ip_dst, tcp_srcport, tcp_dstport
```

**Detect SMB file transfer:**
```
index=win_privesc_logs
(tcp_srcport="445" OR tcp_dstport="445")
| table frame_time, ip_src, ip_dst, tcp_srcport, tcp_dstport
```

**Detect RDP sessions:**
```
index=win_privesc_logs
(tcp_srcport="3389" OR tcp_dstport="3389")
| table frame_time, ip_src, ip_dst, tcp_srcport, tcp_dstport
```

**Detect NTLMSSP authentication to external server:**
```
index=win_privesc_logs
| search _raw="*NTLMSSP*"
| table frame_time, ip_src, ip_dst, tcp_srcport, tcp_dstport
```

**Full attack timeline:**
```
index=win_privesc_logs
(tcp_srcport="4444" OR tcp_dstport="4444"
OR tcp_srcport="445" OR tcp_dstport="445"
OR tcp_srcport="3389" OR tcp_dstport="3389")
| table frame_time, ip_src, ip_dst, tcp_srcport, tcp_dstport
| sort frame_time
```

---

## What To Do If This Happens To You

1. **Isolate the affected machine** from the network immediately to stop any active shells
2. **Reset all passwords** on the machine, especially any that were found stored in the registry or credential manager
3. **Check for new user accounts** that may have been created during the attack
4. **Review all Windows services** for any that have had their binary path changed to point to an unexpected location
5. **Scan for scheduled tasks** that were created outside of normal change windows
6. **Rebuild the machine** from a clean image if possible — once SYSTEM access is achieved, the system cannot be fully trusted

---

## How To Prevent This

- **Never store passwords in the Windows registry** — use a proper credential management solution
- **Disable the AlwaysInstallElevated policy** — this setting should never be enabled in a production environment
- **Audit Windows service permissions regularly** — services should not allow low-privilege users to modify their binary paths
- **Restrict who can connect via RDP** — Remote Desktop should never be open to all users by default
- **Monitor outbound connections from workstations** — a regular user's computer should not be making SMB connections to external servers or initiating connections on port 4444

---

## Key Takeaway

Windows privilege escalation attacks succeed because of small misconfigurations that individually seem harmless — a service with weak permissions here, a policy setting left on there. Attackers are methodical and will try every avenue until one works. Regular configuration audits and the principle of least privilege — giving users only the access they actually need — are the most effective defences against this class of attack.
