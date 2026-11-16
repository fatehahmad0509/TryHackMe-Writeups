# TryHackMe – Web Security Essentials

## Introduction

In this room, I learned the fundamentals of web security, explored common web infrastructure components, and examined the security measures used to protect web applications, web servers, and host machines.

The room covered the transition from desktop applications to web applications, common web security risks, defensive technologies, and practical security hardening.

## Task 1 – Introduction

This task introduced the importance of web security and explained why understanding web applications is essential for cybersecurity professionals.

**Learning objectives:**
- Understand the transition from desktop to web applications.
- Learn why web applications are common targets for attackers.
- Explore web infrastructure and defensive security tools.
- Practice securing a web application.

## Task 2 – Why Web?

I learned how applications gradually shifted from desktop environments to web-based platforms.

- **1990s:** Desktop applications were dominant.
- **2000s:** Dynamic web applications became increasingly popular.
- **2010s:** Cloud computing and SaaS expanded significantly.
- **Today:** Many applications can be accessed directly through a browser.

### Security Risks

Web applications are attractive targets because they are publicly accessible and often connected to databases and internal infrastructure.

**Web application owners face:**
- The responsibility of securing their applications 24/7.
- Protecting stored user data.
- Managing global accessibility.
- Keeping up with emerging threats.

**Web application users face:**
- Account compromise.
- Identity theft.
- Financial loss.
- Privacy violations.

### Real-World Examples

- **Equifax (2017):** Nearly 150 million Americans' sensitive information was compromised through an Apache vulnerability.
- **Capital One (2019):** A misconfigured WAF contributed to a breach affecting over 100 million customers.

**Key takeaway:** The web application owner is ultimately responsible for protecting users' data.

## Task 3 – Web Infrastructure

I learned about the three essential components of a web service.

### 1. Application
Contains the code, images, styles, and icons that determine how a website looks and functions.

### 2. Web Server
Receives incoming HTTP requests, processes them, and returns responses to users.

Common web servers:

- **Apache:** Commonly used for websites and WordPress hosting.
- **Nginx:** Known for high-performance web applications.
- **Microsoft IIS:** Commonly used in enterprise environments.

### 3. Host Machine
The underlying operating system and environment running the web server and application, such as Linux or Windows.

### Request-Response Cycle

When a user visits a website:

1. The browser sends an HTTP request.
2. The web server receives the request.
3. The application processes it.
4. The server returns an HTTP response.

**Key takeaway:** Understanding these three components is essential for identifying and preventing web security vulnerabilities.

## Task 4 – Protecting the Web

This task covered security practices for each component of a web service.

### Protecting the Application

- **Secure Coding:** Avoid insecure functions and handle errors properly.
- **Input Validation & Sanitization:** Validate user input to prevent injection attacks.
- **Access Control:** Restrict access according to user roles.

### Protecting the Web Server

- **Logging:** Record incoming requests using access logs.
- **Web Application Firewall (WAF):** Filter and block malicious HTTP traffic.
- **Content Delivery Network (CDN):** Reduce direct exposure to the origin server.

### Protecting the Host Machine

- **Least Privilege:** Run services with low-privilege accounts.
- **System Hardening:** Disable unnecessary services and close unused ports.
- **Antivirus:** Detect and block known malicious files and programs.

### Additional Security Practices

- **Strong Authentication:** Protect administrative interfaces and systems.
- **Patch Management:** Keep applications, dependencies, web servers, and operating systems updated.

### Access Logs

I also learned about web server access logs and their importance in security investigations.

Access logs can contain:
- Client IP address.
- Timestamp.
- Requested URL.
- HTTP response status.
- Response size.
- Referrer.
- User-Agent.

GET requests are generally used to retrieve resources, while POST requests are commonly used to submit data.

These logs help security analysts reconstruct events and investigate suspicious activity.

## Task 5 – Defense Systems

This task introduced three important defensive technologies.

### Content Delivery Network (CDN)

A CDN distributes cached content through geographically distributed edge servers.

**Security benefits:**
- IP masking: Hides the origin server's IP address.
- DDoS protection: Absorbs large amounts of traffic.
- Enforced HTTPS: Supports encrypted communication through TLS.
- Integrated WAF: Provides additional HTTP traffic filtering.

Examples include Cloudflare CDN, Amazon CloudFront, and Azure Front Door.

### Web Application Firewall (WAF)

A WAF inspects incoming HTTP traffic and blocks or logs potentially harmful requests.

There are three main types:

1. **Cloud-based (Reverse Proxy):** Sits in front of the web server.
2. **Host-based:** Runs directly on the web server.
3. **Network-based:** Operates as a physical or virtual network appliance.

### WAF Detection Techniques

| Technique | Description |
|---|---|
| Signature-Based Detection | Matches known malicious patterns. |
| Heuristic-Based Detection | Analyzes request context and behavior. |
| Anomaly & Behavioral Analysis | Detects unusual traffic patterns. |
| Location & IP Reputation Filtering | Uses location and threat intelligence to filter traffic. |

### Antivirus (AV)

Antivirus software primarily protects endpoints such as desktops, laptops, and servers.

Most antivirus solutions use signature-based detection to identify known malicious files.

They can also help detect malicious uploads, web shells, and other malware.

**Key takeaway:** No single security solution is sufficient. Multiple defensive layers provide stronger protection.

## Task 6 – Practice Scenario

In this task, I worked with the Secure-A-Site practical scenario.

The objective was to secure three different components of a web service.

### 1. Web Application

<code>THM{web_app_secured!}</code>

### 2. Web Server

<code>THM{server_security_expert!}</code>

### 3. Host Machine

<code>THM{the_final_security_layer!}</code>

I successfully completed all three security sections and obtained their respective flags.

## Conclusion

This room provided a solid introduction to defensive web security.

I learned about:
- The evolution of web applications.
- Common web application security risks.
- Web infrastructure and its three main components.
- Secure coding, input validation, and access control.
- Access logs and their role in incident investigation.
- CDNs, WAFs, and antivirus solutions.
- Security hardening and patch management.

**Personal Progress:**

I successfully completed the room and answered all knowledge-based questions correctly.

The practical scenario also helped me understand how different security measures work together to protect a web service.

Another step completed in my cybersecurity learning journey!
