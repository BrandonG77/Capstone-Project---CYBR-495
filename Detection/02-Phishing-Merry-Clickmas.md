# 02 - Phishing: Merry Clickmas

**Attack Date:** March 31, 2026  
**Severity:** High  
**Tool Used by Attacker:** Social Engineer Toolkit (SET)  
**MITRE ATT&CK:** T1566, T1078, T1539  

---

## What Happened?

Phishing is one of the oldest tricks in the book and it still works. In this attack, the attacker sent a fake email pretending to be from a company called Flying Deer. The email looked real — it had a professional subject line about shipping schedule changes — and it contained a link. When the victim clicked the link, it took them to a fake login page that looked identical to the real company staff portal. The victim entered their username and password, and without realising it, handed their credentials directly to the attacker.

One minute later, the attacker used those stolen credentials to log into the victim's real email account and read their inbox.

The tool used to pull this off was the **Social Engineer Toolkit (SET)**, which is a free tool that can clone websites and capture credentials automatically. The fake login page was hosted on the attacker's own machine on port 8000.

The credentials stolen were:
- Username: admin
- Password: unranked-wisdom-anthem

---

## What Signs Did This Attack Leave Behind?

- The phishing email came from a raw IP address in the link rather than a real domain — a major red flag
- The link pointed to port 8000, which is not a standard web port and should never appear in a legitimate company email
- The victim's browser connected to a non-HTTPS page to submit their login credentials
- Within one minute of the credential theft, the same account was accessed from a completely different IP address
- The attacker's machine was seen starting a Python web server and sending an email with a suspicious link in the body

---

## Detection Rules

**Detect the credential capture moment:**
```
index=phishing_lab source="server_access.log"
| search _raw="*Captured*"
| table _time, source, _raw
```

**Detect the same account being accessed from two different IP addresses:**
```
index=phishing_lab source="auth.log"
| rex field=_raw "(?<src_ip>\d+\.\d+\.\d+\.\d+)"
| stats count by src_ip
| table src_ip, count
```

**Detect the rogue server running on port 8000:**
```
index=phishing_lab source="syslog.log"
| search _raw="*8000*"
| table _time, _raw
```

**Full attack timeline:**
```
index=phishing_lab
| table _time, source, _raw
| sort _time
```

---

## What To Do If This Happens To You

1. **Immediately change the password** of any account that may have been compromised
2. **Log out all active sessions** on the affected account — most email services have a "log out all devices" option
3. **Check the inbox and sent folder** of the compromised account for any emails the attacker may have read or sent
4. **Notify any colleagues** who may have received emails from the compromised account after it was taken over
5. **Report the phishing email** to your email provider so they can block the sender
6. **Enable two-factor authentication** on the account immediately if it is not already on

---

## How To Prevent This

- **Enable multi-factor authentication (MFA) on all accounts.** Even if an attacker steals a password, they cannot log in without the second factor. This is the single most effective defence against phishing.
- **Set up DMARC, DKIM, and SPF records** for your business email domain. These are technical email security settings that make it much harder for attackers to spoof emails pretending to be from your company.
- **Train employees** to check the URL in their browser before entering any login credentials. A real company login page will always use HTTPS and your actual domain name.
- **Use an email filtering service** that scans incoming emails for suspicious links and spoofed sender addresses.

---

## Key Takeaway

Phishing works because it targets people, not technology. Even technically aware users can be caught out when they are busy or distracted. The best defence is making sure that even if someone hands over their password, the attacker still cannot get in — and that is exactly what multi-factor authentication does.
