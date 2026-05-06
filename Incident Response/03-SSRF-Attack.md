# 03 - SSRF Attack (Server-Side Request Forgery)

---

## Detection Alert

The Detection Engineer (Jawaria) created detection rules to identify potential Server-Side Request Forgery (SSRF) attacks against a web-based server. Meant to detect when a host attempts to navigate various PHP files that may expose sensitive information, this rule can be used to automatically create an incident ticket if it's triggered. 

Three Splunk rules were created in response to the SSRF attack to swiftly detect when a host uses specific protocols, exposes credentials, or probes the internal network.
1. The first rule is meant to immediately detect when the file:// protocol is used to read local server files via SSRF. The Splunk SPL query being used for this rule is index="ssrf_logs" source="SSRF_Logs.txt.txt" host="aricybr"| search _raw="*file:////*" | table _time, _raw. 
2. The second rule is meant to immediately detect when credentials are exposed through SSRF file reading. The Splunk SPL query being used for this rule is index="ssrf_logs" source="SSRF_Logs.txt.txt" host="aricybr" "[CREDENTIAL_EXPOSURE]" | table _time, _raw. 
3. The third rule is meant to immediately detect attempts to access the internal network services or cloud metadata endpoints using network probing. The Splunk SPL query being used for this rule is index="ssrf_logs" source="SSRF_Logs.txt.txt" host="aricybr" | search _raw="*internal*" OR _raw="*169.254*" OR_raw="*127.0.0.1*" | table _time, _raw. 

Once any of these alerts are triggered, an incident ticket will be created for the Incident Response Lead's (Kaden) investigation. 

Some of the most common responses for XSS injection include 
- Disabling unused protocols 
- Using network segmentation to keep public-facing files separate from sensitive internal data 
- Using whitelists and blacklists for input validation 
- Using allowlists to only permit certain hosts to access the PHP files 
- Sanitizing all user-generated responses to avoid unintended file navigation 
- Isolating the malicious host, blocking them, and discovering the threat vector 
- Escalating to the Incident Response Lead for containment 

Many, all, or none of these response methods will be implemented depending on the nature of the threat. 

---

## Incident Ticket

**Incident Overview**

Incident ID: 135791

Date/Time Detected: Sunday, March 1, 2026, 06:25:10 AM 

Detecting Person/System: Splunk Enterprise 

Reporting Method: Detection Alert 

Incident Title/Summary: SSRF ATTACK DETECTED (REQUIRES INVESTIGATION) 

Severity Level (Low, Medium, High, Critical): Critical 

Current Status (New, Investigating, Identified, Mitigating, Resolved, Monitoring): Investigating 
<br><br>

**Incident Impact**

Customer-Impacting (Y/N/Description): Yes, potential customer user experience may occur. 

Approx. Customers Affected: As many as the PHP files affect. 

Revenue Impact: Substantial if data exposure occurs. 

Products/Services Impacted: Web server operations, files may be affected. 

Geographic Impact: Global until contained. 

SLA Breach Risk: SLA breaches related to customer data, third-party organizations, and uptime requirements may occur. 
<br><br>

**Technical Overview**

Systems/Services Affected: Website's configuration files. 

Affected Environment (Production, Staging, Development): Production, Staging, Development

Root Cause (if known): Manipulatable website requests. 

Triggering Event: >0 SSRF attempts detected. 
<br><br>

**Response Actions**

Designated Incident Manager: Kaden Gilbert (Incident Response Lead) 

Other Employees/Parties: C-suite employees, technical engineers 

Actions Taken: Identify exposed data, disable unneeded protocols, block attacking machine 

Mitigation Steps: Validate/Sanitize inputs, segment network, disable unused protocols, follow strict allowlist/whitelist/blacklist guidelines. 

Implemented Resolutions: None, incident under investigation. 
<br><br>

**Resolution Overview**

Summary: Pending 

Time to Detect (TTD): Instant 

Time to Mitigate (TTM): Pending 

Time to Resolve (TTR): Pending 

Follow-Up/Monitoring Requirements: Pending
<br><br>

**Incident Classification (Nature of Incident)**  

Incident Type (Security, Reliability, Data, Performance, Third-Party, Human Error, Other): Security, Data, Reliability, Performance 

---

## Investigation

Websites generally use configuration and index files to display proper information. Much of these files also dictates the performance of a website and may be sensitive when exposed. When PHP configuration or index files are accessible by unwarranted parties, credentials and methods to alter the site's operation are exposed. 

 

Server-sided request forgery (SSRF) is when an attacker tricks a web server into accessing internal organizational services when it shouldn't. When the malicious host gains access to this sensitive information, it has the ability to execute commands at will—even those that expose data or affect how the website operates. Hosts can gain administrative access to the server files by portraying themselves as an authenticated account. By adjusting the URL and web server requests, elevated access is granted. 

 

This specific SSRF attack used URLs that are meant to remain discrete to access the index.php and config.php files of the web server. The index file listed a configuration file in its contents, which was able to be accessed by changing the URL once more. When the attacker visited the config.php file, they were greeted with the web server's administrative credentials that could be used to login and access even more widespread sensitive data. 

 

Regardless of the SSRF vulnerability's existence, there are some fundamental mitigation tactics that should be implemented to avoid future SSRF issues. 

---

## Stakeholder Coordination

**Organization-related incident communication efforts.**

**Internal Incident Alert**

Subject: INCIDENT ALERT – SEVERITY LEVEL: CRITICAL [low, medium, high, critical] 

Incident Name: SSRF ATTACK DETECTED (REQUIRES INVESTIGATION) 

Status: In Progress 

Detected: Immediately upon attempting malicious actions. 

Impact: Potential financial, reputation, and customer experience impacts. 

Responding Parties: On-call engineer, incident response lead, engineering manager, product manager, customer support, chief technology officer, chief executive officer, legal, public relations 

Incident ID: 135791 

Next Incident Update: Once investigation concludes. 
<br><br>
 

**Incident Update**

Summary: We are currently investigating an incident impacting our web server's operations, which has potentially exposed sensitive data. 

Impact (customer experience, financial, reputation, other): Customer experience, financial, reputation. 

Actions Taken So Far: Attacking machine has been identified, elevated account access has been blocked, network traffic is being monitored, assets are being investigated for compromission. 

ETA to Incident Resolution: ASAP 

Next Incident Update: Once incident is resolved. 
<br><br>
 

**Internal Incident Resolution Announcement**

Subject: INCIDENT RESOLVED – SEVERITY LEVEL: CRITICAL [low, medium, high, critical] 

Incident Title/Summary: SSRF ATTACK DETECTED (REQUIRES INVESTIGATION) 

Incident ID: 135791 

Duration: <1 hour from identification 

Root Cause: Lacked input validation/sanitization, unneeded protocols were available, web server files were improperly segmented. 

Follow-Up: Input validation and sanitization is being implemented, further network monitoring is occurring, unused protocols have been disabled, sensitive web server files have been properly separated. 

 

Thank you to everyone for responding to the incident.  
<br><br>
 

**Customer-related incident communication efforts.**

**Customer Notification of Incident Status**

Investigating: We are investigating an issue affecting our web server's operations. Customers may experience unstable website connectivity. Our team is actively working on mitigating this issue. 
<br><br>
 

**Customer Email Notification**

Subject: Service-Affecting Incident Notice – 03/01/2026 

Hello [name], 

 

We experienced a service-related issue impacting our web server's operations and potential sensitive data exposure on 03/01/2026. 

 

Duration: <1 hour 

Impact: Compromised data security, unpredictable website operations 

Resolution: Security updates were made to the website's file system and more intense security procedures are now in effect 

 

We apologize for any inconvenience this may have caused. You may contact [organization contact] with any questions. 

 

-- [company name] 

---

## Containment

Since this SSRF attack stemmed from improper authentication techniques, we must ensure that elevated account access cannot occur without adhering to stricter web server file access guidelines. Luckily, there are a few ways to mitigate the risk of a server-sided request forgery (SSRF) attack.  

 

First, input validation should be used to limit the characters and format a user can enter into an open-ended input box. This ensures no unwarranted code is executed by the web server unless it's explicitly accepted. Likewise, all user inputs can be sanitized to remove any programming-related syntax or characters, such as untraditional symbols, which may be used to execute unwanted commands and gain elevated access. Disabling all protocols that aren't needed for a website's operations should also be completed to prevent attackers from free-will file access. Part of this includes network segmentation, which ensures critical data remains separate from public-facing website files. This limits the potential for sensitive data exposure. Additionally, access controls lists are needed to govern which IP addresses and domains an application can access. Finally, following the principle of least privilege prevents unauthorized access to files by requiring elevated permissions. 

 

Using one or several of these risk reduction tactics ensures users cannot use SSRF to gain unwarranted access to a web server's sensitive data files. This protects the confidentiality, integrity, and availability of the organization's data. 

 

The detection alert strategies initially used to detect this XSRF attempt will remain implemented to identify future SSRF attack attempts. 

---

## Post-Incident Review

**Summary**

Incident Description: SSRF ATTACK DETECTED (REQUIRES INVESTIGATION) 

Impact Level: Critical 

Resolution Outcome: Implementing input validation and sanitation efforts, segmenting internal and public-facing files, disabling unused protocols, and using principle of least privilege. 
<br><br>
 

**Incident Overview**

Incident ID: 135791 

Date/Time of Detection: Sunday, March 1, 2026, 06:25:10 AM 

Duration of Incident: <1 hour 

Severity Level: Critical 

Impacted Systems/Services: Web server's sensitive files, website operations. 

<br><br>

**Customer/Business Impact**

Customer Experience: Unstable website operations. 

Financial Impact: Loss of revenue due to unstable website functioning. 

Reputation Impact: Substantial if sensitive data is exposed or if the incident prevails. 
<br><br>
 

**Root Cause Overview**

Primary Root Cause: Absence of input validation, readily accessible internal files containing sensitive data. 

Contributing Factors: No network segmentation, unneeded protocols available to attacker, lack of input validation and access control lists. 

Detection Gaps: None, incident was detected instantly via Splunk. 

Process Failures: Lack of effective authentication processes when attempting to access PHP files. 
<br><br>
 

**Timeline**

Detection: Instantly 

Investigation: <1 hour 

Mitigation: 1 hour 

Resolution: <4 hours 
<br><br>
 

**Successful Response Factors**

Successful Tools/Processes: Immediate detection alert, isolation of host machine, identification of impacted data and threat vector. 

Resolution to Incident: Input validation, input sanitization, network segmentation, access controls lists, limited protocol availability, principle of least privilege. 
<br><br>


**Unsuccessful Response Factors**

Unsuccessful Tools/Processes: Ineffective authentication methods prior to the incident, sensitive files were too accessible. 

Alternative Measures Taken: Internal and public-facing files have been separated, allowlists/whitelists/blacklists implemented, limited availability of protocols to restrict data access. 
<br><br>
 

**Future Prevention**

Likelihood of Reoccurrence: Limited due to incident response. 

Risk Response to Limit Reoccurrence: Implemented input validation/sanitization procedures, segmented server files, stronger authentication techniques, access control lists, reduced protocol availability, principle of least privilege. 

Future Steps Required: Continued Splunk monitoring of network traffic, testing file access for future vulnerabilities, zero-trust policy in place to restrict access unless properly authenticated. 



[Home Page](https://github.com/BrandonG77/Capstone-Project---CYBR-495/tree/main)
