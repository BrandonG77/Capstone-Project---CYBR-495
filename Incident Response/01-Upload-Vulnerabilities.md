# 01 - Upload Vulnerabilities

---

## Detection Alert

The Detection Engineer (Jawaria) created 10 detection rules to identify potential upload vulnerability exploitation attempts. Means to detect when files are overwritten, remote code is executed, or client-side and server-side filtering is bypassed, these rules can be used to automatically create an incident ticket if triggered.

Ten Splunk rules were created in response to the upload vulnerability attacks to swiftly detect when a host attempts one of the activities mentioned above.

1. The first rule is meant to identify file upload attempts when an HTTP POST request is received. The Splunk SPL query being used is index=upload_vuln_logs | search http_request_method="POST" | table _time, ip_src, ip_dst, http_request_method, http_request_uri, http_response_code.

2. The second rule identifies when webshell and cmd= elements are contained in a website URL. The Splunk SPL query is index=upload_vuln_logs | search http_request_uri="*webshell*" AND http_request_uri="*cmd*" | table _time, ip_src, ip_dst, http_request_uri.

3. The third rule identifies instances where bash, dev/tcp, and fsockopen exist in a web request. The Splunk SPL query is index=upload_vuln_logs | search http_request_uri="*cmd=bash*" OR http_request_uri="*dev/tcp*" OR http_request_uri="*fsockopen*" | table _time, ip_src, ip_dst, http_request_uri.

4. The fourth rule identifies when a reverse shell remains active, which is used to execute the attack. The Splunk SPL query is index=upload_vuln_logs | search tcp_dstport="1234" OR tcp_srcport="1234" | table _time, ip_src, ip_dst, tcp_srcport, tcp_dstport.

5. The fifth rule captures when directory scanning is completed by a machine using Gobuster and Dirb software. The Splunk SPL query is index=upload_vuln_logs | search http_request_method="GET" AND ip_src="10.65.124.177" | stats count by http_request_uri | sort - count | head 20.

6. The sixth rule identifies all POST requests, which shows the attack's progression. The Splunk SPL query is index=upload_vuln_logs | search http_request_method="POST" | table _time, ip_src, ip_dst, http_request_uri, http_response_code.

7. The seventh rule identifies failed server-side filter manipulations. The Splunk SPL query is index=upload_vuln_logs | search http_request_method="POST" AND http_request_uri="*submit=invalid*" | table _time, ip_src, ip_dst, http_request_uri, http_response_code.

8. The eighth rule identifies when the attacker used the whoami command to confirm their webshell works. The Splunk SPL query is index=upload_vuln_logs | search http_request_uri="*whoami*" OR http_request_uri="*cmd=id*" | table _time, ip_src, ip_dst, http_request_uri.

9. The ninth rule identifies all network activity sorted by protocol and destination port. The Splunk SPL query is index=upload_vuln_logs | search ip_src="10.65.124.177" | stats count by ws_col_protocol, tcp_dstport | sort - count.

10. The tenth rule identifies the entire timeline for each phase of the attack, from their first HTTP request to the last one. The Splunk SPL query is index=upload_vuln_logs | search ip_src="10.65.124.177" AND http_request_method="*" | table _time, ip_src, ip_dst, http_request_method, http_request_uri, http_response_code | sort _time.


Once any of these alerts are triggered, an incident ticket will be created for the Incident Response Lead's investigation.

Some of the most common responses/preventions for upload vulnerability attacks include

- Implementing server-side file type validation using file extension and MIME type checks. Client-side Javascript should never be the sole reliance for file security.
- Validate file content with Magic Number inspection to identify fake file headers.
- Store files being uploaded outside of the web server's root directory so they can't be executed improperly.
- Rename files randomly and only use safe extensions to prevent attacks' predictions of where sensitive files are stored.

Many, all, or none of these response methods will be implemented depending on the nature of the threat. 

---

## Incident Ticket

**Incident Overview**

Incident ID: ######

Date/Time Detected: ------

Detecting Person/System: ------ 

Reporting Method: ------

Incident Title/Summary: POTENTIAL ------ DETECTED (REQUIRES INVESTIGATION) 

Severity Level (Low, Medium, High, Critical): ------ 

Current Status (New, Investigating, Identified, Mitigating, Resolved, Monitoring): ------
<br><br>
 

**Incident Impact**

Customer-Impacting (Y/N/Description): ------

Approx. Customers Affected: ------

Revenue Impact: ------

Products/Services Impacted: ------

Geographic Impact: ------

SLA Breach Risk: ------
<br><br>
 

**Technical Overview**

Systems/Services Affected: ------

Affected Environment (Production, Staging, Development): ------ 

Root Cause (if known): ------ 

Triggering Event: ------ 
<br><br>
 

**Response Actions**

Designated Incident Manager: ------

Other Employees/Parties: ------

Actions Taken: ------

Mitigation Steps: ------

Implemented Resolutions: ------
<br><br>
 

**Resolution Overview**

Summary: ------

Time to Detect (TTD): ------ 

Time to Mitigate (TTM): ------ 

Time to Resolve (TTR): ------ 

Follow-Up/Monitoring Requirements: ------ 
<br><br>
 

**Incident Classification (Nature of Incident)**

Incident Type (Security, Reliability, Data, Performance, Third-Party, Human Error, Other): ------

---

## Investigation

Files are the main framework used in displaying a website. These files are grouped into certain file types and use HTTP requests to communicate with the host web server. There are a few ways uploaded files on websites can be manipulated--file overwriting, remote code execution, and bypassing client-side or server-side filtering methods. Known as upload vulnerabilities, these attacks can damage a website's functionality.

File overwriting takes place when an attacker overwrites, or saves over, a file on a server using a replacement one. This tests upload functionality on the website for further exploitation.

Code can remotely be executed by a website using a PHP webshell to send commands to the server.

Bypassing client-side or server-side filtering takes place when software such as BurpSuite, Magic Number, or aternate PHP extensions are used to modify web requests.

---

## Stakeholder Coordination

**Organization-related incident communication efforts.**

**Internal Incident Alert**

Subject: INCIDENT ALERT – SEVERITY LEVEL: ------

Incident Name: POTENTIAL ------ DETECTED (REQUIRES INVESTIGATION) 

Status: ------

Detected: ------

Impact: 

Responding Parties: ------

Incident ID: ######

Next Incident Update: ------
<br><br>
 

**Incident Update**

Summary: ------ 

Impact (customer experience, financial, reputation, other): ------

Actions Taken So Far: ------

ETA to Incident Resolution: ------

Next Incident Update: ------
<br><br>
 

**Internal Incident Resolution Announcement**

Subject: INCIDENT RESOLVED – SEVERITY LEVEL: ------

Incident Title/Summary: POTENTIAL ------ DETECTED (REQUIRES INVESTIGATION) 

Incident ID: ######

Duration: ------

Root Cause: ------

Follow-Up: ------

 

Thank you to everyone for responding to the incident.  
<br><br>
 

**Customer-related incident communication efforts.** 

**Customer Notification of Incident Status**

Investigating: We are investigating an issue affecting ... 
<br><br>
 

**Customer Email Notification**

Subject: Service-Affecting Incident Notice

Hello [name], 

 

We experienced a service-related issue impacting ------ on ------.

 

Duration: ------

Impact: ------

Resolution: Security updates were made to ------.

 

We apologize for any inconvenience this may have caused. You may contact [organization contact] with any questions. 

 

-- [company name] 

---

## Containment

Since these upload vulnerability attacks stemmed from poor file type validation, file content validation, and faulty file storage methods, we must ensure users cannot overwrite files, remotely execute code, or bypass filters. Luckily, there are a few ways to mitigate the risk of upload vulnerability attacks.

First, storing web server files with unique names that differ from their original prevents attacks from predicting file contents. These files should also be stored outside the website's root directory and permissions should be heavily restricted. Conducting malware scanning and using whitelists for allowed file extensions decreases the chance of an upload vulnerability attack.

Using one or several of these risk reduction tactics ensures users cannot conduct upload vulnerabiity attacks against the company's website. This protects the confidentiality, integrity, and availability of the organization's data.

The detection alert strategies initially used to detect these attacks will remain implemented to idenfity future upload vulnerability exploitation attempts.

---

## Post-Incident Review

**Summary**

Incident Description: POTENTIAL ------ DETECTED (REQUIRES INVESTIGATION) 

Impact Level: ------

Resolution Outcome: ------
<br><br>
 

**Incident Overview**

Incident ID: ######

Date/Time of Detection: ------

Duration of Incident: ------

Severity Level: ------

Impacted Systems/Services: ------
<br><br>
 

**Customer/Business Impact**

Customer Experience:------ 

Financial Impact: ------

Reputation Impact: ------
<br><br>
 

**Root Cause Overview**

Primary Root Cause: ------

Contributing Factors: ------

Detection Gaps: ------

Process Failures: ------
<br><br>
 

**Timeline**

Detection: ------

Investigation: ------

Mitigation: ------ 

Resolution: ------
<br><br>
 

**Successful Response Factors**

Successful Tools/Processes: ------

Resolution to Incident: ------
<br><br>
 

**Unsuccessful Response Factors**

Unsuccessful Tools/Processes: ------

Alternative Measures Taken: ------
<br><br>

 

**Future Prevention**

Likelihood of Reoccurrence: ------

Risk Response to Limit Reoccurrence: ------

Future Steps Required: ------

[Home Page](https://github.com/BrandonG77/Capstone-Project---CYBR-495/tree/main)  | [Next: Upload Vulnerabilities Infrastructure](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Diagrams/Upload%20Vulnerabilities%20-%20Diagrams.drawio.png)  | [Next Incident Response ---> Insider Threat](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Incident%20Response/12-Insider-Threat.md)   | [Previous Incident Response --> Windows PrivEsc](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Incident%20Response/08-Windows-PrivEsc.md)
