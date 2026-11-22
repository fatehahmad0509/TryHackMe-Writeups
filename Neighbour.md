# TryHackMe – Neighbour

## Overview

- **Room:** Neighbour
- **Category:** Web Security
- **Difficulty:** Easy
- **Vulnerability:** IDOR (Insecure Direct Object Reference)
- **Target:** `http://10.128.161.109`

## 1. Initial Access

The challenge provides a web application called **Authentication Anywhere**, which allows users to log in using a username and password.

While inspecting the website's HTML source code, I discovered the following credentials:

```text
Username: guest
Password: guest
```

I used these credentials to log in successfully.

## 2. Identifying the Vulnerability

After logging in, I was redirected to the following URL:

```text
http://10.128.161.109/profile.php?user=guest
```

I noticed that the profile page uses a URL parameter named `user` to determine which user's profile is displayed.

This caught my attention because the application appeared to rely on a user-controlled parameter to identify profiles.

Since the room also suggested looking into IDOR vulnerabilities, I decided to investigate whether changing this parameter would allow access to another user's profile.

## 3. Exploiting IDOR

I modified the URL parameter from `guest` to `admin`:

**Original:**
```text
/profile.php?user=guest
```

**Modified:**
```text
/profile.php?user=admin
```

The application displayed the admin user's profile without requiring additional authentication or authorization.

This confirmed an **Insecure Direct Object Reference (IDOR)** vulnerability.

## 4. Flag

After accessing the admin profile, I found the flag:

```text
flag{66be95c478473d91a5358f2440c7af1f}
```

## 5. What I Learned

- How to identify potential IDOR vulnerabilities through URL parameters.
- Why user-controlled parameters should not determine access permissions.
- How missing server-side authorization checks can expose other users' private information.
- How small changes to application requests can reveal serious access control weaknesses.

## 6. Mitigation

To prevent IDOR vulnerabilities:

- Implement server-side authorization checks for every resource request.
- Verify that the authenticated user has permission to access the requested profile.
- Never rely on client-controlled parameters as proof of authorization.
- Apply proper access control policies consistently across the application.

## Conclusion

The Neighbour room demonstrated how a simple URL parameter manipulation can expose another user's private information when proper authorization checks are missing.

**Status: Completed**
