# NOC Ultra Smart Panel v4.7.5.0

[![CRA Status](https://img.shields.io/badge/CRA-Exempt%20(pure%20OSS)-informational)](./CRA-EXEMPTION.md)
[![Language](https://img.shields.io/badge/Sprache-🇩🇪%20Deutsch-informational)](./LANGUAGE-DE.md)
[![License](https://img.shields.io/badge/License-Apache-blue?style=flat-square)](./LICENSE)
[![Version](https://img.shields.io/badge/Version-4.7.5.0-brightgreen?style=flat-square)](./CHANGELOG.md)
[![Security](https://img.shields.io/badge/Security-Policy-brightgreen?style=flat-square)](./SECURITY.md)

> **Light-weight Network Operations Center Dashboard**
> Ein vollständig serverloses Monitoring-Interface für Echtzeit-Verfügbarkeitsanalysen.

---

## 🛰 Overview

Das **NOC Ultra Smart Panel** ist ein hochperformantes Tool zur Überwachung von Netzwerk-Endpunkten. Es wurde entwickelt, um ohne komplexe Infrastruktur (Datenbanken, Agenten oder Backend-Server) eine sofortige visuelle Rückmeldung über den Status von Web-Services zu liefern.

### Key Capabilities

| Feature | Description |
| --- | --- |
| **Serverless Architecture** | 100% Client-side Execution (HTML5/JS). |
| **Smart Sorting** | Priorisierte Status-Anzeige (Red > Yellow > Blue > Green). |
| **IP-Grouping** | Logische Sortierung nach IP-Segmenten und Protokoll-Weights. |
| **Live Analytics** | Canvas-basierte Latenzgraphen für jeden Monitor. |
| **Persistence** | Volle Konfigurationserhaltung via `localStorage`. |

![NOC Ultra Smart Panel v4.4.11](images/NOC_Ultra_Smart_Panel_v4_4_11.png "NOC_Ultra_Smart_Panel_v4_4_11")

---

## 🛠 Technical Deep Dive & "Seminar"

In diesem Abschnitt werden die architektonischen Entscheidungen und die Bewältigung technischer Restriktionen moderner Browser erläutert.

### 1. Das CORS-Paradoxon & der `no-cors` Ansatz

In einer klassischen Web-Applikation verhindert die **Same-Origin-Policy (SOP)**, dass JavaScript Anfragen an fremde Domains stellt, sofern diese nicht explizit via  [**CORS (Cross-Origin Resource Sharing)**](./CORS/README-DE.md "CORS/README-DE.md") zugestimmt haben.

**Die Lösung im NOC Panel:**
Das Panel nutzt die `fetch`-API im Modus `mode: "no-cors"`.

* **Mechanismus:** Hierbei sendet der Browser die Anfrage ab, erlaubt dem Script aber keinen Zugriff auf den Inhalt der Antwort (Response Body).
* **Effekt für das Monitoring:** Da wir für einen "Ping" nur wissen müssen, ob der Server geantwortet hat und wie lange dies dauerte, reicht uns der zeitliche Verlauf vom Absenden bis zum Eintreffen der (opaken) Antwort. Ein Time-out oder ein Netzwerkfehler wird dennoch im `catch`-Block gefangen.

### 2. Scheduler & Lastverteilung

Um den Browser nicht durch zeitgleiche Anfragen zu blockieren (Network Congestion), berechnet das Panel einen dynamischen Zeitplan:

```javascript
const step = intervalMs / monitors.length;
monitors.forEach((m, i) => { setTimeout(() => checkMonitor(m), i * step); });

```

Dadurch werden die Pings gleichmäßig über das Intervall (z.B. 5 Sekunden) verteilt, was zu einer extrem stabilen UI-Performance führt.

### 3. Logische Gewichtung (Sorting Engine)

Das Panel sortiert nicht nur nach Status, sondern nutzt eine gewichtete Logik für die Übersichtlichkeit im "Green"-State:

1. **Schema-Weight:** HTTPS wird höher gewichtet als HTTP.
2. **Segment-Weight:** Localhost (127.x) vor Intranet (192.168.x) vor externen Domains.
3. **IP-Compare:** Echte numerische Sortierung von IP-Adressen statt einfacher String-Vergleich.

---

## 📂 Project Structure

* `noc_ultra_v4_4_11.html` - Das monolithische Hauptmodul.
* `images/` - Assets und Screenshots für die Dokumentation.

## 🚀 How to use

1. Datei `noc_ultra_v4_4_11.html` lokal oder auf einem Webspace speichern.
2. Im Browser öffnen.
3. URLs im Format `https://domain.tld` hinzufügen.
4. **Favoriten:** Mit dem ★-Symbol können wichtige Endpunkte dauerhaft in der oberen Leiste fixiert werden.

---

## ⚠️ Wichtige Anwendungshinweise

### Serverlast & Rate-Limiting

Dieses Tool führt regelmäßige HTTP-Abfragen durch. Bitte beachte:

* **Rate-Limiting:** Einige Anbieter (z.B. Cloudflare unter `1.1.1.1`) erkennen die hohe Frequenz der Pings als potenziellen Angriff und verweigern nach ca. 30 Sekunden die Antwort.
* **Resistente Ziele:** Andere Dienste (z.B. Google DNS unter `8.8.8.8`) sind sehr tolerant und erlauben auch bei einer hohen Anzahl von Monitoren (30+) dauerhafte Abfragen.
* **Verantwortung:** Nutze das Tool verantwortungsbewusst. Zu kurze Intervalle bei zu vielen Monitoren können dazu führen, dass deine IP-Adresse von Zielservern temporär gesperrt wird.

---

## ⚖️ License

Distributed under the **Apache License 2.0**. See `LICENSE` for more information.

---

**Thomas Walloschke** - [artkeller@gmx.de](mailto:artkeller@gmx.de)
Project Link: [https://artkeller.github.io/NOC-Ultra-Smart-Panel/](https://artkeller.github.io/NOC-Ultra-Smart-Panel/noc_ultra_v4_4_11.html)





