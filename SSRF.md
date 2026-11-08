# TryHackMe – SSRF | Room Writeup

**Platform:** TryHackMe  
**Room:** SSRF  
**Difficulty:** Beginner  
**Category:** Web Security  
**Status:** Completed

## Task 1 – Introduction

In this room, I learned about Server-Side Request Forgery (SSRF), how it works, different types of SSRF vulnerabilities, their potential impacts, and mitigation techniques.

First, I set up the AttackBox and Lab Machine environments. Then, I accessed the Human Resource Management System (HRMS) web application through `hrms.thm`.

## Task 2 – Anatomy of SSRF Attack

SSRF is a web application vulnerability that allows an attacker to make a server send unauthorized requests on their behalf.

This vulnerability may allow attackers to:

- Access sensitive information.
- Interact with internal network systems.
- Perform internal network reconnaissance.
- Cause Denial of Service (DoS).

**OWASP Average Weighted Impact:** 6.72

## Task 3 – Types of SSRF: Basic

### Scenario I – SSRF Against a Local Server

After visiting the HRMS application, I noticed that the URL contained a parameter named `url`.

Example:

`http://hrms.thm/?url=localhost/copyright`

I discovered that this parameter was not properly validated. By modifying its value, I was able to access different local files and expose sensitive configuration information.

As a result, I obtained the following credentials and information:

- Login username: `hrmsadmin`
- Login password: `hrmsadmin@123`
- Admin URL: `http://192.168.2.10/admin.php`

I used these credentials to log in to the HRMS panel and retrieve the flag.

**Flag:** `THM_{1NiT_S$rF}`

## Task 4 – Types of SSRF: Basic (Continued)

### Scenario II – Accessing an Internal Server

In this section, I learned how SSRF can be used to access internal servers.

I initially couldn't access the admin panel hosted at `192.168.2.10` directly from my machine.

However, I noticed that the HRMS application was already making requests to this internal server to retrieve employee information.

Since the URL used by the application could be modified, I discovered that the server could be tricked into requesting a different internal resource.

Using this behavior, I successfully accessed the admin panel through the vulnerable application.

**Flag:** `THM_{B@$ic_s$rF}`

## Task 5 – Types of SSRF: Blind

In this section, I learned about Blind SSRF and Out-of-Band SSRF.

### Blind SSRF

Blind SSRF occurs when an attacker can trigger requests from a vulnerable server but does not receive a direct response or feedback about the result.

### Out-of-Band SSRF

With this technique, an attacker attempts to observe requests through a separate communication channel.

During the lab, I created a simple HTTP server using Python. The server was designed to receive POST requests and save their contents to a `data.html` file.

This helped me understand how data sent by a vulnerable application can be observed through an external server.

### Time-Based SSRF

In this technique, instead of relying on direct responses, the attacker observes differences in request processing times.

These timing differences may provide clues about whether the server successfully reached the requested resource.

Additional information obtained:

- Virtual Directory Support: `disabled`
- PHP Extension Build: `API20190902,NTS`
- SSRF type that does not provide direct feedback: Blind SSRF

## Task 6 – A Classic Example: Crashing the Server

In this section, I learned that SSRF is not limited to unauthorized data access and can also lead to service disruption.

I discovered that the `url.php` file in the HRMS application was capable of loading images from external sources.

By examining the source code, I noticed that the application checked image sizes and that images larger than 100 KB could trigger a memory-related error.

I tested this scenario by loading the large image provided in the lab.

**Flag:** `THM_{$$rF_Cr@$h3D}`

## Task 7 – Remedial Measures

I learned several security measures that can help prevent SSRF vulnerabilities:

- Validate and sanitize user-provided URLs and parameters.
- Use allowlists to restrict requests to trusted sources.
- Implement network segmentation to protect internal systems.
- Apply strong access controls to internal resources.
- Log and monitor suspicious requests.
- Restrict the resources that the server is allowed to access.

**Key takeaway:** Instead of relying only on blocklists, using an allowlist approach is recommended when handling trusted URLs.

## Task 8 – Conclusion

Throughout this room, I learned the fundamentals of SSRF vulnerabilities and how they can be exploited in different scenarios.

I gained practical experience and knowledge in the following areas:

- Basic SSRF
- Localhost SSRF
- Internal Network SSRF
- Blind SSRF
- Out-of-Band SSRF
- Time-Based SSRF
- Denial of Service through SSRF
- SSRF mitigation techniques

This room helped me understand why server-side requests must be properly validated and controlled.

**Room Status: Completed**
