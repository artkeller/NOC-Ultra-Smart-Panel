# Favorites

## Profiles

- vorausgewählt
- verifiziert (qualitätsgesichert)

### Default... (10)

- autoload
- funktionieren immer im Browser
- repräsentieren "das Internet"

### Groups

- selected load
- funktionieren immer im Browser
- repräsentieren bestimmte Typen (CDN, Provider, ...)


#### Root-Server

- A-Server,
- ...

#### CDN

#### Hyperscaler

- Microsoft
- Amazon
- Google
- Cloudflare

#### Content-Provider

- Hetzer
- GMX

#### Standardisierer

- ICANN
- W3C
- ISO
- IEC
- CEN
- CENELEC
- DIN
- ETSI
- IEEE

### Global Sentinel Set

- aus allen o.g. die stärkesten 3..5 (Summe)

---

⚙️ ✅ Scoring- & Quorum-System (JS/TS)

Jetzt der wirklich wichtige Teil.

🧩 Grundidee

Wir messen:

infraScore
continentScore
countryScore

💻 Beispiel-Implementierung (TypeScript)

```js
type CheckResult = {
  url: string;
  ok: boolean;
  latency: number;
};

type Profile = {
  infra: string[];
  continents: string[];
  countries: string[];
};

type ScoreResult = {
  infra: number;
  continents: number;
  countries: number;
  internetAlive: boolean;
};

const TIMEOUT = 3000;

async function checkUrl(url: string): Promise<CheckResult> {
  const controller = new AbortController();
  const timeout = setTimeout(() => controller.abort(), TIMEOUT);

  const start = Date.now();

  try {
    await fetch(url, {
      method: "HEAD",
      mode: "no-cors",
      signal: controller.signal,
    });

    clearTimeout(timeout);

    return {
      url,
      ok: true,
      latency: Date.now() - start,
    };
  } catch {
    return {
      url,
      ok: false,
      latency: TIMEOUT,
    };
  }
}

async function checkGroup(urls: string[]): Promise<number> {
  const results = await Promise.all(urls.map(checkUrl));
  return results.filter(r => r.ok).length;
}

export async function evaluate(profile: Profile): Promise<ScoreResult> {
  const [infraOk, continentOk, countryOk] = await Promise.all([
    checkGroup(profile.infra),
    checkGroup(profile.continents),
    checkGroup(profile.countries),
  ]);
```

  // 🔥 Quorum-Logik

```js
  const internetAlive =
    infraOk >= 2 &&
    continentOk >= 4 &&
    countryOk >= 15;

  return {
    infra: infraOk,
    continents: continentOk,
    countries: countryOk,
    internetAlive,
  };
}
```

🔥 Empfohlene Quorum-Regeln

```js
infra >= 2        // DNS/CDN lebt
continents >= 4   // global verteilt erreichbar
countries >= 15   // breite Streuung
```
⚡ Optional (sehr empfehlenswert)
Gewichtung statt harter Grenzen

```js
const score =
  infraOk * 3 +
  continentOk * 2 +
  countryOk * 1;

const internetAlive = score >= 25;
```

🚀 Bonus: Performance-Optimierung

Nicht alles auf einmal:

```js
// staggered execution
await Promise.all([
  checkGroup(profile.infra),
  delay(200).then(() => checkGroup(profile.continents)),
  delay(400).then(() => checkGroup(profile.countries)),
]);
```

---

Perfekt – jetzt bauen wir daraus ein **wirklich robustes Monitoring-System**.
Ich gebe dir eine **kompakte, produktionsnahe TS-Implementierung**, die drei Dinge sauber integriert:

* 🌐 Geo-Gewichtung (Kontinente > Länder > Infra feinjustiert)
* 🔁 Adaptive Retry (smart statt blind)
* 📊 Ampel-Logik (grün/gelb/rot)

---

# 🌐 1. Architektur (kurz & klar)

Wir erweitern dein Modell:

```ts
type Node = {
  url: string;
  region: string;     // z.B. "EU", "NA", "AS"
  type: "infra" | "continent" | "country";
};
```

---

# ⚙️ 2. Adaptive Check (Retry + Timing)

👉 Idee:

* schneller erster Versuch
* bei Fehler: Retry mit Backoff
* langsame Antworten werden „abgewertet“

```ts
const BASE_TIMEOUT = 2500;

async function fetchWithRetry(url: string, retries = 2): Promise<{ ok: boolean; latency: number }> {
  let attempt = 0;
  let delay = 300;

  while (attempt <= retries) {
    const controller = new AbortController();
    const timeout = setTimeout(() => controller.abort(), BASE_TIMEOUT + attempt * 1000);

    const start = Date.now();

    try {
      await fetch(url, {
        method: "HEAD",
        mode: "no-cors",
        signal: controller.signal,
      });

      clearTimeout(timeout);

      return {
        ok: true,
        latency: Date.now() - start,
      };
    } catch {
      attempt++;
      if (attempt > retries) break;

      await new Promise(r => setTimeout(r, delay));
      delay *= 2; // exponential backoff
    }
  }

  return { ok: false, latency: BASE_TIMEOUT };
}
```

---

# 🌐 3. Geo-Gewichtung

👉 Ziel:

* Kontinente zählen stärker als einzelne Länder
* Infra bleibt kritisch, aber nicht dominant

```ts
const WEIGHTS = {
  infra: 3,
  continent: 5,   // 🔥 bewusst höher!
  country: 1,
};
```

---

## 🌍 Regionale Diversität (Bonus)

👉 Verhindert „alles nur Europa“

```ts
function uniqueRegions(results: { node: Node; ok: boolean }[]) {
  const set = new Set<string>();
  results.forEach(r => {
    if (r.ok) set.add(r.node.region);
  });
  return set.size;
}
```

---

# ⚙️ 4. Scoring Engine

```ts
type Result = {
  node: Node;
  ok: boolean;
  latency: number;
};

async function evaluateNodes(nodes: Node[]) {
  const results: Result[] = await Promise.all(
    nodes.map(async node => {
      const res = await fetchWithRetry(node.url);
      return { node, ...res };
    })
  );

  let score = 0;

  for (const r of results) {
    if (!r.ok) continue;

    const base = WEIGHTS[r.node.type];

    // 🔥 Latenz beeinflusst Score
    const latencyFactor =
      r.latency < 500 ? 1.0 :
      r.latency < 1500 ? 0.7 :
      0.4;

    score += base * latencyFactor;
  }

  const regionCount = uniqueRegions(results);

  return {
    score,
    regionCount,
    results,
  };
}
```

---

# 📊 5. Ampel-Logik (entscheidend)

👉 Kombination aus:

* Score
* globale Verteilung

```ts
function getStatus(score: number, regions: number): "green" | "yellow" | "red" {
  if (score >= 60 && regions >= 4) return "green";
  if (score >= 30 && regions >= 3) return "yellow";
  return "red";
}
```

---

# 🚀 6. Gesamtfunktion

```ts
export async function checkInternet(nodes: Node[]) {
  const { score, regionCount, results } = await evaluateNodes(nodes);

  const status = getStatus(score, regionCount);

  return {
    status,          // 🔴 🟡 🟢
    score,
    regions: regionCount,
    details: results,
  };
}
```

---

# 📊 7. Dashboard-Logik

👉 Beispiel für UI-State:

```ts
function statusLabel(status: string) {
  switch (status) {
    case "green":
      return "Internet OK";
    case "yellow":
      return "Instabil / eingeschränkt";
    case "red":
      return "Internet gestört";
  }
}
```

---

# 🔥 Optional: Visuelle Ampel

```ts
function statusColor(status: string) {
  return {
    green: "#00c853",
    yellow: "#ffd600",
    red: "#d50000",
  }[status];
}
```

---

# 🧠 Warum das jetzt „richtig gut“ ist

### ✅ Robust gegen:

* einzelne Ausfälle
* regionale Zensur
* CDN-Probleme

### ✅ Erkennt:

* „Internet lebt, aber nur lokal“ → 🟡
* „global verfügbar“ → 🟢
* „wirklich down“ → 🔴

---

# 🔥 TL;DR

Du hast jetzt:

### 🌐 Geo-gewichtetes Modell

→ Kontinente dominieren

### 🔁 Adaptive Retries

→ weniger False Negatives

### 📊 Ampel-System

→ sofort verständlich

---



