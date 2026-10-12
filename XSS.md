# XSS

## Overview

This TryHackMe room explores Cross-Site Scripting (XSS), a web application vulnerability that allows attackers to inject malicious scripts into web pages viewed by other users. These scripts execute in the victim's browser under the origin of the vulnerable website.

**Topics Covered:**
- Reflected XSS
- Stored XSS
- DOM-based XSS
- Input validation and sanitization
- Output encoding
- HTML and JavaScript contexts
- XSS filter evasion
- Security implications and remediation

**Room Name:** XSS  
**Room Status:** Completed

---

## 1. Introduction to XSS

Cross-Site Scripting occurs when an application handles user-controlled input insecurely, allowing injected JavaScript to execute in a user's browser.

XSS can bypass the protection normally provided by the Same-Origin Policy (SOP) because the injected script executes in the context of the vulnerable website's origin.

### Types of XSS

- **Reflected XSS:** User input is immediately included in the server's response without proper encoding.
- **Stored XSS:** Malicious input is saved by the application and executed when another user views the affected content.
- **DOM-based XSS:** Client-side JavaScript insecurely processes user-controlled data and modifies the DOM.

### Questions

- Which XSS vulnerability relies on saving the malicious script? `Stored XSS`
- Which XSS vulnerability executes without being stored? `Reflected XSS`
- What does DOM stand for? `Document Object Model`

---

## 2. Causes and Implications of XSS

### Common Causes

- Insufficient input validation and sanitization
- Missing or incorrect output encoding
- Improperly configured security headers, including Content Security Policy (CSP)
- Vulnerable frameworks and third-party libraries

### Potential Impact

Depending on the application's functionality and the victim's privileges, XSS may enable session compromise, phishing, unauthorized actions, page manipulation, or exposure of sensitive information.

### Prevention

- Validate and sanitize input where appropriate.
- Encode output according to its context.
- Avoid inserting untrusted data into dangerous HTML or JavaScript sinks.
- Use a restrictive Content Security Policy as an additional security layer.
- Keep frameworks and third-party dependencies updated.

### Questions

- What operations should be performed on user input? `Validation and sanitization`
- What operation should be performed before displaying data to users? `Encoding`

---

## 3. Reflected XSS

Reflected XSS occurs when user-controlled input, such as a search query, is included in a server response without appropriate output encoding.

For example, an application may retrieve a query parameter and insert it directly into an HTML response. If the browser interprets the input as executable markup or JavaScript, the vulnerability can be exploited.

### Vulnerable Code Example

```php
$search_query = $_GET['q'];
echo "<p>You searched for: $search_query</p>";
```

The application inserts the query parameter directly into the HTML response.

### Remediation

```php
$search_query = $_GET['q'];
$escaped_search_query = htmlspecialchars($search_query);
echo "<p>You searched for: $escaped_search_query</p>";
```

The `htmlspecialchars()` function converts special HTML characters into entities, preventing the browser from interpreting them as HTML syntax in this context.

Similar vulnerabilities can occur in Node.js, Flask, and ASP.NET applications when untrusted input is inserted into HTML without suitable encoding.

### Questions

- Which character should be HTML-encoded? `&`
- Which character should be HTML-encoded? `<`
- Which JavaScript library function was used for sanitization in the lesson? `sanitizeHtml()`
- Which PHP function was used to encode HTML special characters? `htmlspecialchars()`

---

## 4. Practical Lab – Reflected XSS

The room provided a vulnerable version of **copyparty**, associated with CVE-2023-38501.

The vulnerable service was available on port `3923` in the assigned lab environment.

### Provided Exploit

The room supplied a URL-encoded proof of concept that decodes to:

```html
?k304=y

<img src=copyparty onerror=alert(1)>
```

The injected image references an invalid resource. When loading fails, the `onerror` event handler executes JavaScript.

### Results

- **Vulnerability Type:** Reflected XSS
- **Answer on the second line after `go to`:** `/?h#cc`

### Key Takeaway

Reflected XSS can occur when user-controlled input is reflected into a response in a context where the browser interprets it as active HTML.

---

## 5. Stored XSS

Stored XSS, also known as persistent XSS, occurs when user-controlled content is saved and later displayed to other users without appropriate output encoding or sanitization.

Common locations include comments, contact forms, reviews, and profile fields.

### Vulnerable Code Example

```javascript
app.get('/comments', (req, res) => {
  let html = '<ul>';

  for (const comment of comments) {
    html += `<li>${comment}</li>`;
  }

  html += '</ul>';
  res.send(html);
});
```

The application inserts stored comments directly into an HTML response.

### Remediation

```javascript
const sanitizeHtml = require('sanitize-html');

app.get('/comments', (req, res) => {
  let html = '<ul>';

  for (const comment of comments) {
    const sanitizedComment = sanitizeHtml(comment);
    html += `<li>${sanitizedComment}</li>`;
  }

  html += '</ul>';
  res.send(html);
});
```

Sanitization can remove HTML elements and attributes that are not permitted by the application's policy. For plain-text output, context-appropriate output encoding is generally preferable.

### Questions

- Which JavaScript function was used in the lesson to sanitize input? `sanitizeHtml()`
- Which ASP.NET method was used to encode HTML output? `HttpUtility.HtmlEncode()`

---

## 6. Practical Lab – Stored XSS

The second practical machine used a vulnerable **Hospital Management System** affected by CVE-2021-38757.

The application contained a contact form that stored submitted messages in a database. Those messages were subsequently displayed to the receptionist.

### Vulnerable Component

The `contact.php` file saved the submitted message without appropriate protection:

```php
$message = $_POST['txtMsg'];

$query = "insert into contact(name,email,contact,message)
          values('$name','$email','$contact','$message');";
```

The stored content could later be rendered in the receptionist's interface.

### Lab Results

- **Vulnerability Type:** Stored XSS
- **Key displayed in the key-value pair:** `PHPSESSID`

### Key Takeaway

Stored XSS can affect a different user from the person who submitted the original input. All user-generated content must be handled securely when it is rendered.

Output encoding protects against XSS, while parameterized SQL queries address the separate SQL injection risk in database operations.

---

## 7. DOM-Based XSS

DOM-based XSS occurs when client-side JavaScript processes untrusted data and passes it to an unsafe DOM operation.

Unlike reflected and stored XSS, the vulnerable data flow can occur entirely in the browser without requiring the server to reflect the input.

### Vulnerable Example

```html
<script>
const name = new URLSearchParams(window.location.search).get('name');
document.write("Hello, " + name);
</script>
```

The `name` parameter is inserted directly into the document using `document.write()`. If the input contains executable markup, the browser may interpret it as HTML.

### Safer Approach

```javascript
const name = new URLSearchParams(window.location.search).get('name');

document.getElementById("greeting").textContent = "Hello, " + name;
```

Using `textContent` displays the value as text instead of interpreting it as HTML.

**Important:** `encodeURIComponent()` is designed for encoding URI components; it is not a universal substitute for context-specific HTML output encoding. When inserting plain text into the DOM, prefer safe text APIs such as `textContent`.

### Questions

- Is DOM-based XSS reflected through the server? `Nay`
- Does DOM-based XSS occur on the client side? `Yea`
- Which method was used in the lesson to encode the user input? `encodeURIComponent()`

---

## 8. XSS Contexts and Filter Evasion

The context in which user input is inserted determines how the browser interprets it.

Common contexts include:

- Between HTML tags
- Inside HTML attributes
- Within JavaScript code

A payload that works in one context may fail in another because HTML parsing rules and JavaScript syntax differ.

Applications may also use filters or a Web Application Firewall (WAF) to block suspicious input. Such filters are not a substitute for secure output handling.

### Encoding Observation

The room demonstrated how character references can represent whitespace characters in HTML.

- `&#x09;` represents a horizontal tab.
- `&#x0A;` represents a line feed.
- `&#x0D;` represents a carriage return.

These examples demonstrate why simple blocklists may be insufficient: browsers can interpret different representations of input in unexpected ways.

### Question

- Which character does `&#x09;` represent? `TAB`

---

## 9. Final Question

**Question:** Which XSS type was demonstrated using a fictional static website?

**Answer:** `DOM-based XSS`

The example showed that a client-side script can introduce an XSS vulnerability by inserting user-controlled data into the document unsafely.

---

## 10. Key Takeaways

After completing this room, I improved my understanding of:

1. The differences between reflected, stored, and DOM-based XSS.
2. How unsafe handling of user-controlled input can lead to JavaScript execution.
3. Why input validation alone is not sufficient to prevent XSS.
4. The importance of context-appropriate output encoding and safe DOM APIs.
5. How stored XSS can affect other users who view saved content.
6. Why understanding the HTML and JavaScript context is essential when analyzing an XSS vulnerability.
7. Why security headers and WAFs should complement, rather than replace, secure application code.

### Conclusion

This room helped me understand XSS beyond simply testing payloads. Identifying the source of untrusted data, tracing how it reaches a browser sink, understanding the relevant parsing context, and recognizing the appropriate mitigation are all essential skills for web application security testing.

**Room Status:** Completed
