# Detection Engineering

**Role:** Detection Engineer & SIEM Analyst  
**Tool Used:** Splunk Enterprise, Wireshark  
**Total Attacks Covered:** 12  

---

## What Is Detection Engineering?

Think of detection engineering like setting up security cameras and alarms in a building. The cameras are your logs — they record everything that happens. The alarms are your detection rules — they go off when something suspicious happens. A detection engineer's job is to decide where to put the cameras, what the alarms should watch for, and what to do when one goes off.

In cybersecurity, instead of cameras we use tools like **Splunk** to collect and search through logs. Instead of alarms we write **detection rules** that automatically flag suspicious activity. Every time an attack happens, a good detection engineer asks two questions: what signs did this attack leave behind, and how do I make sure I catch it next time?

That is exactly what this section covers.

---

## What Is Splunk?

Splunk is a security monitoring tool that collects logs from across your network and lets you search through them to find suspicious activity. It is used by security teams at companies of all sizes. Think of it like a Google search engine for everything that happens on your network. When something suspicious shows up, Splunk can alert your team automatically.

---

## How To Read These Reports

Each attack report follows the same structure so it is easy to follow:

- **What happened** — a plain English explanation of the attack and how it worked
- **How it was detected** — what signs the attack left behind in the logs
- **Detection rules** — the Splunk queries used to catch it
- **What to do** — steps to take if this happens to your business
- **How to prevent it** — simple security controls that would stop this attack

---

## Attacks Covered

| Number | Attack | Type | Severity |
|--------|--------|------|----------|
| 01 | [Upload Vulnerabilities](01-Upload-Vulnerabilities.md) | Web Application | Critical |
| 02 | [Phishing - Merry Clickmas](02-Phishing-Merry-Clickmas.md) | Social Engineering | High |
| 03 | [SSRF Attack](03-SSRF-Attack.md) | Web Application | High |
| 04 | [XSS Attack](04-XSS-Attack.md) | Web Application | High |
| 05 | [OWASP Juice Shop](05-OWASP-Juice-Shop.md) | Web Application | High |
| 06 | [Attacking Kerberos](06-Attacking-Kerberos.md) | Active Directory | Critical |
| 07 | [Vulnversity](07-Vulnversity.md) | Web Application | Critical |
| 08 | [Windows Privilege Escalation](08-Windows-PrivEsc.md) | Endpoint | Critical |
| 09 | [Ransomware Password Cracking](09-Ransomware-Password-Cracking.md) | Ransomware | Critical |
| 10 | [Post Exploitation](10-Post-Exploitation.md) | Post Exploitation | Critical |
| 11 | [Credential Harvesting](11-Credential-Harvesting.md) | Credential Access | Critical |
| 12 | [Insider Threat](12-Insider-Threat.md) | Insider Threat | Critical |

---

## Key Takeaway For Small Businesses

You do not need a large IT team to protect yourself. Most of the attacks in this section succeeded because of small, fixable things — a file upload form with no restrictions, a weak password, an employee clicking a suspicious link. The detection rules here are designed to catch those small things before they turn into major incidents.

If your business uses any kind of website, stores customer data, or has employees with access to sensitive systems, this playbook is for you.
