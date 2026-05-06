# 06 - Attacking Kerberos

---

## Detection Alert

The Detection Engineer is able to detect Kerberos attacks using both Wireshark and Splunk queries. User enumeration attempts, AS-REQ flooding, AS-REP roasting, passing tickets, and malicious communication with the domain controller is able to be documented through these programs. While these activities are detectable, they pose risk to the company if user accounts are compromised. By using Splunk queries to create incident alerts, an incident ticket can automatically be generated when an alert triggers. Once an alert triggers, the Incident Response Lead will investigate the situation.

Some of the most common responses for Kerberos attacks include

- Using Group Managed Service Accounts (gMASs)
- Strengthening service account passwords
- Rotating passwords regularly
- Using the principle of least privilege for every account
- Enforcing Kerberos pre-authentication
- Not making service accounts Domain Administrators
- Monitoring RDP access to remote services

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

Microsoft Windows uses Kerberos as a default a default authentication service. It uses user accounts and hashes their passwords so they're harder to uncover. However, attackers that can gain access to a Windows domain's Kerberos service can enumerate, or find, every user's username and hash. Once the hash is found and paired to an account, it's possible for the attacker to use software and crack the code to expose the user's real password. This can then be used to gain access to critical systems a business uses to succeed.

Regardless of a company's use for Kerberos, there are some fundamental mitigation tactics that should be implemented to avoid future Kerberos issues.

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

Since this Kerberos attack stemmed from the attacker's ability to access the service and uncover user credentials, we must ensure they cannot perform this action in the future. Luckily, there are a few ways to mitigate the risk of Kerberos attacks.

- Enforcing Kerberos pre-authentication on all accounts
- Using strong passwords for all service accounts
- Never making service accounts Domain Admins
- Restricting Domain Admin logins to the domain controller only
- Alert on AS-REQ flood attempts to port 88
- Monitor RDP access to domain controllers
- Rotate passwords regularly

Using one or several of these risk reduction tactics ensures users cannot gain unwarranted access to Microsoft Kerberos services. This protects the confidentiality, integrity, and availability of the organization's data. 

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



[Home Page](https://github.com/BrandonG77/Capstone-Project---CYBR-495/tree/main)
