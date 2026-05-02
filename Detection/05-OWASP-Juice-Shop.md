# 05 - OWASP Juice Shop

**Attack Date:** February 2026  
**Dataset Used:** CSIC 2010 Web Application Attack Dataset (61,065 web requests)  
**Severity:** High  
**MITRE ATT&CK:** T1190, T1110, T1083  

---

## What Happened?

OWASP Juice Shop is a deliberately vulnerable web application used for security training. In this exercise, a red team member attacked the application using several different techniques. The detection engineer's job was to analyze the traffic and build rules to catch each type of attack.

Five different attack types were carried out:

1. **SQL Injection** — typed a special command into the login form to skip the password entirely and log in as admin
2. **Brute Force** — used a list of common passwords and tried them all until one worked. The admin password was `admin123`
3. **Account Takeover** — found personal information about a user online, used it to answer a security question, and reset their password
4. **Sensitive Data Exposure** — found an FTP directory that was accidentally left open and downloaded files from it
5. **XSS** — typed JavaScript code into the search bar and it ran in the browser

The dataset analyzed contained 61,065 web requests, and 41% of them were malicious — meaning nearly half of all the traffic hitting this application was an attack.

---

## What Signs Did This Attack Leave Behind?

- SQL keywords like `SELECT`, `UNION`, `DROP`, and `--` appearing in form submissions or URL parameters
- Hundreds of login attempts from the same IP address in a short period of time — brute force in action
- The URL of failed login pages appearing thousands of times, confirming repeated failed attempts
- Directory path characters like `../` and `passwd` appearing in URLs — an attacker trying to access system files
- JavaScript tags appearing in search queries

The most targeted page was the login page — which makes sense because that is where the attacker was trying to get in.

---

## Detection Rules

**Detect SQL injection attempts:**
```
index=csic
| search _raw="Anomalous*" ("union" OR "select" OR "drop" OR "insert" OR "--")
| stats count
```
Result: 2,037 SQL injection events found.

**Detect brute force login attacks:**
```
index=csic
| search _raw="Anomalous*" ("login" OR "password" OR "admin")
| stats count
```
Result: 8,225 credential attack events found — the largest attack category in the dataset.

**Detect directory traversal attempts:**
```
index=csic
| search _raw="Anomalous*" ("passwd" OR "etc" OR "../" OR "cmd" OR "exec")
| stats count
```
Result: 528 directory traversal events found.

**Find the most targeted pages:**
```
index=csic _raw="Anomalous*"
| rex field=_raw "http://localhost:8080(?<uri>/[^\s]+)"
| stats count by uri
| sort -count
| head 10
```

---

## Detection Gaps

Two attack types could not be fully detected in this dataset:

- **DOM XSS** — the dataset did not contain XSS traffic, so 0 results were returned. The detection rule is ready but needs the right log source.
- **Account Takeover via social engineering** — this attack happened entirely outside the web application. The logs only showed a normal successful password reset, which looks identical to a legitimate user resetting their own password. This is a process and policy problem, not a technical one.

---

## What To Do If This Happens To You

1. **If SQL injection is detected**, immediately check whether any data was returned from your database and notify affected users
2. **If brute force is detected**, lock the targeted account and block the attacking IP address at your firewall
3. **If directory traversal is detected**, check whether the attacker successfully accessed any sensitive files
4. **Review your web application** for any directories that are publicly accessible but should not be

---

## How To Prevent This

- **Use parameterized queries** in your database code to make SQL injection impossible — ask your developer about this
- **Enforce strong password policies** — `admin123` should never be an accepted password on any system
- **Remove weak security questions** from your account recovery process — personal information can be found online
- **Audit your web server** for any directories that are accidentally exposed to the public
- **Set up rate limiting** on your login page so attackers cannot try thousands of passwords without being blocked

---

## Key Takeaway

41% of the traffic hitting this web application was malicious. For a small business with a public-facing website, this is a realistic threat level. Most of these attacks are automated — bots constantly scanning the internet looking for weak passwords and vulnerable login forms. The good news is that most of these attacks can be stopped with basic security hygiene: strong passwords, locked login pages, and properly configured servers.
