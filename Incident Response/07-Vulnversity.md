# 07 - Vulnversity

---

## Detection Alert

The Detection Engineer was able to identify an attack that proceeded in four phases. First, the attacker completed reconnaissance on the target machine using the Nmap tool to find open ports and services. Next, the attacker used Gobuster to brute force port 3333 and find the machine's internal directories. Then BurpSuite was used to compromize the web server and a reverse shell was found available for the attacker. Finally, the attacker used systemctl to run as a root user on the machine, escalating its privileges substantially.

Alerts were created to acknowledge each of these phases. Using Splunk and Wireshark, the detection was successful. This can then be used to create an incident ticket for the Incident Response Lead to investigate.

Some of the most common responses for these attack phases include

- Strict file type validation
- Renaming uploaded files to prevent path traversal and file overwriting
- Disabling script execution
- Removing unnecessary permissions to avoid unwanted root access
- Network segmentation and firewalls
- Monitoring tools
- Principle of least privilege
- Regularly updating software

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

When investigating these attacks, there are multiple steps to take so uncover the true response methods that need implemented. 

Conduct initial reconnaissance on the company's server with the Nmap scanning tool. Search for directory enumeration logs to uncover vulnerable directories. Check web logs for HTTP POST requests and analyze file extensions being used. Review logs for usage of tools such as Netcat to trace back to the attacker's machine and the port they used. Remove any unneeded root access from any accounts within the server. Implement whitelisting and store files outside of the main directory.

While each organization's filepaths differ and vulnerabilities change, there are some fundamental mitigation tactics that should be implemented to avoid future privilege escalation, recon, directory discovery, and web server compromising.

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

Since this attack used exposed ports to gain advanced privileges to the server, we must ensure that access to these systems is only available to those who need it. Luckily, there are a few ways to mitigate the risk of these attacks.

First, port scanning should be set up with alerts. If a machine interacts with multiple ports in a short amount of time, an alert will fire. Flagging high quantities of HTTP POST requests is necessary, too. 

To prevent future attacks of this nature, a zero-trust strategy needs executed. Everyone and everything that interacts with organizational systems needs verified before they can access each major element. Blocking outbound connections from web servers, only whitelisting needed connections, and requiring authentication before accessing critical data are essential.

Using one or several of these risk reduction tactics ensures users cannot use SSRF to gain unwarranted access to a web server's sensitive data files. This protects the confidentiality, integrity, and availability of the organization's data. 

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
