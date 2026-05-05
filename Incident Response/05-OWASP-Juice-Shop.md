# 05 - OWASP Juice Shop

---

## Detection Alert

The Detection Engineer (Jawaria) created a detection rule to identify potential SQL injection attacks on a web-based application. Meant to detect when SQL commands are typed into web forms, this rule can be used to automatically create an incident ticket if it's triggered. 

 

According to the parameters outlined during the rule's creation, the following elements apply. 

- A Splunk search is conducted in an ongoing state to search for SQL injection evidence. The index=csic _raw="Anomalous*" ("union" OR "select" OR "drop" OR "insert" OR "--") | stats count is used to identify this evidence. If more than 10 events appear within 15 minutes, an alert is triggered. 

- Once an alert is triggered, an incident ticket will be created for the Incident Response Lead's (Kaden) investigation. 

Some of the most common responses for a SQL injection attack include 

- Locating the source IP address of the SQL requests. 

- Blocking this source IP at the firewall level. 

- Check the organization's database to see if any errors or data alteration is discovered. 

Many, all, or none of these response methods will be implemented depending on the nature of the threat. 

---

## Incident Ticket

**Incident Overview**

Incident ID: 123456 

Date/Time Detected: Wednesday, February 11, 2026, 02:48 PM 

Detecting Person/System: Splunk Enterprise 

Reporting Method: Detection Alert 

Incident Title/Summary: SQL INJECTION DETECTED (REQUIRES INVESTIGATION) 

Severity Level (Low, Medium, High, Critical): High 

Current Status (New, Investigating, Identified, Mitigating, Resolved, Monitoring): Investigating 
<br><br>
 

**Incident Impact**

Customer-Impacting (Y/N/Description): Yes, customer data and customer experience may be impacted. 

Approx. Customers Affected: All historical, current, and future customers. 

Revenue Impact: Substantial if not resolved quickly. 

Products/Services Impacted: Website's Database, Ability to Sell Products, Alteration of Proprietary Data 

Geographic Impact: Global until contained. 

SLA Breach Risk: High; customer data, employee data, and partnering companies are at risk of SLA breach. 
<br><br>
 

**Technical Overview**

Systems/Services Affected: Website's Database, Ability to Access Website, Stored Data at Risk of Exposure 

Affected Environment (Production, Staging, Development): Production 

Root Cause (if known): Website visitor attempting to run SQL-based code in website's open-entry forms. 

Triggering Event: >10 SQL events detected within 15 minutes 
<br><br>
 

**Response Actions**

Designated Incident Manager: Kaden Gilbert (Incident Response Lead) 

Other Employees/Parties: Other technical staff, third-party services if affected 

Actions Taken: Identify attacker's source IP address, block attacker's source IP address, investigate company's web server/database to evaluate potentially impacted data 

Mitigation Steps: Prevent execution of SQL code, limit format of website forms when data is being entered by customers, monitor Splunk Enterprise environment for future SQL injection alerts 

Implemented Resolutions: None, alert under investigation. 
<br><br>
 

**Resolution Overview**

Summary: Pending 

Time to Detect (TTD): Pending 

Time to Mitigate (TTM): Pending 

Time to Resolve (TTR): Pending 

Follow-Up/Monitoring Requirements: Pending 
<br><br>
 

**Incident Classification (Nature of Incident)**

Incident Type (Security, Reliability, Data, Performance, Third-Party, Human Error, Other): Security, Data 

---

## Investigation

Many websites use unique account logins to save visitors' data for future use. Likewise, an administrative account is generally present on websites that are used to manage the site's appearance and operations. 

 

In the case of SQL injection, foreign code can be entered into certain parts of the web server's communication to alter its operation. By injecting this code, downtime, loss of data, or unwarranted retrieval of sensitive data may occur. This vulnerability generally stems from a loophole an attacker can manipulate in the website's code, enabling their efforts to enter whatever SQL-based code they wish and having the website execute it as such. 

 

This specific SQL injection attack entered "' or 1=1--" into a form where a user's email was requested. Accepting this query without any hesitation is likely the root cause of this injection attack, which can be manipulated freely by attackers. Luckily, there are several ways injection attacks can be mitigated to avoid further damage from ungoverned visitor inputs. 

---

## Stakeholder Coordination

**Organization-related incident communication efforts.**

**Internal Incident Alert**

Subject: INCIDENT ALERT – SEVERITY LEVEL: HIGH 

Incident Name: SQL INJECTION DETECTED (REQUIRES INVESTIGATION) 

Status: In Progress 

Detected: 15 minutes after malicious efforts began. 

Impact: Potential financial, reputation, and customer experience impacts 

Responding Parties: On-call engineer, incident response lead, engineering manager, product manager, customer support, chief technology officer, legal(on standby), public relations(on standby) 

Incident ID: 123456 

Next Incident Update: Once investigation concludes. 
<br><br>
 

**Incident Update**

Summary: We have investigated an incident impacting the website's database. 

Impact (customer experience, financial, reputation, other): Potential financial, reputation, and customer experience impacts 

Actions Taken So Far: Source IP address has been identified, codebase's weaknesses were uncovered 

ETA to Incident Resolution: <20 hours 

Next Incident Update: Once incident is resolved. 
<br><br>
 

**Internal Incident Resolution Announcement**

Subject: INCIDENT RESOLVED – SEVERITY LEVEL: HIGH 

Incident Title/Summary: SQL INJECTION DETECTED (REQUIRES INVESTIGATION) 

Incident ID: 123456 

Duration: <24 hours from identification 

Root Cause: No input validation measures existed in website's code, no detection alert notifications were initially present. 

Follow-Up: Website's codebase was updated to include input validation measures, Splunk Enterprise detection alert is now in place. 

 

Thank you to everyone for responding to the incident.  
<br><br>
 

**Customer-related incident communication efforts.**

**Customer Email Notification**

Subject: Service-Affecting Incident Notice – February 11, 2026 

Hello [name], 

 

We experienced a service-related issue impacting our website's operations and customer database security on February 11, 2026. 

 

Duration: 24 hours 

Impact: Customer experience, loss of sales, public reputation. 

Resolution: Security updates were made to the website and database. 

 

We apologize for any inconvenience this may have caused. You may contact [organization contact] with any questions. 

---

## Containment

Since this attack's root cause stemmed from poor management over what website visitors can enter into input forms on the website, we must ensure this attack doesn't occur again. Luckily, there are multiple ways to mitigate the risk of injection attacks in the future. 

 

Input validation, or only allowing users to input specific characters, formats, and lengths of data into a login page, for example, restricts the opportunity to enter code-specific syntax. Similarly, limiting a website's codebase to only execute a select few functions limits the opportunities for malicious attackers looking to break a website and gain unwarranted access. Finally, applying escaping to all user inputs on a webpage ensures their entries are strictly read as characters instead of manipulative instructions. 

 

Using one or several of these risk reduction tactics ensures users cannot inject malicious code into the website's forms. This protects the confidentiality, integrity, and availability of the organization's data. 

 

The detection alert strategy previously discussed will also be implemented to identify future injection attempts. 

---

## Post-Incident Review

**Summary**

Incident Description: SQL INJECTION DETECTED (REQUIRES INVESTIGATION) 

Impact Level: High 

Resolution Outcome: Updating website's codebase ensures future injection attacks are unsuccessful. 
<br><br>
 

**Incident Overview**

Incident ID: 123456 

Date/Time of Detection: Wednesday, February 11, 2026, 02:48 PM 

Duration of Incident: <24 hours 

Severity Level: High 

Impacted Systems/Services: Website's Database, Ability to Access Website, Stored Data at Risk of Exposure 
<br><br>
 

**Customer/Business Impact**

Customer Experience: Customer data and customer experience while visiting website may be impacted. 

Financial Impact: Limited if resolved quickly. 

Reputation Impact: Substantial if communication with customers is ineffective. 
<br><br>
 

**Root Cause Overview**

Primary Root Cause: No input validation in website's forms, allowing visitors to enter foreign code and execute it. 

Contributing Factors: Overlooked security principles while constructing codebase. 

Detection Gaps: No SQL injection detection rules were in place prior to the incident. 

Process Failures: Website's code accepted any and all visitor-entered queries, allowing malicious code to be executed. 
<br><br>
 

**Timeline**

Detection: <15 minutes 

Investigation: <4 hours 

Mitigation: <4 hours 

Resolution: <24 hours 
<br><br>
 

**Successful Response Factors**

Successful Tools/Processes: Reevaluating the website's codebase to limit future injection opportunities. 

Resolution to Incident: Implementing input validation within website's codebase, enabling detection rule to notify incident response team if greater than 10 injection attempts occur within 15 minutes. 
<br><br>
 

**Unsuccessful Response Factors**

Unsuccessful Tools/Processes: A lack of detection alerts prior to the incident. 

Alternative Measures Taken: Network traffic and database alterations were investigated, detection rules are now in place. 
<br><br>
 

**Future Prevention**

Likelihood of Reoccurrence: Limited due to recent response. 

Risk Response to Limit Reoccurrence: Conduct further testing to eliminate potential injection attacks, receive detection alert notifications if SQL injection attempts occur. 

Future Steps Required: Monitor network traffic, search for code-related syntax that's being entered into website forms. 
