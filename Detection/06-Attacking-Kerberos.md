# 06 - Attacking Kerberos

**Attack Date:** April 9, 2026  
**Attack Duration:** 10:01 AM to 12:36 PM  
**Severity:** Critical  
**MITRE ATT&CK:** T1589, T1558.003, T1558.004, T1550.003, T1558.001, T1556  

---

## What Is Kerberos?

Before getting into the attack, it helps to understand what Kerberos is. Kerberos is the system that Windows networks use to handle logins and authentication. When you log into a computer at work and it automatically gives you access to shared drives and internal tools without asking for your password again, that is Kerberos working in the background. It uses tickets — think of them like digital passes — that prove who you are to different systems.

The problem is that some of these tickets and the hashes behind them can be stolen, forged, or cracked offline. That is what this attack is all about.

---

## What Happened?

The attacker targeted a Windows domain controller — the central server that manages user accounts and access for an entire organisation. The attack moved through seven stages:

1. **User Enumeration** — used a tool called Kerbrute to brute-force valid usernames. Found 10 valid accounts out of 100 tested.
2. **Password Spray** — tried the password `Password1` against all found accounts. Got a valid ticket for the account `Machine1`.
3. **Kerberoasting** — requested service tickets for two accounts and extracted their encrypted hashes to crack offline with hashcat.
4. **AS-REP Roasting** — found two accounts with a security setting misconfigured (pre-authentication disabled), dumped their hashes, and cracked them.
5. **Pass the Ticket** — stole Kerberos ticket files from memory and used them to impersonate an administrator.
6. **Golden Ticket** — created a forged ticket using the most sensitive hash on the domain (the krbtgt hash). This ticket is valid for 10 years and works for any account.
7. **Skeleton Key Backdoor** — installed a universal password on the domain controller so any account could be logged into using the password `mimikatz`.

---

## What Signs Did This Attack Leave Behind?

- 100 Kerberos requests from the same IP address all within the same minute — a clear sign of an automated scan
- 98 error responses from the domain controller for unknown usernames — confirming the enumeration scan
- Only 2 successful authentication responses coming back, corresponding to the accounts with misconfigured settings
- RDP connections to the domain controller from the attacker's machine
- Traffic on port 88 (Kerberos) in large bursts from a single source

---

## Detection Rules

**Detect Kerbrute username enumeration — burst of Kerberos requests:**
```
index=kerberos_logs ws_col_protocol="KRB5" ip_src="[attacker_ip]"
| bucket _time span=1m
| stats count by _time, ip_src, ip_dst
| where count > 10
| sort -count
```

**Detect AS-REP Roasting — accounts with pre-auth disabled responding:**
```
index=kerberos_logs ws_col_protocol="KRB5" ip_src="[domain_controller_ip]"
| stats count by ip_src, ip_dst, kerberos_msg_type, ws_col_protocol
| sort -count
```

**Detect RDP access to domain controller:**
```
index=kerberos_logs ip_src="[attacker_ip]" tcp_dstport="3389"
| stats count by ip_src, ip_dst, tcp_dstport, ws_col_protocol
| sort -count
```

**Full attack timeline:**
```
index=kerberos_logs ip_src="[attacker_ip]" OR ip_dst="[attacker_ip]"
| eval attack_phase=case(
    ws_col_protocol="KRB5" AND ip_src="[attacker_ip]", "Kerberos Attack Traffic",
    tcp_dstport="3389" OR tcp_srcport="3389", "RDP Access",
    true(), "Other"
)
| stats count by attack_phase, ip_src, ip_dst
| sort -count
```

---

## What To Do If This Happens To You

1. **Alert immediately on any burst of Kerberos requests** — 100 requests in one minute from one IP is never normal
2. **Check which accounts have Kerberos pre-authentication disabled** and enable it immediately
3. **Reset the krbtgt account password twice** — this invalidates any golden tickets that have been created
4. **Audit all service accounts** and make sure they have strong, complex passwords that are not in any wordlist
5. **Remove domain admin rights from service accounts** — a service account should never be a domain administrator

---

## How To Prevent This

- **Enable Kerberos pre-authentication on all accounts** — this setting being disabled is what makes AS-REP Roasting possible
- **Use strong passwords on all service accounts** — simple passwords get cracked in seconds after Kerberoasting
- **Restrict which machines domain admins can log into** — admin accounts logging into regular workstations leave tickets in memory that can be stolen
- **Rotate the krbtgt account password regularly** — this limits the damage if someone has already created a golden ticket
- **Monitor port 88** for unusually high volumes of traffic from a single source

---

## Key Takeaway

This attack is particularly dangerous because it uses legitimate Windows features against you. The tools involved are not hacking exploits — they are just abusing the normal authentication system. This is why network-level monitoring is so important. If you cannot see what is happening on your domain controller, you cannot catch this kind of attack until it is too late.
