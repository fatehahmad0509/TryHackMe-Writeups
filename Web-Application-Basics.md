# TryHackMe — Web Application Basics

## Task 1: Introduction
This room introduces the fundamentals of web applications, including URLs, HTTP requests and responses, request methods, status codes, and security headers.

## Task 2: Web Application Overview

A web application consists of front-end and back-end components.

- **HTML:** Defines the structure and content of a webpage.
- **CSS:** Controls the appearance, colors, fonts, and layout.
- **JavaScript (JS):** Adds interactive behavior to the front end.
- **Web Server:** Hosts and delivers content for web applications.
- **Database:** Stores, modifies, and retrieves information.
- **Infrastructure:** Includes servers, storage, networking devices, and supporting software.
- **WAF (Web Application Firewall):** Filters potentially malicious requests to help protect web applications.

**Answers:**
- Web server
- Web browser
- Web Application Firewall (WAF)

## Task 3: Uniform Resource Locator (URL)

A URL identifies the location of an online resource.

Main URL components:
- **Scheme:** The protocol used, such as HTTP or HTTPS.
- **User:** Optional login information; including credentials in URLs can expose sensitive data.
- **Host/Domain:** Identifies the website being accessed.
- **Port:** Specifies the service's communication port. Common ports are 80 for HTTP and 443 for HTTPS.
- **Path:** Identifies a specific resource or endpoint.
- **Query String:** Passes additional parameters, starting with `?`.
- **Fragment:** Points to a specific section of a page, starting with `#`.

**Security note:** Typosquatting involves registering deceptive domain names that resemble legitimate websites, potentially tricking users into phishing attacks.

**Answers:**
- HTTPS
- Typosquatting
- Query string

## Task 4: HTTP Messages

HTTP enables communication between clients and web servers.

There are two main types:
- **HTTP Request:** Sent by the client to request a resource or trigger an action.
- **HTTP Response:** Returned by the server after processing a request.

Main message components:
1. **Start Line:** Identifies the request or response and provides essential information.
2. **Headers:** Contain metadata and instructions in key-value pairs.
3. **Empty Line:** Separates the headers from the message body.
4. **Body:** Contains the submitted data or returned content, when present.

**Answers:**
- HTTP response
- Empty line

## Task 5: HTTP Request — Request Line and Methods

An HTTP request line contains the method, URL path, and HTTP version.

Example:
`GET /api/users HTTP/1.1`

Common HTTP methods:
- **GET:** Retrieves data.
- **POST:** Sends data, often to create a resource or trigger an operation.
- **PUT:** Replaces a resource or its representation.
- **DELETE:** Requests resource deletion.
- **PATCH:** Partially updates a resource.
- **HEAD:** Similar to GET but returns headers without the response body.
- **OPTIONS:** Describes communication options for the target resource.
- **TRACE:** Used for diagnostic purposes and often disabled for security reasons.
- **CONNECT:** Establishes a tunnel, commonly used with HTTPS proxies.

HTTP versions:
- **HTTP/0.9:** Supported GET requests.
- **HTTP/1.0:** Introduced headers and improved content handling.
- **HTTP/1.1:** Introduced persistent connections and chunked transfer encoding.
- **HTTP/2:** Added multiplexing, header compression, and prioritization.
- **HTTP/3:** Uses QUIC for transport.

**Security note:** Requests must be properly validated, and authorization checks must be enforced before protected resources are modified or deleted.

**Answers:**
- HTTP/1.1
- OPTIONS
- URL path

## Task 6: HTTP Request — Headers and Body

Request headers provide information about the request and help the server process it.

Common headers:
- **Host:** Specifies the target hostname.
- **User-Agent:** Provides information about the client software.
- **Referer:** Indicates the URL from which the request originated, subject to browser policies.
- **Cookie:** Sends previously stored cookies to the server.
- **Content-Type:** Specifies the format of the request body.

Common request body formats:
- **URL-encoded:** Uses key-value pairs, such as `name=Fatih&country=AZ`.
- **Multipart/form-data:** Supports multiple data parts, including file uploads.
- **JSON:** Represents data using key-value pairs and structured objects.
- **XML:** Represents structured data using tags.

**Answers:**
- Host
- `application/x-www-form-urlencoded`
- Request headers

## Task 7: HTTP Response — Status Line and Status Codes

The HTTP response status line contains the HTTP version, status code, and reason phrase.

Status code categories:
- **100–199:** Informational responses.
- **200–299:** Successful responses.
- **300–399:** Redirection responses.
- **400–499:** Client error responses.
- **500–599:** Server error responses.

Common status codes:
- **100 Continue:** The server is ready for the rest of the request.
- **200 OK:** The request succeeded.
- **301 Moved Permanently:** The resource has permanently moved.
- **404 Not Found:** The requested resource could not be found.
- **500 Internal Server Error:** The server encountered an internal error.

**Answers:**
- Status line
- Server error responses
- 404

## Task 8: HTTP Response — Headers and Body

Response headers provide metadata and instructions to the client.

Important response headers:
- **Date:** Indicates when the response was generated.
- **Content-Type:** Specifies the response content format.
- **Server:** May reveal the web server software.
- **Set-Cookie:** Sends cookies to the client for storage.
- **Cache-Control:** Controls caching behavior.
- **Location:** Specifies a destination for redirection responses.

Cookie security flags:
- **Secure:** Restricts cookie transmission to HTTPS connections.
- **HttpOnly:** Prevents JavaScript from accessing the cookie.

The response body contains the actual returned content, such as HTML, JSON, or images.

**Answers:**
- Server
- Secure
- HttpOnly

## Task 9: Security Headers

Security headers provide additional protections against common web attacks.

### Content-Security-Policy (CSP)
Controls which sources are allowed to provide content, including scripts and stylesheets.

- `default-src`: Defines the default source policy.
- `script-src`: Defines permitted script sources.
- `style-src`: Defines permitted stylesheet sources.
- `'self'`: Refers to the same origin as the website.

### Strict-Transport-Security (HSTS)
Instructs browsers to use HTTPS for the website.

- `max-age`: Specifies how long the policy remains active.
- `includeSubDomains`: Extends the policy to subdomains.
- `preload`: Relates to inclusion in browser HSTS preload lists.

### X-Content-Type-Options
The `nosniff` directive prevents browsers from interpreting content as a different MIME type from the one declared.

### Referrer-Policy
Controls how much referrer information is sent with requests.

Examples:
- `no-referrer`: Sends no referrer information.
- `same-origin`: Sends referrer information for same-origin requests.
- `strict-origin`: Sends origin information under specified secure-protocol conditions.
- `strict-origin-when-cross-origin`: Sends more information for same-origin requests and limits it for cross-origin requests.

**Answers:**
- `script-src`
- `includeSubDomains`
- `nosniff`

## Task 10: Practical Task — Making HTTP Requests

This task involved sending HTTP requests to API endpoints and observing the results.

### 1. GET request
Endpoint: `/api/users`

Purpose: Retrieve the user list.

Flag:
`THM{YOU_HAVE_JUST_FOUND_THE_USER_LIST}`

### 2. POST request
Endpoint: `/api/user/2`

Purpose: Update Bob's country from UK to US.

Flag:
`THM{YOU_HAVE_MODIFIED_THE_USER_DATA}`

### 3. DELETE request
Endpoint: `/api/user/1`

Purpose: Delete the specified user.

Flag:
`THM{YOU_HAVE_JUST_DELETED_A_USER}`

## Task 11: Conclusion

Completed the Web Application Basics room.

Key topics learned:
- Web application architecture and its components.
- URL structure and its individual components.
- HTTP requests, responses, headers, and body formats.
- HTTP methods and response status codes.
- Security headers and cookie security flags.
- Practical interaction with API endpoints using GET, POST, and DELETE.

These fundamentals provide a foundation for studying web application security and understanding how HTTP communication works during security testing.
