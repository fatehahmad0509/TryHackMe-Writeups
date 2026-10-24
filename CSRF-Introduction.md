# TryHackMe — CSRF Introduction

## 1. Introduction

**CSRF (Cross-Site Request Forgery)** is a web security vulnerability that allows an attacker to trick an authenticated user's browser into performing actions on a web application without the user's knowledge or consent.

When a user logs in to a web application, their browser typically stores a session cookie. The browser may automatically include this cookie in subsequent requests to the target application, allowing the server to identify the user's session.

If the application does not properly verify whether a request was intentionally initiated by the user, an attacker may be able to abuse this behavior.

In this lab, I explored how CSRF attacks work, how weak protections can be identified, and how these vulnerabilities can be prevented using the StaffHub employee portal provided by TryHackMe.

## 2. How CSRF Works

CSRF exploits the trust relationship between a user's browser and a web application.

For a CSRF attack to succeed, the following conditions generally need to be met:

- The victim must have an authenticated session with the target application.
- The application must provide a state-changing action, such as updating an email address or modifying account settings.
- The server must fail to adequately verify whether the request originated from a legitimate user interaction.
- The browser must be able to send the relevant session cookie with the forged request.

For example, a user may be logged in to StaffHub while visiting an unrelated website. That website could cause the user's browser to send a request to StaffHub. If the application lacks adequate CSRF protection, the server may accept the request.

**Key takeaway:** CSRF primarily abuses an existing authenticated session and the browser's cookie-handling behavior rather than stealing the user's password.

## 3. Identifying CSRF Vulnerabilities

When testing for CSRF vulnerabilities, pentesters should focus on HTTP requests that change the application's state.

Common examples include:

- Changing an email address
- Updating account settings
- Modifying a user's role
- Changing security preferences
- Performing financial transactions

When inspecting a request, a pentester should consider the following questions:

1. Which HTTP method does the request use?
2. Does the request contain a CSRF token?
3. Is the token predictable or reusable?
4. Does the server validate the token correctly?
5. Can the request be accepted when initiated from outside the legitimate application?
6. How do session cookies and the `SameSite` policy behave?

Both GET and POST requests should be examined during CSRF testing. Using POST does not automatically prevent CSRF. GET requests that modify application state are particularly risky.

## 4. CSRF Exploitation Using an HTML Form

### Objective

Determine whether the email-update functionality in StaffHub can be triggered without proper CSRF protection.

### Analysis

The email-update form in the application used an HTML structure similar to the following:

```html
<form action="update_email.php" method="POST">
    <input type="email" name="email" required>
    <button type="submit">Update Email</button>
</form>
```

The request sent the new email address to the server through the `email` parameter. The form did not contain a CSRF token, and the application did not adequately verify the origin of the request.

### Testing Process

1. Logged in to StaffHub using the `user:user` credentials.
2. Inspected the email-update functionality.
3. Identified the HTTP method and request parameters.
4. Created an external HTML form that reproduced the same action in the authorized lab environment.
5. Configured the form to submit a new email address to the target application.
6. Opened the test page in the same browser where the StaffHub session was active.
7. Verified that the application accepted the request and updated the email address.

### Result

Because the application did not adequately verify whether the request originated from a legitimate user interaction, it accepted the state-changing request.

**Lab result:** The following flag was obtained after changing the email address to `attacker@evilmail.thm`:

`THM{Got_The_Evil_Email001}`

The second flag obtained after changing the email address to `special@evilmail.thm` was:

`THM{My_Special_Email007}`

This exercise demonstrated that an account setting could be modified through an external webpage when appropriate CSRF protection was missing.

## 5. Exploitation Using a Weak CSRF Token

### Objective

Investigate whether simply including a CSRF token is sufficient to protect an application.

### Analysis

The role-update functionality in StaffHub used a CSRF token:

```html
<input type="hidden" name="csrf_token" value="YWRtaW4=">
```

The token was the Base64-encoded representation of the value `admin`:

```text
admin → YWRtaW4=
```

Base64 is an encoding scheme, not encryption or a secure token-generation method. It converts data into another representation that can be decoded. Therefore, generating a token from predictable information, such as a user's role, can make the protection ineffective.

### Testing Process

1. Inspected the user-role update functionality.
2. Analyzed the CSRF token included in the request.
3. Identified that the token was generated from a predictable value using Base64 encoding.
4. Created a request containing the expected parameters and token within the lab environment.
5. Triggered the request through a browser with an active authenticated session.
6. Verified that the user's role changed from `admin` to `staff`.

### Result

The application checked for the presence of a token, but the token was not generated securely. Consequently, the protection could be bypassed.

The resulting flag was:

`THM{Weak_CSRF_Role_001}`

**Key takeaway:** A CSRF token does not provide effective protection if it is predictable, easily reproducible, or insufficiently bound to the user's session.

## 6. Best Practices for CSRF Testing

The following practices were covered during the lab:

- **Identify state-changing requests:** Focus on functionality such as account settings, email updates, and role changes.
- **Inspect CSRF tokens:** Determine whether a token exists and investigate how it is generated.
- **Evaluate token validation:** Check whether the server validates the token correctly for the relevant user session and operation.
- **Review HTTP methods:** Do not treat the use of POST as sufficient CSRF protection.
- **Test requests in a controlled environment:** Verify whether requests originating outside the application are accepted in an authorized testing environment.
- **Inspect cookie behavior:** Examine when session cookies are sent and how their `SameSite` attributes are configured.
- **Verify the result:** Confirm whether the intended action actually occurred through the application interface or authorized test logs.

## 7. CSRF Prevention Techniques

Secure applications should consider implementing the following protections together.

### Secure CSRF Tokens

Tokens should be generated using a cryptographically secure random source, be difficult to predict, and be validated correctly on the server. Where appropriate, they should be associated with the user's session and the relevant operation.

### Origin and Referer Validation

Where supported, the server can inspect the `Origin` header and, when appropriate, the `Referer` header to determine whether a request originated from an expected source. These checks may not be sufficient on their own in every scenario.

### SameSite Cookie Policy

Configuring session cookies with an appropriate `SameSite` attribute can restrict when cookies are sent with cross-site requests, reducing CSRF risk. However, this should complement rather than replace other protections.

### Avoid GET Requests for State-Changing Operations

GET requests should be used for retrieving information rather than modifying application state. State-changing operations should use appropriate HTTP methods. However, using POST alone does not prevent CSRF.

### Additional Verification

For sensitive operations, requiring reauthentication or explicit user confirmation can provide an additional layer of protection, depending on the risk associated with the action.

## 8. Conclusion

In this lab, I learned the fundamentals of CSRF, the role of session cookies, and how to inspect state-changing HTTP requests.

The practical exercises demonstrated that:

- CSRF exploits the trust relationship between a browser and a web application.
- An unprotected email-update function can be triggered through a forged HTML form.
- A predictable token encoded with Base64 does not provide secure CSRF protection.
- Proper token validation, HTTP method selection, session-cookie configuration, and origin validation are important considerations when testing and preventing CSRF vulnerabilities.

**Overall assessment:** CSRF protection involves more than simply adding a token to a request. The protection must be securely designed, validated on the server, and evaluated alongside the application's session-management mechanisms.

**Platform:** TryHackMe  
**Room:** CSRF Introduction  
**Focus area:** Web Application Security / CSRF Testing
