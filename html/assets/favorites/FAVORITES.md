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


