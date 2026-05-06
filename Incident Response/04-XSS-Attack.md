# 04 - XSS Attack (Cross-Site Scripting)

---

## Detection Alert

The Detection Engineer (Jawaria) created a detection rule to identify potential cross-site scripting (XSS) on a web-based application. Meant to detect when a host enters malicious script code into a web server's URL or submission forms, this rule can be used to automatically create an incident ticket if it's triggered. 

 

Three Splunk rules were created in response to the XSS attack to swiftly detect when a host injects potentially malicious code into a website's URL or input form. 

1. The first rule is meant to immediately detect when Reflected XSS, or the input of malicious code into a website's URL, occurs. The Splunk SPL query being used for this rule is index="xss_logs" source="Xss_Logs.txt.txt" host="aricybr" | search _raw="*[SEARCH]*" AND _raw="*script*" | table _time, _raw. 

2. The second rule is meant to immediately detect when Stored XSS, or the input of malicious code into a website's submission form that openly accepts user input, occurs. The Splunk SPL query being used for this rule is index="xss_logs" source="Xss_Logs.txt.txt" host="aricybr" | search _raw="*[MESSAGE]*" AND _raw="*script*" | table _time, _raw. 

3. The third rule acts as a precaution. It fires an alert when potential XSS activity occurs. This means additional investigation is required to determine whether a XSS threat is being realized or is simply a false-positive alert. The Splunk SPL query being used for this rule is index="xss_logs" source="Xss_Logs.txt.txt" host="aricybr" | search _raw="*POTENTIAL XSS DETECTED*" | table _time, _raw. 

Once any of these alerts are triggered, an incident ticket will be created for the Incident Response Lead's (Kaden) investigation. 

 

Some of the most common responses for XSS injection include 

- Removing the HTTP TRACE call from all web servers 

- Using whitelists and blacklists for input validation 

- Sanitizing all user-generated responses to avoid executing unintended code 

- Utilizing content security policies to govern what data is allowed to load on a webpage 

- Isolating the malicious host, blocking them, and discovering the threat vector 

- Escalating to the Incident Response Lead for containment 

Many, all, or none of these response methods will be implemented depending on the nature of the threat. 

---

## Incident Ticket

**Incident Overview**

Incident ID: 246802 

Date/Time Detected: Sundary, March 1, 2026, 01:23:31 PM 

Detecting Person/System: Splunk Enterprise 

Reporting Method: Detection Rule 

Incident Title/Summary: POTENTIAL XSS DETECTED (REQUIRES INVESTIGATION) 

Severity Level (Low, Medium, High, Critical): High 

Current Status (New, Investigating, Identified, Mitigating, Resolved, Monitoring): Investigating 
<br><br>
 

**Incident Impact**

Customer-Impacting (Y/N/Description): Yes, customer's data and their webpage view may be affected by this incident. 

Approx. Customers Affected: All customers currently navigating the website. 

Revenue Impact: Substantial if customer data, company data, and reputation is affected. 

Products/Services Impacted: Ability to reliably navigate the website and purchase products. The website's database may be impacted. 

Geographic Impact: Global if international customers are attempting to navigate the website. 

SLA Breach Risk: Substantial if not resolved quickly. Customers' information is at risk, presenting a major SLA breach risk. 
<br><br>
 

**Technical Overview**

Systems/Services Affected: Web server, web database, product library, ability to access services 

Affected Environment (Production, Staging, Development): Production 

Root Cause (if known): Cross-site scripting attempt (XSS) by unauthorized host. 

Triggering Event: At least 1 reflected XSS attempt was detected in web search. 
<br><br>

**Response Actions**

Designated Incident Manager: Kaden Gilbert (Incident Response Lead) 

Other Employees/Parties: Engineering leads, product manager, customer support, chief technical officer, legal(on standby), public relations(on standby) 

Actions Taken: Incident detection, investigating issue to isolate malicious host and identify their threat vector 

Mitigation Steps: Input validation, output sanitization, content security policies 

Implemented Resolutions: None, alert under investigation. 
<br><br>
 

**Resolution Overview**

Summary: Pending 

Time to Detect (TTD): Instant 

Time to Mitigate (TTM): <4 hours 

Time to Resolve (TTR): Pending 

Follow-Up/Monitoring Requirements: Pending 
<br><br>
 

**Incident Classification (Nature of Incident)**

Incident Type (Security, Reliability, Data, Performance, Third-Party, Human Error, Other): Security, Data, Reliability 

---

## Investigation

Many websites allow users to enter open-ended inputs into submission forms to narrow search results, purchase products, and communicate with the organization. When users submit this information, it's reflected in the website's server for future use if needed. Unfortunately, this creates vulnerabilities when an ill-intended website visitor attempts to enter unwarranted information into a website's URL or on-site submission forms. Known as cross-site scripting (XSS), an attacker can use these vulnerabilities to steal other customers' information, alter how the site operates, and change what pages it displays. 

 

XSS attacks occur in two different instances—reflected and stored XSS. Reflected XSS occurs when an attacker uses a website's URL or submission form to enter unsanitized inputs and immediately displays these changes. By doing so, the attacker can alter the content displayed on the website and ultimately change how it operates. Stored XSS occurs when an attacker enters a programming script into a web server and uses the server to load an alternative page for every customer that visits the website. This severely impacts how hosts navigate the website which damages the company's reputation and revenue in the process. 

 

This specific reflected XSS attack entered "<script>alert('Reflected Meow Meow')</script>" into an input box meant to search for previous customer messages. Once submitted, the website redirected the attacker to a webpage displaying "Reflected Meow Meow" and the web server's IP address to the user. This could be further used to manipulate the organization if proper response isn't conducted. 

 

The specific stored XSS attack that was conducted entered "<script>alert('Stored Meow Meow')</script>" into an input box meant to send a message to the company. Since the user's message is stored in a file for future retrieval, the malicious script can be executed whenever someone accesses a specific webpage. The attacker can now redirect customers to whatever webpage they want because of this vulnerability. 

 

Regardless of the XSS attack type, there are some fundamental mitigation tactics that should be implemented to avoid future XSS issues. 

---

## Stakeholder Coordination

**Organization-related incident communication efforts.**

**Internal Incident Alert**

Subject: INCIDENT ALERT – SEVERITY LEVEL: HIGH 

Incident Name: POTENTIAL XSS DETECTED (REQUIRES INVESTIGATION) 

Status: Investigating 

Detected: Sundary, March 1, 2026, 01:23:31 PM 

Impact: Website's server and customer's ability to navigate webpages. 

Responding Parties: On-call engineer, incident response lead, engineering manager, product manager, customer support, chief technology officer, legal(on standby), public relations(on standby) 

Incident ID: 246802 

Next Incident Update: Once investigation concludes 
<br><br>
 

**Incident Update**

Summary: We have investigated an incident impacting the website's functionality. 

Impact (customer experience, financial, reputation, other): Potential financial, reputation, and customer experience impacts. 

Actions Taken So Far: Isolated attacking host, threat vector has been identified. 

ETA to Incident Resolution: <20 hours 

Next Incident Update: Once incident is resolved. 
<br><br>
 

**Internal Incident Resolution Announcement**

Subject: INCIDENT RESOLVED – SEVERITY LEVEL: HIGH 

Incident Title/Summary: POTENTIAL XSS DETECTED (REQUIRES INVESTIGATION) 

Incident ID: 246802 

Duration: <24 hours from detection 

Root Cause: No input validation measures existed in the website's submission forms, no input sanitization, no content security policies (CSPs) in place. 

Follow-Up: Implement these methods, monitor for future XSS attempts. 

 

Thank you to everyone for responding to the incident.  
<br><br>
 

**Customer-related incident communication efforts.**

**Customer Notification of Incident Status**

Investigating: We are investigating an issue affecting our website's operations. Customers may experience difficulty navigating our website and submitting communication requests. Our team is actively working on mitigating this issue. 
<br><br>
 

**Customer Email Notification**

Subject: Service-Affecting Incident Notice – March 1, 2026 

Hello [name], 

 

We experienced a service-related issue impacting our website's operations on March 1, 2026. 

 

Duration: 24 hours 

Impact: Ability to navigate website and submit communication requests. 

Resolution: Security updates were made to the website, its server, its database, and the way users submit forms. 

 

We apologize for any inconvenience this may have caused. You may contact [organization contact] with any questions. 

 

-- [company name] 

---

## Containment

Since these XSS attacks stemmed from poor input validation efforts related to open-ended submission forms, we must ensure users cannot manipulate the website in this unintended way again. Luckily, there are a few ways to mitigate the risk of a cross-site scripting (XSS) attack.  

 

First, input validation should be used to limit the characters and format a user can enter into an open-ended input box. This ensures no unwarranted code is executed by the web server unless it's explicitly accepted. Likewise, all user inputs can be sanitized to remove any programming-related syntax or characters, such as untraditional symbols, which may be used to run code where it shouldn't be. Content security policies (CSPs) can also be implemented to only display allowed webpages so site visitors aren't redirected through a malicious path. 

 

Using one or several of these risk reduction tactics ensures users cannot conduct XSS when using the website's forms or URL paths. This protects the confidentiality, integrity, and availability of the organization's data. 

 

The detection alert strategies initially used to detect these XSS attempts will remain implemented to identify future XSS attack attempts. 

---

## Post-Incident Review

**Summary**

Incident Description: POTENTIAL XSS DETECTED (REQUIRES INVESTIGATION) 

Impact Level: High 

Resolution Outcome: Implementing input validation, input sanitizing, and content security policies (CSPs) 
<br><br>
 

**Incident Overview**

Incident ID: 246802 

Date/Time of Detection: Sunday, March 1, 2026, 01:23:31 PM 

Duration of Incident: <24 hours 

Severity Level: High 

Impacted Systems/Services: Website functionality, website navigation, web server, web database 
<br><br>
 

**Customer/Business Impact**

Customer Experience: Difficulty navigating website, accessing unwanted pages 

Financial Impact: Substantial if revenue isn't resolved quickly 

Reputation Impact: Substantial is incident isn't resolved quickly 
<br><br>
 

**Root Cause Overview**

Primary Root Cause: No input validation, no input sanitation, no content security policies. 

Contributing Factors: Open-ended input forms allowing users to enter whatever scripts they want and execute them. 

Detection Gaps: None, incident was detected instantly via Splunk. 

Process Failures: Lack of query checking when submitting forms. 
<br><br>
 

**Timeline**

Detection: Instantly 

Investigation: <4 hour 

Mitigation: <4 hours 

Resolution: <24 hours 
<br><br>
 

**Successful Response Factors**

Successful Tools/Processes: Addressing security vulnerabilities and implementing mitigation tactics. 

Resolution to Incident: Implement input validation, input sanitation, content security policies, and continually monitor Splunk for future XSS detection. 
<br><br>
 

**Unsuccessful Response Factors**

Unsuccessful Tools/Processes: A lack of input protections prior to the incident. 

Alternative Measures Taken: Isolation of host, identification of threat vector, implementing input validation, input sanitation, content security policies, and continuing Splunk monitoring. 
<br><br>
 

**Future Prevention**

Likelihood of Reoccurrence: Limited due to incident response. 

Risk Response to Limit Reoccurrence: Testing security measures to confirm XSS isn't allowed, only allowing users to access certain webpages and input preset entries into forms when applicable. 

Future Steps Required: Continue monitoring Splunk network traffic, test all new URL files and submission forms allowing open-ended input, educate staff and customers on identifying malicious activity within a website. 




[Home Page](https://github.com/BrandonG77/Capstone-Project---CYBR-495/tree/main)  | [Next XSS Infrastructure](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Diagrams/XSS%20-%20Merry%20Christmas%20.drawio.png)  | [Previous Incident Response --> SSRF](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Incident%20Response/03-SSRF-Attack.md)   | [Next Incident Response --> Phishing](https://github.com/BrandonG77/Capstone-Project---CYBR-495/blob/main/Incident%20Response/02-Phishing-Merry-Clickmas.md)
