# TryHackMe - Detecting Web Attacks

## Room Overview

**Room:** Detecting Web Attacks  
**Platform:** TryHackMe  
**Category:** Defensive Security / SOC  
**Difficulty:** Easy  
**Topics:** Web Attack Detection, Log Analysis, Network Traffic Analysis, Wireshark, WAF

## Task 1 - Introduction

This room focuses on detecting common web attacks through server logs and network traffic analysis.

The main objectives were:

- Understanding common client-side and server-side attacks.
- Learning how to identify suspicious activity through web server logs.
- Analyzing HTTP requests and responses using Wireshark.
- Understanding the role of Web Application Firewalls (WAFs).
- Investigating a simulated web application breach.

## Task 2 - Client-Side Attacks

Client-side attacks target users by exploiting browser vulnerabilities, user behavior, or weaknesses in client-side applications.

The room covered three common client-side attacks:

- **Cross-Site Scripting (XSS):** Malicious JavaScript is injected into a trusted website and executed in visitors' browsers.
- **Cross-Site Request Forgery (CSRF):** Tricks an authenticated user's browser into sending unauthorized requests.
- **Clickjacking:** Uses invisible or disguised interface elements to trick users into interacting with unintended content.

One important takeaway is that client-side attacks can be difficult for SOC analysts to detect because server-side logs and network captures may not provide visibility into what happens inside a user's browser.

### Answers

**Q1: What class of attacks relies on exploiting the user's behavior or device?**

Answer: `client-side`

**Q2: What is the most common client-side attack?**

Answer: `XSS`

## Task 3 - Server-Side Attacks

Server-side attacks exploit vulnerabilities in web servers, application logic, or backend infrastructure.

The room introduced three common examples:

- **Brute Force:** Repeatedly attempting different username and password combinations to gain unauthorized access.
- **SQL Injection (SQLi):** Manipulating SQL queries through improperly handled user input.
- **Command Injection:** Exploiting applications that pass untrusted input to operating system commands.

Unlike many client-side attacks, server-side attacks often leave observable traces in web server logs and network traffic.

### Answers

**Q1: What class of attacks relies on exploiting vulnerabilities within web servers?**

Answer: `server-side`

**Q2: Which server-side attack lets attackers abuse forms to dump database contents?**

Answer: `SQLi`

## Task 4 - Log-Based Detection

In this task, I investigated a simulated banking application breach involving TryBankMe.

I opened the `access.log` file on the lab machine and analyzed the attacker's requests.

### Log Analysis

The investigation demonstrated several useful indicators:

- Repeated requests to different directories.
- Suspicious User-Agent strings.
- Multiple POST requests to the login endpoint.
- Unusual response codes.
- SQL injection payloads in URL query parameters.

### Findings

**1. Directory Fuzzing**

The attacker used the following User-Agent while performing directory fuzzing:

`FFUF v2.1.0`

**2. Brute-Force Attack**

The attacker performed a brute-force attack against:

`/login.php`

Repeated POST requests indicated multiple login attempts.

A change in the response code to `302 Found` indicated a successful login attempt in this scenario.

**3. SQL Injection**

The attacker subsequently targeted:

`/changeusername.php`

The complete decoded SQL injection payload was:

`%' OR '1'='1`

This payload attempted to manipulate the application's SQL query by injecting an always-true condition.

### Answers

**Q1: What is the attacker's User-Agent while performing the directory fuzz?**

Answer: `FFUF v2.1.0`

**Q2: What is the name of the page on which the attacker performs a brute-force attack?**

Answer: `/login.php`

**Q3: What is the complete, decoded SQLi payload the attacker uses on the /changeusername.php form?**

Answer: `%' OR '1'='1`

## Task 5 - Network-Based Detection

In this task, I continued the investigation using the `traffic.pcap` file and Wireshark.

Unlike access logs, network packet captures can provide additional visibility into HTTP communication, including request headers, cookies, POST bodies, and server responses when traffic is not encrypted.

### Wireshark Investigation

I used the following display filter to focus on HTTP traffic:

`http`

I then examined the attack sequence and followed relevant HTTP streams to inspect complete request-response exchanges.

### 1. Brute-Force Investigation

I inspected the repeated login requests and identified the credentials used in the successful login attempt.

The password successfully identified by the attacker was:

`astrongpassword123`

### 2. SQL Injection Investigation

Next, I examined the request targeting:

`/account/changeusername.php`

The request contained the following encoded query parameter:

`q=%25%27+OR+%271%27%3D%271`

After decoding, the SQL injection payload was:

`%' OR '1'='1`

The request also contained the following User-Agent:

`sqlmap/stable`

### SQL Query Analysis

The server's response exposed its SQL query through a debug message:

```sql
SELECT id, username, email
FROM users
WHERE username LIKE '%%' OR '1'='1%'
```

Because the application handled the user input insecurely, the injected condition altered the intended query.

The response revealed multiple database records, including:

- Alice
- Bob
- Admin
- Flag

The attacker successfully retrieved the following flag from the database:

**Flag:** `THM{dumped_the_db}`

### Answers

**Q1: What password does the attacker successfully identify in the brute-force attack?**

Answer: `astrongpassword123`

**Q2: What is the flag the attacker found in the database using SQLi?**

Answer: `THM{dumped_the_db}`

## Task 6 - Web Application Firewall (WAF)

This task introduced Web Application Firewalls and their role in protecting web applications.

A WAF inspects incoming web requests and determines whether they should be allowed or blocked based on predefined security rules.

### Common WAF Rules

Some examples covered in this task:

- Blocking known malicious User-Agent strings.
- Blocking requests from known malicious IP addresses.
- Creating application-specific filtering rules.
- Rate-limiting repeated requests.
- Using CAPTCHA challenges to distinguish legitimate users from automated traffic.

### Custom Firewall Rule

The challenge required creating a rule that blocks requests containing the User-Agent `BotTHM`.

The rule:

```text
IF User-Agent contains "BotTHM"
THEN BLOCK
```

### Answers

**Q1: What do WAFs inspect and filter?**

Answer: `web requests`

**Q2: Create a custom firewall rule to block any User-Agent that matches "BotTHM".**

Answer:

`if user-agent contains "BotTHM" then BLOCK`

## Task 7 - Conclusion

Successfully completed the Detecting Web Attacks room.

### Key Takeaways

This room helped me understand how web attacks can be investigated from a defensive security perspective.

The most important things I learned:

1. **Client-side vs. Server-side Attacks:** Understanding the differences between attacks targeting users and attacks targeting backend infrastructure.
2. **Log Analysis:** Identifying suspicious behavior through repeated requests, unusual response codes, and suspicious User-Agent strings.
3. **Network Traffic Analysis:** Using Wireshark to inspect HTTP requests and responses, identify credentials exposed in lab traffic, and investigate SQL injection attempts.
4. **SQL Injection Detection:** Understanding how improperly handled user input can expose database records.
5. **Web Application Firewalls:** Learning how WAF rules can detect and block suspicious web requests.

One of the biggest takeaways was understanding the difference between log-based detection and network-based detection.

While access logs helped identify the attack sequence, Wireshark provided deeper visibility into the actual HTTP requests and responses, allowing me to identify the compromised password and the database flag.

**Room completed!**
