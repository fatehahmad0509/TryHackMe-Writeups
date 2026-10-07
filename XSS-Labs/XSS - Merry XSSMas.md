# XSS - Merry XSSMas

## Overview

This TryHackMe room focuses on two common types of Cross-Site Scripting (XSS):

* Reflected XSS
* Stored XSS

The objective was to identify both vulnerabilities in the web application and retrieve the corresponding flags.

---

## Task 2 — Reflected XSS

The application contains a search functionality where user input is reflected back onto the page.

I first tested the search functionality with a basic XSS payload:

```html
<script>alert(0)</script>
```

The application executed the JavaScript instead of displaying the input as plain text, confirming a **Reflected XSS** vulnerability.

The alert was triggered successfully, and the application revealed the following flag:

```text
THM{Evil_Bunny}
```

### Flag

```text
THM{Evil_Bunny}
```

---

## Stored XSS

The application also provides a **Send Message** functionality.

Unlike the search parameter, messages submitted through this form are stored by the application and later displayed to the user.

I tested the same payload:

```html
<script>alert(0)</script>
```

After submitting the message, the JavaScript executed successfully.

This confirmed that the application was vulnerable to **Stored XSS**.

The resulting flag was:

```text
THM{Evil_Stored_Egg}
```

### Flag

```text
THM{Evil_Stored_Egg}
```

---

## Questions

### Which type of XSS attack requires payloads to be persisted on the backend?

**Answer:**

```text
Stored XSS
```

### Reflected XSS Flag

```text
THM{Evil_Bunny}
```

### Stored XSS Flag

```text
THM{Evil_Stored_Egg}
```

---

## What I Learned

This lab helped reinforce the difference between Reflected and Stored XSS.

**Reflected XSS:**

```text
User Input → Request → Server Response → Browser → XSS
```

The payload is reflected in the response and is not permanently stored.

**Stored XSS:**

```text
User Input → Server → Database/Storage → Page → Browser → XSS
```

The payload is persisted and can execute when the affected content is viewed later.

The main takeaway was that identifying **where user input goes and whether it is persisted** is important when testing for XSS.

---

## AI Assistance

AI was used for clarification of XSS concepts and writeup organization. The lab interaction, payload testing, flag discovery, and final verification were performed by me.
