# TryHackMe — Become a Hacker

**Room:** Become a Hacker  
**Platform:** TryHackMe  
**Category:** Introduction to Offensive Security  
**Status:** Completed

## 1. Overview

In this room, I learned the basics of offensive security by testing a deliberately vulnerable web application. The exercises introduced hidden-page discovery, directory enumeration, password guessing, and basic usage of security tools such as Gobuster and Hydra.

The goal was to identify exposed pages, discover weak login credentials, and access a protected section of the test application.

## 2. Task 1 — What Is Offensive Security?

Offensive security involves testing systems and applications from an attacker's perspective to identify vulnerabilities before malicious actors can exploit them.

I learned the difference between:

- **Offensive Security:** Identifying and testing vulnerabilities through authorized attacks.
- **Defensive Security:** Monitoring, detecting, investigating, and preventing cyber threats.

**Answer:** Offensive Security

## 3. Task 2 — Hidden Page Discovery

### Objective

Discover a hidden page on the target web application, `http://www.onlineshop.thm/`.

### Method 1: Manual Enumeration

I tested several possible paths by appending common page names to the target URL:

- `/sitemap`
- `/mail`
- `/login`
- `/register`
- `/admin`

This process is called **manual directory or path enumeration**. It helps identify pages that are not directly linked from the main website.

The hidden page discovered was `/login`.

### Method 2: Automated Enumeration with Gobuster

I used Gobuster to automate the discovery process.

Command:

```bash
gobuster dir --url http://www.onlineshop.thm/ -w /usr/share/wordlists/dirbuster/directory-list.txt
```

**Command breakdown:**

- `gobuster`: Starts the Gobuster tool.
- `dir`: Selects directory and file enumeration mode.
- `--url`: Specifies the target URL.
- `-w`: Specifies the wordlist containing paths to test.

Gobuster sends requests for candidate paths and reports interesting HTTP responses, helping identify potentially accessible resources.

**Result:** Discovered the hidden `/login` page.

## 4. Task 3 — Login Authentication Testing

### Objective

Access the protected section of the application by identifying weak credentials.

### Manual Password Guessing

The username `admin` was provided as the starting point. I tested a small list of common passwords against the lab's login page.

This demonstrated how weak passwords can make authentication vulnerable to guessing attacks.

### Automated Testing with Hydra

The lab introduced Hydra as a tool for automating authentication attempts.

Command provided by the lab:

```bash
hydra -l admin -P passlist.txt www.onlineshop.thm http-post-form "/login:username=^USER^&password=^PASS^:F=incorrect" -V
```

**Command breakdown:**

- `hydra`: Starts the authentication-testing tool.
- `-l admin`: Specifies the username to test.
- `-P passlist.txt`: Supplies a list of candidate passwords.
- `www.onlineshop.thm`: Specifies the target host.
- `http-post-form`: Selects HTTP POST form authentication testing.
- `/login`: Identifies the login endpoint.
- `username=^USER^&password=^PASS^`: Defines where the candidate credentials are inserted.
- `F=incorrect`: Uses the presence of the text `incorrect` as an indicator of a failed login.
- `-V`: Displays individual attempts.

The lab's success condition allowed Hydra to identify valid credentials and demonstrate access to the protected page.

**Result:** Successfully accessed the secret page.

**Secret message:** `born_to_be_a_hacker`

## 5. Key Takeaways

Through this room, I learned:

1. What offensive security is and how it differs from defensive security.
2. How manual path enumeration can reveal hidden web pages.
3. How Gobuster automates directory and file discovery using wordlists.
4. How weak passwords can expose an application's protected functionality.
5. How Hydra automates login testing and interprets failed authentication responses.
6. Why organizations should use strong passwords, rate limiting, and appropriate authentication protections.

## 6. Security Recommendations

To reduce the risks demonstrated in this lab, developers should:

- Enforce strong passwords and encourage unique credentials.
- Implement rate limiting and appropriate protections against automated login attempts.
- Monitor authentication failures for suspicious patterns.
- Avoid relying on obscure URLs to protect sensitive pages.
- Enforce server-side authentication and authorization checks on every protected resource.
- Review exposed endpoints before deploying an application publicly.

## 7. Conclusion

This room provided a practical introduction to offensive security and basic web application testing.

I practiced discovering hidden pages through manual enumeration and Gobuster, then learned how weak authentication can be tested using manual attempts and Hydra.

The main lesson is that hidden pages and weak passwords can expose sensitive functionality when an application lacks proper security controls.

**Tools covered:** Gobuster, Hydra, wordlists, and a simulated web browser and terminal.

**Next step:** Continue practicing web enumeration and authentication testing in authorized lab environments, while learning how to interpret HTTP responses and document findings clearly.
