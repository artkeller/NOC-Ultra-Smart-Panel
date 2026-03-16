# CORS (Cross-Origin Resource Sharing) explained

[![Language](https://img.shields.io/badge/language-🇬🇧%20English-informational)](/LANGUAGE.md)

## 1. The core concept: Why does CORS exist?

- By default, **browsers** (!) enforce the **Same-Origin Policy (SOP)**.

- This prevents **scripts** from **website A** (e.g., `evil-site.com`) 
from retrieving data from **website B** (e.g., `my-bank-account.com`) without permission 
while you are logged in there.

- **CORS** is the controlled **exception** to this **rule**. 

- It allows a **server** to explicitly say, “I trust requests 
from `domain-a.com` and allow access to my data.” 

## 2. What is an “origin”?

Two URLs have the same **origin** if these three characteristics are identical: 

    Protocol (e.g., http vs. https)
    Domain (e.g., example.com vs. api.example.com)
    Port (e.g., :80 vs. :443)

## 3. How does the process work?

When your **browser** makes a request to another **origin** 
(e.g., via `fetch()` or `XMLHttpRequest`), the following process takes place: 

1. **The request:** The **browser** automatically adds the header 
`Origin: https://your-site.com`.
2. **The check (preflight):** For “complex” requests (e.g., with 
`PUT`, `DELETE`, or special JSON headers), the **browser** sends an 
`OPTIONS` request in advance. It asks the server, “Am I allowed to make this request 
using these methods?”
3. **The response:** The server responds with **CORS** headers. 
The most important one is: `Access-Control-Allow-Origin: https://your-site.com` (or * for all).
4. **The decision:** If the server's header matches the **origin**
of the website, the browser allows access. If not, 
the **browser** blocks the response and displays a **CORS** error 
in the console. 

## 4. Important facts for developers

- **Browser issue:** **CORS** is enforced by the **browser**, not the **server**. 
Tools such as `Postman` or `curl` completely ignore **CORS** rules.

- **No substitute for authentication:** **CORS** protects users from third-party 
sites acting on their behalf. However, it does not replace **API keys** or **logins** on the server.

- **Wildcards:** `Access-Control-Allow-Origin: *` allows every site to access 
the data. This is okay for public APIs (e.g., Google Fonts), but dangerous for private data.
 
