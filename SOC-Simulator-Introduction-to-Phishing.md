# TryHackMe – SOC Simulator: Introduction to Phishing

## 1. Overview

**Room:** SOC Simulator: Introduction to Phishing  
**Category:** Security Operations Center (SOC) / Phishing Analysis  
**Difficulty:** Beginner

### Objective

The objective of this room was to investigate phishing-related security alerts in a simulated Security Operations Center (SOC) environment. The investigation involved analyzing email logs, searching firewall logs, checking suspicious indicators using threat intelligence, classifying alerts, and writing case reports.

The scenario contained five alerts. The goal was to identify and close all true positive alerts while correctly distinguishing malicious activity from legitimate activity.

## 2. Tools Used

- **SOC Simulator:** Used to manage and investigate alerts.
- **Splunk SIEM:** Used to search email and firewall logs.
- **TryDetectThis:** Used to check whether suspicious IP addresses were known to be malicious.
- **Company Documentation:** Used to identify employees, their machines, and their IP addresses.

## 3. Investigation Methodology

For each alert, I followed a structured investigation process:

1. Reviewed the alert details and its detection rule.
2. Assigned the alert to myself.
3. Searched the SIEM for relevant email addresses, domains, URLs, and IP addresses.
4. Correlated email activity with firewall logs.
5. Identified the affected employee and endpoint using company documentation.
6. Checked suspicious indicators using TryDetectThis when necessary.
7. Classified the alert as a true positive or false positive.
8. Determined whether escalation was required.
9. Documented the findings in a case report.

## 4. Alert Investigations

### Alert 1: Legitimate HR Onboarding Email

**Detection Rule:** Inbound Email Containing Suspicious External Link

**Sender:** onboarding@hrconnex.thm  
**Recipient:** j.garcia@thetrydaily.thm  
**Subject:** Action Required: Finalize Your Onboarding Profile

**Investigation:**

The email initially appeared suspicious because it contained an external link and used an action-oriented subject line.

I searched the SIEM for the sender's domain, `hrconnex.thm`. The logs revealed an internal email from Hannah Harris in Human Resources to Jacob Carter in IT Support. The message confirmed that hrconnex.thm was the company's new third-party HR partner and that J. Garcia was expecting an onboarding email.

No firewall logs indicated that an endpoint had accessed the link.

**Classification:** False Positive  
**Escalation Required:** No

**Conclusion:**

The email was legitimate and expected by the recipient. The investigation found no evidence of malicious activity, so the alert was closed as a false positive.

### Alert 2: Fake Amazon Delivery Email

**Detection Rule:** Inbound Email Containing Suspicious External Link

**Sender:** urgents@amazon.biz  
**Recipient:** h.harris@thetrydaily.thm  
**Subject:** Your Amazon Package Couldn't Be Delivered – Action Required  
**URL:** `http://bit.ly/3sHkX3da12340`

**Investigation:**

Several indicators suggested that the email was a phishing attempt:

- The sender used a suspicious domain, `amazon.biz`, rather than an official Amazon domain.
- The email used a generic greeting instead of addressing the recipient by name.
- A shortened Bitly URL concealed the destination.
- The message created urgency by threatening to return the package within 48 hours.
- The recipient was asked to confirm shipping information through the link.

I searched the SIEM for the URL and found a firewall event associated with source IP `10.20.2.17`. Company documentation identified this address as Hannah Harris's machine.

The firewall log showed that the connection was **blocked** under the `Blocked Websites` rule. A search for the sender also confirmed that Hannah was the only employee who received the email.

**Classification:** True Positive  
**Escalation Required:** No

**Conclusion:**

The email was a phishing attempt designed to obtain personal information. Although the recipient clicked the link, the firewall blocked the connection before the destination loaded. No additional remediation was indicated by the available evidence.

### Alert 3: Access to Blacklisted External URL Blocked by Firewall

**Detection Rule:** Access to Blacklisted External URL Blocked by Firewall

**Source IP:** `10.20.2.17`  
**Destination IP:** `67.199.248.11`  
**URL:** `http://bit.ly/3sHkX3da12340`  
**Firewall Action:** Blocked

**Investigation:**

This alert was directly related to the suspicious Amazon email investigated in Alert 2.

Company documentation identified `10.20.2.17` as Hannah Harris's workstation. A SIEM search for the URL correlated the phishing email with the subsequent firewall event.

I checked destination IP `67.199.248.11` using TryDetectThis, which identified it as **malicious**.

Further SIEM searches showed that the connection was blocked and that no other machine had connected to the same destination IP during the investigated activity.

**Classification:** True Positive  
**Escalation Required:** No

**Conclusion:**

The alert represented a genuine phishing-related connection attempt. However, the firewall prevented the connection, and the available logs did not indicate that the malicious destination was reached. No further remediation was required based on the observed evidence.

### Alert 4: Fake Microsoft Account Security Email

**Detection Rule:** Inbound Email Containing Suspicious External Link

**Sender:** no-reply@m1crosoftsupport.co  
**Recipient:** c.allen@thetrydaily.thm  
**Subject:** Unusual Sign-In Activity on Your Microsoft Account  
**URL:** `https://m1crosoftsupport.co/login`

**Investigation:**

The email impersonated Microsoft's account security team and claimed that an unusual sign-in had occurred. It urged the recipient to secure the account immediately through a provided link.

The sender's domain, `m1crosoftsupport.co`, used the number `1` in place of the letter `i`, making it a lookalike domain. The email's urgency and fake login link were additional phishing indicators.

A SIEM search revealed a firewall connection from source IP `10.20.2.25` to destination IP `45.148.10.131`.

Company documentation identified `10.20.2.25` as Charlotte Allen's workstation. The firewall action was **allowed**, meaning the connection to the suspicious destination was not blocked.

I checked destination IP `45.148.10.131` using TryDetectThis, which identified it as malicious.

The available logs confirmed that Charlotte's workstation reached the suspicious destination. However, the logs alone did not establish whether she entered her credentials.

**Classification:** True Positive  
**Escalation Required:** Yes

**Conclusion:**

This was a confirmed phishing incident in which an employee's workstation connected to a known malicious destination. Because the connection was allowed, the possibility of credential theft could not be ruled out.

The incident required escalation for further investigation and remediation.

**Recommended Remediation Actions:**

- Reset the affected user's password.
- Review account sign-in activity for unauthorized access.
- Investigate the affected workstation for additional suspicious activity.
- Block or investigate the malicious domain and destination IP.
- Review the email and its indicators to determine whether further containment is necessary.

### Alert 5: Legitimate HR Onboarding Email

The scenario contained a fifth alert involving another onboarding email from `hrconnex.thm` to J. Garcia. The email was substantially similar to the first onboarding message.

The scenario ended after all true positive alerts had been closed, leaving this alert open. Based on the scenario's description, it was another false positive because the sender was the legitimate HR partner.

**Classification:** False Positive  
**Escalation Required:** No

## 5. Key Findings

The scenario demonstrated that an alert must be investigated rather than classified solely on the basis of its detection rule.

The most important distinction was the firewall action:

- **Blocked:** The malicious connection was stopped before the destination loaded in the observed scenario.
- **Allowed:** The suspicious connection reached its destination, creating a need to investigate potential impact and consider remediation.

A blocked connection can still represent a true positive because the malicious activity genuinely occurred. However, escalation depends on whether further investigation or remediation is needed.

The scenario also demonstrated the importance of correlating multiple log sources. Email logs revealed the phishing messages, firewall logs showed whether recipients accessed the links, company documentation identified the affected employees, and threat intelligence helped assess suspicious destination IP addresses.

## 6. Lessons Learned

- **Alert classification:** A true positive identifies genuine malicious or policy-violating activity, while a false positive identifies legitimate activity incorrectly flagged by a detection rule.
- **Email analysis:** Suspicious domains, lookalike domains, shortened URLs, generic greetings, and urgency are useful phishing indicators.
- **SIEM investigation:** Searching across email and firewall logs helps establish the relationship between a suspicious email and subsequent network activity.
- **Threat intelligence:** A malicious reputation supports an investigation, while a clean result alone does not prove that an indicator is safe.
- **Escalation decisions:** Analysts must consider the evidence of impact and whether additional investigation or remediation is required.
- **Case reporting:** Reports should clearly explain who was involved, what happened, when and where it occurred, why it was classified as malicious or legitimate, and what actions are recommended.
- **Alert management:** Investigating one alert at a time helps prevent multiple alerts from being closed under the same classification accidentally.

## 7. Conclusion

In this room, I completed a simulated SOC investigation involving phishing emails and firewall alerts. I used Splunk to correlate email and network activity, TryDetectThis to investigate malicious indicators, and company documentation to identify affected endpoints.

The investigations demonstrated how to distinguish legitimate business communications from phishing attempts and how firewall evidence influences escalation decisions.

This exercise strengthened my understanding of alert triage, SIEM searches, phishing analysis, threat intelligence, incident escalation, and security case reporting.
