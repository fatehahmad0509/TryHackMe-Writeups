# TryHackMe Writeup: CSRF

**Platform:** TryHackMe  
**Room:** CSRF  
**Category:** Web Application Security  
**Difficulty:** From fundamental concepts to advanced CSRF scenarios  
**Status:** Completed ✅

## 1. Introduction — What Is CSRF?

Cross-Site Request Forgery (CSRF) is a web security vulnerability that allows an attacker to trick a user's browser into sending an unwanted request to a trusted web application where the user is authenticated.

The attack relies on the fact that browsers automatically include relevant session cookies in requests under certain conditions.

For example, suppose a user is logged into an online banking application. If an attacker can trick the user's browser into sending a request that performs a banking operation, and the application does not implement sufficient CSRF protection, an unwanted action may be performed using the user's authenticated session.

CSRF attacks may affect actions such as:

- Changing an email address.
- Modifying account settings.
- Changing a password.
- Performing financial transactions.
- Logging a user out of an application.

### The Basic CSRF Attack Flow

1. The attacker learns how the target application performs a particular action.
2. The victim opens a link or webpage prepared by the attacker.
3. Under the appropriate conditions, the browser attaches the target site's authentication cookies to a request.
4. If the server does not adequately verify whether the request represents a legitimate user action, it may process the request.

**Key takeaway:** CSRF does not necessarily require stealing the victim's password. Instead, it attempts to abuse the privileges of an existing authenticated session.

## 2. Types of CSRF Attacks

### 2.1. Traditional CSRF

Traditional CSRF attacks involve tricking a user's browser into submitting a form or sending a request that changes application state.

For example, a bank's money transfer functionality might accept the following parameters:

- `to_account`: The destination account.
- `amount`: The transfer amount.

Even if the application validates these parameters, that does not guarantee that the request is protected against CSRF. A vulnerability may exist if the server accepts the authenticated session without adequately verifying whether the request represents a legitimate user action.

### 2.2. XMLHttpRequest / Asynchronous CSRF

Modern web applications often use `XMLHttpRequest` or the Fetch API to communicate with servers without reloading the entire page.

If an application is vulnerable to CSRF, a browser may be tricked into sending an unwanted request to the application while the user is browsing another webpage.

For example, consider an email application that allows users to modify their email forwarding settings through an API endpoint. If the application lacks sufficient protections, an attacker may attempt to change these settings through the victim's browser.

The important distinction is that **using asynchronous requests does not automatically eliminate CSRF risks**. However, the Same-Origin Policy, CORS configuration, cookie policies, and request characteristics can affect whether a particular attack is possible.

### 2.3. Flash-Based CSRF

This section covered historical CSRF attacks that took advantage of vulnerabilities in Adobe Flash components.

Flash files used in these scenarios had the `.swf` extension. Since Adobe Flash Player support ended on December 31, 2020, this topic is primarily useful for understanding legacy systems and historical attack techniques.

## 3. Hidden Image / Link Exploitation

This section demonstrated how hidden links or images could be used in CSRF attacks.

An image's `src` attribute or a link's `href` attribute can cause the browser to send a request to a specified URL.

Placing an image where the user cannot easily notice it may cause a request to be initiated in the background. However, whether the request succeeds depends on the target URL's behavior, HTTP method, cookie transmission rules, and application security controls.

In the lab, I examined a banking application that performed money transfers using the user's authenticated session.

### Identified Issue

The application accepted transfer parameters but did not implement sufficient CSRF protection to verify whether the request represented a legitimate user action.

### Mitigation

A CSRF token validation mechanism was introduced. The server was configured to require a valid token in incoming requests.

This demonstrated that providing the correct transfer parameters alone was not sufficient to make a request trustworthy.

**Key takeaway:** Valid parameters do not automatically make a request legitimate. Server-side validation is essential.

### Lab Results

- Successful transfer flag: `THM{SUCCESSFUL_ATTACK}`
- Flag returned when a CSRF attack was detected: `THM{INVALID_CSRF_TOKEN}`
- Does a hidden image's `src` attribute have to point to a valid image file? **No.**

## 4. Double Submit Cookie Bypass

This section explored the Double Submit Cookie technique and demonstrated how an incorrectly implemented version can become ineffective.

### How Does Double Submit Cookie Work?

In this approach, one copy of a CSRF token is stored in a cookie, while another copy is included in a form field or request parameter.

The server checks whether the two values match. If they do not match, the request should be rejected.

However, the security of this approach depends on how the token is generated and whether an attacker can obtain or influence the token or cookie values.

### Issues Identified in the Lab

The banking application used a predictable CSRF token. The token was Base64-encoded, and decoding it revealed a value corresponding to an account number.

Base64 is an encoding scheme, not encryption. Encoding a value with Base64 does not make it secret or unpredictable.

The lab also demonstrated a scenario in which an attacker-controlled subdomain could influence a cookie associated with the parent domain.

When these weaknesses were combined, simply checking whether the cookie value matched the form value was not sufficient to prevent the attack.

### Mitigation

- Generate cryptographically secure and unpredictable tokens.
- Validate tokens on the server side.
- Configure cookie attributes such as `Domain`, `Path`, `Secure`, `HttpOnly`, and `SameSite` appropriately.
- Prevent untrusted subdomains from influencing cookies belonging to the main application.
- Where appropriate, securely bind tokens to the user's session.

### Lab Results

- Decoded token value: `GB82MYBANK5699`
- Password used in the lab: `GB82MYBANK5697`
- Flag after secure, unpredictable token generation was implemented: `THM{SECURED_CSRF}`
- Hidden field name: `csrf_token`
- Is session cookie hijacking technically possible in a web application? **Yes.** However, this does not mean that it is possible in every application or under all conditions.

## 5. SameSite Cookie Bypass

SameSite is a cookie attribute that controls the conditions under which browsers send cookies with cross-site requests.

### SameSite Values

**Strict:** Restricts cookies from being sent in cross-site contexts more strictly.

**Lax:** Allows cookies to be sent in certain cross-site top-level navigations, particularly with safe HTTP methods such as GET. In general, it is more restrictive with cross-site POST requests.

**None:** Allows cookies to be sent with cross-site requests. Modern browsers generally require the `Secure` attribute when this value is used.

SameSite is not a complete solution that prevents every CSRF attack. In particular, GET requests should not be used to perform state-changing operations.

### 5.1. GET Requests with SameSite=Lax

In the lab, I examined the banking application's logout functionality.

The application checked the value of a particular `logout` cookie before terminating the user's session. Because the cookie was configured with `SameSite=Lax`, it could be sent during qualifying cross-site top-level GET navigations.

When this behavior was combined with a logout function that accepted GET requests, a CSRF risk emerged.

**Root cause:** A state-changing operation, such as logging out, could be triggered through a GET request initiated by an untrusted webpage.

### 5.2. Lax and POST Requests

This section covered a second scenario based on specific browser behavior.

Some Chrome versions have applied a temporary exception for cookies created without an explicitly specified SameSite attribute. Under certain conditions, recently created cookies could be included in cross-site POST requests.

The lab examined this behavior using an `isBanned` cookie. The scenario combined a logout operation, which refreshed the cookie, with a subsequent POST request.

This behavior depends on the browser and the time at which the cookie was created. It should not be treated as a universal rule for every browser or every `SameSite=Lax` cookie.

### Lab Results

- `logout` cookie value: `7kRt2x9LpQyW`
- Successful logout flag: `THM{LOGGED_OUT}`
- Flag returned when the modified cookie value triggered attack detection: `THM{ATTACK_DETECTED}`
- Flag after the `isBanned` cookie was updated: `THM{USER_IS_B@NNED}`

### Key Takeaways

- Inspect all relevant cookie attributes.
- Using GET requests for state-changing operations can introduce security risks.
- SameSite policies are not a complete replacement for CSRF tokens.
- Browser-specific exceptions must be considered when evaluating application security.

## 6. Additional CSRF Techniques

### 6.1. XMLHttpRequest Exploitation

`XMLHttpRequest` is an API that allows JavaScript to send HTTP requests.

The lab examined POST requests, the `Content-Type` header, and custom headers such as `X-Requested-With`.

The important lesson is that using AJAX does not automatically prevent CSRF. However, custom headers, CORS checks, and browser security policies can restrict certain attack scenarios.

### 6.2. Same-Origin Policy and CORS

The Same-Origin Policy (SOP) is a fundamental browser security policy that restricts how resources from different origins can interact.

An origin consists of the scheme, hostname, and port.

Cross-Origin Resource Sharing (CORS) is a mechanism through which servers specify which origins are permitted to access responses through browser-based requests.

The lab examined the following header:

`Access-Control-Allow-Origin: *`

This header can allow any origin to read a response in applicable CORS scenarios. However, **the presence of this header alone does not mean that all cross-origin requests containing credentials will succeed**.

CORS, SOP, and CSRF are related but distinct concepts. A browser preventing JavaScript from reading a response does not necessarily mean that the request was never sent to the server.

### 6.3. Referer Header

The `Referer` header can contain information about the URL of the page that initiated the current request.

Some applications inspect this header to help determine whether a request originated from an expected source.

However, the `Referer` header may be omitted due to privacy settings, browser policies, or other conditions. Therefore, it should not be the only CSRF defense. Appropriate CSRF tokens and other server-side security controls should also be implemented.

### Lab Results

- Policy that restricts cross-origin interactions: `Same-Origin Policy`
- Answer to the lab's CORS configuration question: `yea`
- Header that can contain the previous page's URL: `Referer`

## 7. CSRF Defense Mechanisms

The final technical section covered the checks that penetration testers should perform and the defenses developers should implement.

### From a Pentester's Perspective

- Test whether state-changing requests are protected against CSRF.
- Verify that tokens are present and correctly validated by the server.
- Evaluate whether tokens are predictable.
- Examine session management and cookie attributes.
- Check how GET, POST, and other HTTP methods are used.
- Review CORS configuration and `Referer` validation.
- Inspect request and response parameters alongside the application's security controls.

### From a Secure Developer's Perspective

**1. CSRF Tokens:** Tokens should be properly validated for protected operations and generated using unpredictable values.

**2. SameSite:** Use appropriate `Strict` or `Lax` cookie settings according to the application's requirements.

**3. Referer Policy:** Origin validation can provide an additional layer of defense, but it should not be treated as sufficient on its own.

**4. Content Security Policy (CSP):** Restrict trusted content sources to reduce certain script injection risks. However, CSP alone is not a general CSRF defense.

**5. Double Submit Cookie:** Implement cookie and request-parameter comparison with secure token generation and an appropriate design.

**6. CAPTCHA:** CAPTCHA may provide additional protection for certain operations, but it is not a general-purpose replacement for CSRF defenses.

**7. Server-Side Validation:** Authorization and security checks must be enforced on the server. Client-side checks alone are insufficient.

The final lab question asked whether keeping CSRF tokens predictable is good practice for faster implementation.

**Answer: No.**

## 8. Key Concepts

| Concept | Description |
|---|---|
| CSRF | A vulnerability that can cause unwanted actions through a user's browser |
| CSRF Token | A value used to validate requests when generated and verified appropriately |
| Cookie | Data stored in a browser and sent to a server under applicable conditions |
| Double Submit Cookie | An approach that compares a token in a cookie with a token in a request parameter |
| SameSite | A cookie attribute that restricts cookie transmission in cross-site requests |
| SOP | A browser security policy that restricts interactions between different origins |
| CORS | A mechanism that specifies which origins can access browser-based responses |
| XMLHttpRequest | An API for sending HTTP requests from JavaScript |
| Referer | An HTTP header that can contain information about the preceding page |
| Session Hijacking | Unauthorized acquisition or use of a user's session |

## 9. Conclusion

In the TryHackMe CSRF room, I explored the fundamentals of CSRF and examined hidden image and link scenarios, asynchronous requests, the Double Submit Cookie technique, SameSite policies, and various defensive mechanisms.

The room demonstrated that examining request parameters alone is not enough. The session associated with the request, the conditions under which the browser sends cookies, and the server's validation mechanisms must all be considered together.

### What I Learned

- The fundamental mechanics of CSRF attacks.
- The role of HTTP requests and cookies in CSRF.
- The risks associated with predictable CSRF tokens.
- The differences between SameSite, SOP, and CORS.
- What penetration testers should examine when testing for CSRF.
- The importance of server-side token validation and secure cookie configuration.

**Room Status:** Completed ✅

This lab was completed for educational purposes to improve my theoretical knowledge and hands-on experience in web application security and CSRF testing.
