# 01 - Upload Vulnerabilities

**Attack Date:** April 26, 2026  
**Attack Duration:** 2 hours 15 minutes  
**Severity:** Critical  
**MITRE ATT&CK:** T1190, T1505.003, T1059  

---

## What Happened?

Imagine your website has a feature where users can upload a profile picture. That sounds harmless. But if your website does not check what kind of file is being uploaded, an attacker can upload a malicious script disguised as an image. Once that script is on your server, they can use it to run any command they want — like reading your files, stealing data, or opening a back door into your system.

That is exactly what happened here. An attacker found a file upload form on the web application and used it to upload a PHP webshell — a small script that lets them control the server through a web browser. By the end of the attack they had full interactive access to the server.

The attack happened in four stages:

1. The attacker uploaded an image file to test if uploads worked at all
2. They uploaded a PHP webshell and used it to run commands on the server
3. When the site tried to block them, they used a tool called BurpSuite to bypass the JavaScript filter
4. They then bypassed the server-side filter too by using alternate file extensions and hiding the script inside a fake image header

---

## What Signs Did This Attack Leave Behind?

This is what showed up in the logs that gave the attack away:

- A flood of GET requests testing hundreds of directory names on the server — this is a tool called Gobuster doing a scan
- Multiple file upload attempts using POST requests in a short period of time
- Web requests containing `cmd=` in the URL, which is the signature of someone using a webshell to run commands
- Commands like `id`, `whoami`, and `ls` appearing in web request URLs — an attacker checking what access they have
- A connection established on port 1234, which is not a port that should ever have traffic on a normal web server
- Requests containing `bash`, `/dev/tcp`, or `fsockopen` in the URL — the building blocks of a reverse shell

---

## Detection Rules

These are the Splunk queries used to detect this attack. If you have Splunk set up, you can use these to monitor your own environment.

**Detect all file upload attempts:**
```
index=your_logs | search http_request_method="POST"
| table _time, ip_src, ip_dst, http_request_method, http_request_uri, http_response_code
```

**Detect webshell usage:**
```
index=your_logs | search http_request_uri="*webshell*" AND http_request_uri="*cmd*"
| table _time, ip_src, ip_dst, http_request_uri
```

**Detect reverse shell connection:**
```
index=your_logs | search tcp_dstport="1234" OR tcp_srcport="1234"
| table _time, ip_src, ip_dst, tcp_srcport, tcp_dstport
```

**Detect directory scanning:**
```
index=your_logs | search http_request_method="GET"
| stats count by http_request_uri | sort -count | head 20
```

---

## What To Do If This Happens To You

1. **Immediately take the affected web server offline** to stop the attacker from continuing to use their shell
2. **Check your web server directories** for any files that should not be there, especially .php, .phar, or .php5 files in your uploads folder
3. **Review your web server logs** for any requests containing `cmd=` in the URL
4. **Change all passwords** associated with the web server and any connected databases
5. **Contact your hosting provider** and let them know your server may be compromised
6. **Do not just delete the files** — take a backup of the evidence first for investigation purposes

---

## How To Prevent This

- **Never rely on JavaScript alone to validate file uploads.** JavaScript runs in the browser and attackers can easily bypass it. Validation must happen on the server.
- **Check the actual content of uploaded files**, not just the file extension. An attacker can rename a PHP script to image.jpg.
- **Store uploaded files outside of your web root** so they cannot be executed by the web server even if someone uploads a malicious file.
- **Rename uploaded files** to a random name so attackers cannot predict where their file is stored.
- **Set up alerts** for any connections on unusual ports like 1234 from your web server.

---

## Key Takeaway

File upload forms are one of the most dangerous features on any website. If you have one, make sure your web developer or hosting provider has implemented proper server-side validation. A simple misconfiguration here can give an attacker full control of your server.
