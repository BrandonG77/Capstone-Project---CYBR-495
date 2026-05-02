# 07 - Vulnversity

**Attack Date:** April 11, 2026  
**Attack Duration:** 3:20 PM to 5:10 PM  
**Severity:** Critical  
**MITRE ATT&CK:** T1595, T1083, T1190, T1059, T1548.001  

---

## What Happened?

This attack followed a very common pattern that attackers use against businesses with public-facing web servers. It went from zero knowledge to full root access on the server in four steps.

1. **Reconnaissance** — the attacker used a tool called Nmap to scan the target and find what ports and services were running. They discovered the web server was running on an unusual port (3333) and that a proxy service was also exposed.

2. **Directory Discovery** — using a tool called Gobuster, the attacker fired hundreds of requests at the web server trying common directory names until they found a hidden upload page at `/internal`.

3. **Web Server Compromise** — the upload form on `/internal` only blocked certain file types, but the attacker used BurpSuite to test different file extensions and found that `.phtml` files were not blocked. They uploaded a reverse shell disguised as a `.phtml` file, triggered it through the browser, and gained access to the server as the `www-data` user.

4. **Privilege Escalation** — once inside, the attacker found that a system tool called `/bin/systemctl` had its SUID bit set. This is a misconfiguration that allowed them to run commands as the root (administrator) user. They used this to read the root flag and take full control of the server.

---

## What Signs Did This Attack Leave Behind?

- Rapid SYN packets going to dozens of different ports within milliseconds of each other — the signature of an Nmap port scan
- Hundreds of GET requests to the web server in quick succession, each trying a different directory name — Gobuster scanning
- Seven POST requests to the `/internal` upload page in quick succession, each with a different file extension — BurpSuite fuzzing
- A TCP connection established from the target server back to the attacker on port 1234 — the reverse shell connecting
- The server initiating an outbound connection, which web servers should almost never do

---

## Detection Rules

**Detect Nmap port scan — single IP hitting many ports:**
```
index=vulnversity_logs ip_src="[attacker_ip]"
| stats dc(tcp_dstport) as unique_ports, count by ip_src, ip_dst
| where unique_ports > 100
| sort -unique_ports
```

**Detect Gobuster directory brute force:**
```
index=vulnversity_logs ip_src="[attacker_ip]" http_request_method="GET" tcp_dstport="3333"
| stats count by ip_src, ip_dst, http_request_uri
| sort -count
```

**Detect BurpSuite file upload fuzzing:**
```
index=vulnversity_logs ip_src="[attacker_ip]" ip_dst="[target_ip]" tcp_dstport="3333"
| stats dc(tcp_srcport) as unique_src_ports, count by ip_src, ip_dst
| where unique_src_ports > 3
```

**Detect reverse shell callback:**
```
index=vulnversity_logs ip_src="[target_ip]" ip_dst="[attacker_ip]" tcp_srcport="1234"
| stats count by ip_src, ip_dst, tcp_srcport, tcp_dstport
```

---

## What To Do If This Happens To You

1. **Take the web server offline** immediately if a reverse shell is detected
2. **Search your upload directories** for any files with unusual extensions like `.phtml`, `.php5`, `.phar`
3. **Check for SUID misconfigurations** on your Linux server — run `find / -perm -u=s -type f` to list all SUID files
4. **Block outbound connections** from your web server at the firewall level — web servers should not be initiating connections to the outside world
5. **Review your upload form restrictions** and ensure they are enforced on the server side, not just in the browser

---

## How To Prevent This

- **Alert on port scanning activity** — if any single IP hits more than 100 ports in under a minute, that should trigger an alert immediately
- **Restrict file upload endpoints** so they require authentication and only accept safe file types validated on the server
- **Block outbound connections from web servers by default** — only allow specific approved destinations
- **Audit SUID binaries regularly** — tools like `systemctl` should never have elevated permissions on a production server
- **Run a weekly scan for unexpected SUID files** on all Linux servers
- ** Adopt a Zero Trust approach — do not assume traffic inside your network is safe. 
Verify everything, assume breach, and never give a web server more outbound access 
than it absolutely needs.
---

## Key Takeaway

This attack followed a completely predictable pattern — scan, find, exploit, escalate. Each step was only possible because of a specific misconfiguration: an exposed directory, an unrestricted file upload form, and an SUID binary that should not have had those permissions. Fixing any one of these would have stopped the attack at that stage. Regular security audits of your web server configuration go a long way toward preventing this kind of compromise.
