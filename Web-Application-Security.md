# TryHackMe – Web Application Security

## 1. Overview

In this lab, I investigated an Insecure Direct Object Reference (IDOR) vulnerability in a vulnerable Inventory Management System.

IDOR is a type of **Broken Access Control** vulnerability that occurs when an application fails to verify whether a user is authorized to access or modify a specific resource. By manipulating object identifiers, an attacker may access other users' data or perform unauthorized actions.

The objective of this lab was to identify the user account responsible for malicious changes to planned shipments and revert those changes to restore the system to its original state.

## 2. Investigation

After launching the target machine, I clicked **View Site** to access the Inventory Management System.

I navigated to the **Your Activity** section to inspect the recorded activities of the users. While examining the page, I noticed that the URL contained a user ID parameter.

This suggested that the application might be using a direct object reference to retrieve user activity.

## 3. Exploiting the IDOR Vulnerability

To investigate further, I modified the user ID in the URL to view the activity records of other users.

I checked multiple user accounts and compared their activity logs. Most of the accounts had no recent activity related to the incident. However, **user ID 8** had suspicious activity records that matched the malicious changes described in the lab.

The activity logs indicated that this account had been used to modify the planned shipments, assigning incorrect tire types to different assembly lines.

This confirmed that user ID 8 was the account responsible for the malicious changes.

## 4. Reverting the Malicious Changes

After identifying the suspicious account, I reviewed its activity records and reverted all the malicious changes.

This restored the planned shipments and resolved the sabotage scenario presented in the lab.

## 5. Flag

After successfully reverting the changes, the application provided the following flag:

`THM{IDOR_EXPLORED}`

## 6. What I Learned

- **IDOR:** Changing an object identifier can expose unauthorized resources when proper authorization checks are missing.
- **Broken Access Control:** Applications must verify permissions for every requested resource and action.
- **User Activity Analysis:** Reviewing activity logs can help identify suspicious behavior and determine which account performed unauthorized actions.
- **Practical Testing:** Manipulating URL parameters in an authorized lab is a useful way to investigate potential access control vulnerabilities.

## Conclusion

I successfully completed the lab by identifying the IDOR vulnerability, discovering that user ID 8 was responsible for the malicious changes, and reverting those changes to obtain the flag.

**Flag:** `THM{IDOR_EXPLORED}`
