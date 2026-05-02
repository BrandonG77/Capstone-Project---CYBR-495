# 03 - SSRF Attack (Server-Side Request Forgery)

**Attack Date:** March 1, 2026  
**Severity:** High  
**MITRE ATT&CK:** T1190, T1552  

---

## What Happened?

Server-Side Request Forgery sounds complicated but the idea is simple. Imagine your website has a feature that fetches data from a URL you give it — like a tool that previews a webpage or pulls in content from another site. If your website does not check what URL it is being given, an attacker can point it at internal systems or files on your own server that should never be accessible from the outside.

In this attack, the target was a web application with an endpoint called `getClientData.php`. This endpoint accepted a URL from the user and fetched whatever was at that address. The attacker used this to:

1. Read the source code of the website's own files using the `file://` protocol
2. Find a file called `config.php` that contained login credentials
3. Use those credentials to log into the admin dashboard
4. Probe internal network addresses to see what other systems were accessible

By the end of the attack, the attacker had full access to the admin control panel and had mapped out parts of the internal network — all without ever needing to guess a password or exploit complicated software.

---

## What Signs Did This Attack Leave Behind?

- Requests to the vulnerable endpoint containing `file://` in the URL parameter — a normal user would never do this
- Requests trying to access internal addresses like `127.0.0.1` or `169.254.169.254` through the web application
- The web server reading its own configuration files from disk
- A login attempt using credentials that came from the config file, following shortly after the file was read
- Multiple failed login attempts with common usernames like `admin` and `administrator` before a successful login

---

## Detection Rules

**Detect file protocol abuse in web requests:**
```
index=your_logs
| search http_request_uri="*file://*" OR http_request_uri="*127.0.0.1*"
  OR http_request_uri="*169.254.169.254*"
| table _time, ip_src, http_request_uri
```

**Detect internal network probing:**
```
index=your_logs
| search http_request_uri="*internal*" OR http_request_uri="*localhost*"
| table _time, ip_src, ip_dst, http_request_uri
```

**Detect credential exposure followed by login:**
```
index=your_logs log_type="CREDENTIAL_EXPOSURE" OR log_type="AUTH"
| table _time, ip_src, log_type, message
| sort _time
```

---

## What To Do If This Happens To You

1. **Immediately change any credentials** that may have been exposed through config files
2. **Disable the vulnerable endpoint** or restrict what URLs it is allowed to fetch
3. **Review your server logs** for any requests containing `file://`, `127.0.0.1`, or `localhost` in URL parameters
4. **Check what the attacker accessed** — look for unusual file reads on your server around the time of the attack
5. **Rotate any API keys or database passwords** stored in config files

---

## How To Prevent This

- **Never allow user-supplied URLs to be fetched by your server without validation.** If your application needs to fetch external content, use an allowlist of approved domains only.
- **Block the `file://` protocol** entirely in any URL-handling code — there is no legitimate reason for a web application to read local files this way.
- **Do not store credentials in config files in plain text.** Use environment variables or a dedicated secrets manager instead.
- **Implement a Web Application Firewall (WAF)** that can detect and block SSRF attempts automatically.

---

## Key Takeaway

This attack required zero hacking skills to pull off. The attacker just pointed your own website at your own files and it handed them over. Any web application that fetches content from a user-supplied URL needs very strict controls on what URLs it is allowed to access.
