# TryHackMe — Enumeration & Brute Force

## Overview

**Lab:** Enumeration & Brute Force
**Platform:** TryHackMe
**Category:** Web Application Security
**Topics:** Authentication Enumeration, Verbose Error Messages, Password Reset Vulnerabilities, HTTP Basic Authentication, Brute Force Attacks

## 1. Introduction

Authentication Enumeration is a security testing technique carried out to gather information about a web application's usernames, email addresses, and authentication mechanisms.

Applications can unintentionally leak information through verbose error messages, registration forms, password reset functions, or inconsistent login responses.

In this lab, I learned how these kinds of weaknesses can be detected, and how the information obtained can facilitate subsequent password attacks.

### Learning Objectives

- Understand authentication enumeration and its relationship to brute-force attacks.
- Identify valid email addresses through verbose error messages.
- Understand vulnerabilities in password reset tokens.
- Perform tests on HTTP Basic Authentication using Burp Suite Intruder.
- Learn historical URL discovery and Google Dorks techniques.

## 2. Authentication Enumeration

Authentication enumeration involves examining how an application responds to different authentication inputs.

### Identifying Valid Usernames

Some applications return different error messages depending on whether the submitted account exists or not.

For example:

- **Email does not exist** — Indicates the submitted email address is not registered.
- **Invalid password** — Indicates the email address may be registered, but the submitted password is incorrect.

These differences can allow attackers to identify valid accounts without directly accessing the application's database.

### Other Enumeration Sources

Other sources that can leak information about user accounts include:

- Registration forms that reveal whether an email address has already been registered.
- Password reset forms that return different responses for existing versus non-existing accounts.
- Validation messages that reveal password policies.
- Information obtained from previous data breaches.

**Key takeaway:** Authentication responses should not reveal whether an account exists.

### Question

Which error messages can give attackers the ability to confirm valid usernames?

**Answer:** Verbose errors

## 3. Understanding Verbose Errors

Verbose error messages provide detailed information about an application's internal workings.

While useful during debugging, improperly configured error handling can lead to sensitive information disclosure.

This information can include:

- The application's file paths and directory structure.
- Database tables and application details.
- Validity of a username or email address.
- Server-side validation logic.

### Techniques That Can Trigger Verbose Errors

The lab covered the following techniques:

- Invalid input attempts.
- SQL Injection.
- File inclusion and Path Traversal.
- Form and parameter manipulation.
- Application fuzzing with tools like Burp Suite Intruder.

### Email Enumeration Lab

**Endpoint:** `/labs/verbose_login/`

The application returned different responses depending on whether the submitted email address was registered or not.

I downloaded the provided email list and saved it in the same directory as the Python enumeration script. I then ran the script to send requests to the application's login endpoint.

The script sent an HTTP POST request for each email address, using a sample password. It then examined the JSON response to check whether the application returned the `Email does not exist` message.

The results were classified as valid or invalid email addresses based on this response behavior.

### Result

Valid email address identified by the script:

```text
canderson@gmail.com
```

### Key Takeaway

Automating response analysis can make the enumeration process more efficient. However, this method depends on the application's response behavior and should only be used in authorized tests.

## 4. Vulnerabilities in the Password Reset Flow

The password reset function allows users to regain access to their accounts. However, when implemented incorrectly, it can lead to security vulnerabilities.

### Common Vulnerabilities

- **Predictable tokens:** Reset tokens generated from small numeric ranges or predictable patterns can be guessed.
- **Token lifetime issues:** Tokens that remain valid for a long time increase the opportunity for abuse.
- **Insufficient validation:** Weak authentication mechanisms can lead to unauthorized password resets.
- **Information leakage:** Returning different responses for registered versus non-registered accounts can allow valid users to be identified.
- **Insecure transmission:** Sending sensitive reset tokens over unencrypted HTTP can lead to them being intercepted.

### Predictable Token Lab

**Endpoint:** `/labs/predictable_tokens/`

The lab showed that the password reset token was generated with the following code:

```php
$token = mt_rand(100, 200);
```

This application limited the token to only 101 distinct values. As a result, the number of possible values was quite low.

The password reset flow was examined on the application using the `admin@admin.com` account.

### Steps Followed

1. I opened the password reset page and submitted the target email address.
2. I examined the token reset flow shown in the lab.
3. I crafted a request by adding a token parameter to the `reset_password.php` endpoint.
4. I intercepted the request and sent it to Burp Suite Intruder.
5. I configured a Sniper attack to try values between 100 and 200.
6. I compared the responses to try to identify the valid token.
7. I determined that the working token was `192`.
8. I obtained a new password using the lab's password reset function.
9. I logged in with the new password and obtained the flag value.

### Result

Valid reset token: `192`

Flag:

```text
THM{50_pr3d1ct4BL333!!}
```

### Key Takeaway

Password reset tokens should be generated using a cryptographically secure random number generator, have sufficient entropy, expire quickly, and not be reusable once used.

A small number of possible tokens makes brute-force attacks far more practical.

## 5. HTTP Basic Authentication

HTTP Basic Authentication is a simple authentication method defined in the RFC 7617 standard.

Credentials are sent via the HTTP `Authorization` header using Base64 encoding.

The general format of the header is:

```text
Authorization: Basic <credentials>
```

The credentials are formed by Base64-encoding text in the format `username:password`.

The general process is as follows:

```text
username:password
        |
        v
   Base64 encoding
        |
        v
Authorization header
```

**Important:** Base64 is an encoding method, not an encryption method. HTTPS must be used to protect credentials during transmission.

### Basic Authentication Lab

**Endpoint:** `/labs/basic_auth/`

This lab showed how HTTP Basic Authentication credentials can be tested using Burp Suite Intruder.

### Steps Followed

1. I opened the Basic Authentication lab.
2. I entered test credentials in the browser's authentication prompt.
3. I captured the resulting HTTP request with Burp Suite.
4. I sent the request to Intruder.
5. I examined the Base64-encoded credentials in the `Authorization` header.
6. I configured payload processing rules that combined the username `admin` with candidate passwords and Base64-encoded the result.
7. I used the provided `500-worst-passwords.txt` password list.
8. I launched the Intruder attack and compared the HTTP responses.
9. I determined that the working password was `yellow`.
10. I obtained the flag after successful authentication.

### Result

- Username: `admin`
- Password: `yellow`

Flag:

```text
THM{b4$$1C_AuTTHHH}
```

### Key Takeaway

Basic Authentication relies on the security of the credentials sent with every request. Weak passwords can leave accounts vulnerable to password guessing attacks.

HTTPS, strong passwords, rate limiting, monitoring, and proper authentication controls help mitigate this risk.

## 6. Wayback URLs and Google Dorks

This section introduced additional reconnaissance techniques. The section was informational; I did not carry out a separate hands-on exploitation task here.

### Wayback URLs

The Wayback Machine, provided by the Internet Archive, allows viewing historically saved versions of websites.

Historical URLs can reveal old pages, directories, or resources that are no longer linked.

The lab introduced the `waybackurls` command-line tool for retrieving URLs archived by the Wayback Machine.

Example usage:

```bash
./waybackurls example.com
```

The URLs obtained can be used to examine past application functionality and investigate endpoints that may have been forgotten.

Finding a historically archived URL does not mean the resource is still accessible or vulnerable today.

### Google Dorks

Google Dorks are specialized search queries crafted to find specific information using search engines.

Examples covered in the lab:

```text
site:example.com inurl:admin
```

Searches for pages on the specified domain that contain the word `admin` in the URL.

```text
filetype:log "password" site:example.com
```

Searches for indexed log files on the specified domain that contain the word `password`.

```text
intitle:"index of" "backup" site:example.com
```

Searches for pages whose title contains a phrase resembling a directory listing along with the word `backup`.

These queries can help discover publicly available resources. However, any findings should be verified and only examined within authorized scope.

## 7. Conclusion

In this lab, I learned that weaknesses in authentication processes can expose account information or lead to the abuse of password recovery mechanisms.

### What I Learned

- How verbose error messages can reveal valid email addresses.
- How response-based email enumeration can be automated with Python and the `requests` library.
- Why predictable password reset tokens are vulnerable to brute-force attacks.
- Using Burp Suite Intruder to test authentication requests.
- How Base64 encoding is used in the HTTP Basic Authentication method.
- How historical URLs and search engine queries contribute to the reconnaissance process.
- The importance of secure token generation, HTTPS, rate limiting, and consistent error messages.

### Final Assessment

Authentication security isn't just about checking whether a password is correct. Account enumeration, error handling, password recovery, token generation, and request processing all need to be taken into account during a security assessment.

Through this lab I practiced authentication enumeration and learned how seemingly small implementation mistakes can affect the security of a web application.

Lab completed.
