# 08 - Windows Privilege Escalation

---

## Detection Alert

The Detection Engineer was able to identify a Windows privilege escalation attack based on the detection rules' findings. Based on these findings, the attack was carried out by using a reverse shell, server message block (SMB) file transfer, authentication to the SMB, using RDP to remotely connect to servers, and gradually navigating from a low-level account to one with escalated privileges.

Some of the most common responses for these attacks include

- Patching vulnerabilities
- Principle of least privilege
- Securing service/file permissions
- Auditing service binaries
- Managing sensitive files to prevent unauthorized privilege elevation

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

When investigating these attacks, there are multiple steps to take to uncover the true response methods that need implemented.

First, gather system information, user information, and network setup information. Then, check for service misconfigurations, unquoted service paths, scheduled tasks, registry autoruns, and stored credentials. Consider token manipulation, methods for exploiting drivers or the system kernel, and ways to modify services on the machine. 

While each organization's filepaths differ and vulnerabilities change, there are some fundamental mitigation tactics that should be implemented to prevent attackers from taking advantage of these steps on company machines.

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

To contain an attack and prevent it from happening again in the future, there are several mitigation strategies to consider. Regularly updating systems, using the principle of least privilege, securing services with access control lists, disabling dangerous policies that shouldn't be active, and monitoring the active machine setup being used are just a few of the ways this attack can be contained.

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
