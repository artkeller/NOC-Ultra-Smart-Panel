# NOC Ultra Smart Panel v4.7.0

[![CRA Status](https://img.shields.io/badge/CRA-Exempt%20(pure%20OSS)-informational)](./CRA-EXEMPTION.md)
[![Language](https://img.shields.io/badge/language-🇬🇧%20English-informational)](./LANGUAGE.md)
[![License](https://img.shields.io/badge/License-Apache-blue?style=flat-square)](./LICENSE)
[![Version](https://img.shields.io/badge/Version-4.7.0-brightgreen?style=flat-square)](./CHANGELOG.md)
[![Security](https://img.shields.io/badge/Security-Policy-brightgreen?style=flat-square)](./SECURITY.md)

> **Light-weight Network Operations Center Dashboard**
> A fully serverless monitoring interface for real-time availability analysis.

---

## 🛰 Overview

The **NOC Ultra Smart Panel** is a high-performance tool designed for monitoring network endpoints. It provides instant visual feedback on the status of web services without the need for complex infrastructure like databases, agents, or backend servers.

![NOC Ultra Smart Panel v4.7.0](images/NOC_Ultra_Smart_Panel_v4_7_0.png "NOC_Ultra_Smart_Panel_v4_7_0")

### Key Capabilities

| Feature | Description |
| --- | --- |
| **Serverless Architecture** | 100% Client-side execution (HTML5/JS). |
| **Smart Sorting** | Prioritized status display (Red > Yellow > Blue > Green). |
| **IP-Grouping** | Logical sorting based on IP segments and protocol weights. |
| **Live Analytics** | Canvas-based latency graphs for every single monitor. |
| **Persistence** | Full configuration retention via `localStorage`. |

---

## 🛠 Technical Deep Dive & "Seminar"

This section explains the architectural decisions and how modern browser restrictions are bypassed.

### 1. The CORS Paradox & the `no-cors` Approach

In a standard web application, the **Same-Origin-Policy (SOP)** prevents JavaScript from making requests to foreign domains unless they explicitly agree via [**CORS (Cross-Origin Resource Sharing)**](./CORS/README.md "CORS/README.md").


**The Solution in the NOC Panel:**
The panel utilizes the `fetch` API in `mode: "no-cors"`.

* **Mechanism:** The browser sends the request but prevents the script from accessing the response body (opaque response).
* **Monitoring Impact:** Since we only need to know if the server responded and how long it took, the time elapsed between sending the request and receiving the opaque response is sufficient for our "Ping". Network errors or timeouts are still caught in the `catch` block.

### 2. Scheduler & Load Balancing

To prevent the browser from being overwhelmed by simultaneous requests (Network Congestion), the panel calculates a dynamic schedule:

```javascript
const step = intervalMs / monitors.length;
monitors.forEach((m, i) => { setTimeout(() => checkMonitor(m), i * step); });

```

This ensures that pings are evenly distributed across the interval (e.g., 5 seconds), resulting in extremely stable UI performance.

### 3. Logical Weighting (Sorting Engine)

The panel doesn't just sort by status; it uses a weighted logic for clarity in the "Green" state:

1. **Schema-Weight:** HTTPS is weighted higher than HTTP.
2. **Segment-Weight:** Localhost (127.x) before Intranet (192.168.x) before external domains.
3. **IP-Compare:** Real numerical sorting of IP addresses instead of simple string comparison.

---

## 📂 Project Structure

* `noc_ultra_v4_4_11.html` - The monolithic main module.
* `images/` - Assets and screenshots for documentation.

## 🚀 How to use

1. Save the `noc_ultra_v4_7_0.html` file locally or on a web space.
2. Open it in any modern web browser.
3. Add URLs in the format `https://domain.tld`.
4. **Favorites:** Use the ★ symbol to pin important endpoints to the top bar.

---

## ⚠️ Important Usage Notes

### Server Load & Rate-Limiting

This tool performs frequent HTTP requests. Please be aware of the following:

* **Rate-Limiting:** Some providers (e.g., Cloudflare at `1.1.1.1`) may interpret the high frequency of pings as a potential attack and will stop responding after approx. 30 seconds.
* **Resilient Targets:** Other services (e.g., Google DNS at `8.8.8.8`) are very tolerant and allow continuous monitoring even with a high number of monitors (30+).
* **Responsibility:** Use this tool responsibly. Setting very short intervals for a large number of monitors may result in your IP address being temporarily blacklisted by the target servers.

---

## ⚖️ License

Distributed under the **Apache License 2.0**. See `LICENSE` for more information.

---

**Thomas Walloschke** - [artkeller@gmx.de](mailto:artkeller@gmx.de)
Project Link: [https://artkeller.github.io/NOC-Ultra-Smart-Panel/](https://artkeller.github.io/NOC-Ultra-Smart-Panel/noc_ultra_v4_4_11.html)

