# 04 - XSS Attack (Cross-Site Scripting)

**Attack Date:** March 1, 2026  
**Attack Duration:** ~3 minutes  
**Severity:** High  
**MITRE ATT&CK:** T1059.007, T1185  

---

## What Happened?

Cross-Site Scripting, or XSS, is when an attacker manages to inject a piece of malicious code into a website that then runs inside other users' browsers. Think of it like someone slipping a fake page into a real book — when someone reads that page, they are reading the attacker's content, not the original author's.

In this attack, the attacker found two places on the web application where they could inject JavaScript code:

1. **The search bar** — by typing a script tag directly into the search field, the code ran in the browser immediately. This is called Reflected XSS.
2. **The message form** — by submitting a script tag as a comment or message, the code got saved into the database and ran for every user who loaded that page. This is called Stored XSS, and it is more dangerous because it affects every visitor, not just the attacker.

The payloads used were simple alert boxes, but in a real attack this code could steal session cookies, redirect users to fake login pages, or silently perform actions on behalf of the victim.

The entire attack took less than 3 minutes.

---

## What Signs Did This Attack Leave Behind?

- Log entries containing `<script>` tags in URL parameters or form submissions
- Security events flagged by the application showing XSS attempts
- URL-encoded versions of script tags like `%3Cscript%3E` appearing in web requests
- The same IP address submitting multiple variations of script injection in a short window
- A message or comment in the database containing JavaScript code

---

## Detection Rules

**Detect reflected XSS in the search bar:**
```
index=xss_logs
| search _raw="*<script>*" OR _raw="*%3Cscript%3E*" OR _raw="*alert(*"
| table _time, ip_src, _raw
```

**Detect stored XSS in message submissions:**
```
index=xss_logs log_type="MESSAGE"
| search _raw="*<script>*" OR _raw="*javascript:*"
| table _time, ip_src, _raw
```

**Detect all XSS security alerts:**
```
index=xss_logs log_type="SECURITY"
| table _time, ip_src, _raw
| sort _time
```

---

## What To Do If This Happens To You

1. **Identify where the script was injected** — was it stored in a database or just reflected?
2. **If stored XSS**, remove the malicious content from your database immediately so it stops running for other users
3. **Check if any user sessions or cookies** may have been stolen during the attack window
4. **Force all users to log out** and re-authenticate to invalidate any stolen session tokens
5. **Report the vulnerability to your web developer** so it can be fixed at the code level

---

## How To Prevent This

- **Sanitise all user input.** Any text a user can type into your website — search bars, comment forms, contact forms — should have special characters like `<` and `>` stripped out or converted before being displayed or stored.
- **Use a Content Security Policy (CSP).** This is a browser security setting that tells browsers not to run scripts that did not come from your own domain. It is one of the most effective defences against XSS.
- **Never trust user input.** Anything a user types could be malicious. Your web application should treat all input as potentially dangerous and handle it accordingly.
- **Use a Web Application Firewall** that can detect and block script injection attempts in real time.

---

## Key Takeaway

XSS is one of the most common vulnerabilities on the web. If your website has any feature where users can type something and see it displayed back — a search bar, a comment section, a contact form — there is a chance it is vulnerable to XSS. A good web developer can fix this quickly, but left unchecked it can be used to steal user accounts and spread malicious content to your visitors.
