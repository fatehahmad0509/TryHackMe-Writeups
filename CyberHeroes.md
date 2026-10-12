# TryHackMe — CyberHeroes

## Overview

- **Platform:** TryHackMe
- **Room:** CyberHeroes
- **Category:** Web Security
- **Vulnerability:** Client-Side Authentication / Hardcoded Credentials
- **Difficulty:** Easy

## Objective

The objective of this challenge was to discover a vulnerability in the login mechanism, successfully authenticate, and retrieve the flag.

## Methodology

### 1. Initial SQL Injection Attempts

I started by testing several SQL injection payloads against the login form. However, these attempts did not lead to a successful login.

I then decided to inspect the page source to understand how the authentication mechanism worked.

### 2. Inspecting the Page Source

While examining the HTML source code, I discovered a JavaScript function named `authenticate()` that handled the login process.

The function retrieved the username and password from the input fields and compared them against hardcoded values:

```javascript
if (a.value == "h3ck3rBoi" &
    b.value == RevereString("54321@terceSrepuS")) {
    // Authentication logic
}
```

This revealed that the authentication check was performed on the client side rather than being securely enforced by a server-side authentication mechanism.

### 3. Recovering the Credentials

The username was directly visible in the JavaScript code:

```text
Username: h3ck3rBoi
```

The password was stored in reverse order and processed using a function that reversed the string:

```javascript
const RevereString = str => [...str].reverse().join('');
```

Reversing the string `54321@terceSrepuS` revealed the password:

```text
Password: SuperSecret@12345
```

Since both credentials were embedded in the client-side source code, anyone inspecting the page could recover them.

### 4. Logging In and Retrieving the Flag

I entered the recovered credentials into the login form and successfully authenticated.

After successful authentication, the JavaScript sent an HTTP GET request to a path containing the flag file. The response was then displayed on the page.

The challenge was completed successfully.

## Flag

```text
flag{edb0be532c540b1a150c3a7e85d2466e}
```

## Vulnerability Analysis

The primary issue was the use of client-side authentication with hardcoded credentials.

Client-side JavaScript is delivered to the user's browser and can be inspected. Therefore, credentials embedded in the source code cannot be considered secret. Reversing a string is only an obfuscation technique; it does not provide meaningful security.

Authentication and authorization must be enforced on the server. Sensitive credentials and protected resources should never rely on client-side checks alone.

## Key Takeaways

- Inspecting the page source can reveal authentication logic and sensitive information.
- Not every login vulnerability involves SQL injection.
- Hardcoded credentials in client-side JavaScript are insecure.
- Simple string reversal is obfuscation, not encryption.
- Understanding how an application works is often more useful than blindly trying payloads.

## AI Usage

AI suggested the possibility that the login validation might be implemented in the HTML/JavaScript source code. I then inspected the source, identified the hardcoded credentials, understood the string-reversal function, and used the credentials to complete the challenge.

## Conclusion

This challenge demonstrated how insecure client-side authentication can expose credentials and allow unauthorized access. Instead of continuing with SQL injection attempts, inspecting the page source revealed the actual weakness and provided a straightforward path to the flag.
