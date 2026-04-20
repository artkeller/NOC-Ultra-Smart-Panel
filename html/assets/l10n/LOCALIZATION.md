# LOCALIZATION (l10n)

Nachfolgend die Namen von 28 Sprachdateien (JSON-Format), die auf dem Server im gleichen Verzeichnis wie die HTML-Datei liegen müssen. 

## Dateinamen 

 Version 4.7.5.0/2   -> Version 4.7.5.3 (l10n-schema)
- language_de.json -> noc_ultra_l10n_de.json,
- language_en.json -> noc_ultra_l10n_en.json,
- language_bg.json -> noc_ultra_l10n_bg.json,
- language_cs.json -> noc_ultra_l10n_cs.json,
- language_da.json -> noc_ultra_l10n_da.json,
- language_el.json -> noc_ultra_l10n_el.json,
- language_es.json -> noc_ultra_l10n_es.json,
- language_et.json -> noc_ultra_l10n_et.json,
- language_fi.json -> noc_ultra_l10n_fi.json,
- language_fr.json -> noc_ultra_l10n_fr.json,
- (ga fehlt)       -> noc_ultra_l10n_ga.json,
- language_hr.json -> noc_ultra_l10n_hr.json,
- language_hu.json -> noc_ultra_l10n_hu.json,
- language_it.json -> noc_ultra_l10n_it.json,
- language_lt.json -> noc_ultra_l10n_lt.json,
- language_lv.json -> noc_ultra_l10n_lv.json,
- language_mt.json -> noc_ultra_l10n_mt.json,
- language_nl.json -> noc_ultra_l10n_nl.json,
- language_pl.json -> noc_ultra_l10n_pl.json,
- language_pt.json -> noc_ultra_l10n_pt.json,
- language_ro.json -> noc_ultra_l10n_ro.json,
- language_sk.json -> noc_ultra_l10n_sk.json,
- language_sl.json -> noc_ultra_l10n_sl.json,
- language_sv.json -> noc_ultra_l10n_sv.json,
- language_ru.json -> noc_ultra_l10n_ru.json,
- language_ja.json -> noc_ultra_l10n_ja.json,
- language_zh.json -> noc_ultra_l10n_zh.json,
- language_ko.json -> noc_ultra_l10n_ko.json.
  
## Inhalte

Jede Datei enthält die exakt gleichen Schlüssel wie das defaultTranslations-Objekt plus die zusätzlichen Tooltip‑Schlüssel. 

Die Übersetzungen sind vollständig und entsprechen den in der vorherigen Version eingepflegten multilingualen Texten. 

Die Dateien sind direkt verwendbar.

## Struktur (v4.7.5.3)

```javascript

  // ========== Eingebettete Default-Sprache (Englisch) – Fallback ==========
  const defaultTranslations = {
    langCode: "en",
    langNameNative: "English",
    langNameEnglish: "English",
    langAuto: "Auto (Browser)",
    add: "Add Monitor",
    favs: "Favorites",
    monitors: "Monitors",
    placeholder: "Enter URL...",
    err_url: "invalid",
    err_net: "Network Error",
    err_empty: "URL empty",
    err_offline: "no network",
    online: "OK",
    loading: "init",
    start: "Start",
    warn_http: "⚠️ Insecure HTTP URL – click again to add it anyway.",
    menuTitle: "Menu",
    menuLang: "🌐 Language",
    menuMode: "🎛️ Mode",
    menuSettings: "⚙️ Settings",
    menuVersion: "📌 Version",
    close: "Close",
    footerImpressum: "Imprint",
    footerDatenschutz: "Privacy",
    footerNutzung: "Terms",
    footerCookies: "Cookies",
    autoLabel: "Auto-Fav",
    autoFavTitle: "Auto-Favorite: Valid URLs are automatically added to favorites",
    favMenuDefault: "↺ Load default",
    favMenuSave: "💾 Save",
    favMenuLoad: "📂 Load",
    favMenuDelete: "🗑️ Delete all",
    monMenuSave: "💾 Save monitors",
    monMenuLoadAdd: "📂 Add monitors",
    monMenuLoadSub: "📂 Substitute monitors",
    monMenuDelete: "🗑️ Delete all monitors",
    monDeleteConfirm: "Delete all monitors?",
    tooltipMenu: "Menu",
    tooltipFavMenu: "Favorites menu",
    tooltipMonitorMenu: "Monitors menu"
  };


```
