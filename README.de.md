# Play Store Search API

Eine Google-Play-Such-API von [FAK LABS](https://faizankhichi.me/). Apps auf Google Play suchen — mit vollständigen App-Details, Filtern und Sortierung. Kein API-Key nötig.

> **Dieses Repo ist nur Dokumentation.** Es beschreibt den öffentlichen Endpoint und seine Verwendung. Es enthält keinen Code.

## In Ihrer Sprache lesen

- [🇬🇧 English](README.md) · [🇧🇷 Português](README.pt.md) · [🇸🇦 العربية (Arabic)](README.ar.md) · [🇫🇷 Français](README.fr.md) · [🇪🇸 Español](README.es.md) · [🇵🇰 اردو (Urdu)](README.ur.md) · [🇹🇿 Kiswahili](README.sw.md) · [🇩🇪 Deutsch](README.de.md) · [🇮🇩 Bahasa Indonesia](README.id.md) · [🇮🇳 हिन्दी (Hindi)](README.hi.md) · [🇦🇫 پښتو (Pashto)](README.ps.md) · [🇮🇷 فارسی (Farsi)](README.fa.md) · [🇨🇳 简体中文 (Chinese)](README.zh.md)

## Endpoint

```
GET https://play-api.faizankhichi.me/?q=<query>
```

Jede Anfrage liefert sauberes JSON. Der einzige Pflichtparameter ist `q`.

## Schnellstart

```bash
curl "https://play-api.faizankhichi.me/?q=whatsapp"
```

```js
const res = await fetch("https://play-api.faizankhichi.me/?q=whatsapp&maxResults=5");
const data = await res.json();
data.results.forEach(a => console.log(a.name, a.package, a.score, a.installs));
```

## Parameter

| Parameter | Werte | Standard | Hinweise |
|---|---|---|---|
| `q` | Suchtext (max. 200 Zeichen) | — | **Pflicht.** |
| `category` | Play-Store-Genre-ID, z. B. `communication`, `game_arcade`, `education`, `tools`, `photography`, `game_puzzle` | — | Braucht `details=full` (Standard). Groß-/Kleinschreibung egal; auch der Anzeigename funktioniert (`Communication`). |
| `price` | `free`, `paid`, `all` | `all` | |
| `sort` | `relevance`, `rating`, `installs`, `updated` | `relevance` | `updated` braucht `details=full` (Standard). |
| `hl` | Sprachcode, z. B. `en`, `ur`, `es` | `en` | Oberflächensprache der Ergebnisse. |
| `gl` | Regionscode, z. B. `US`, `PK`, `ES` | `US` | Region, für die die Ergebnisse lokalisiert sind. |
| `maxResults` | 1–30 | `20` | Die Play-Suchseite listet höchstens ~30 Apps pro Anfrage, daher sind die Ergebnisse auf 30 begrenzt. |
| `details` | `full`, `basic` | `full` | `full` lädt die Detailseite jeder App (vollständige Daten). `basic` ist schneller, aber dünner — siehe unten. |

Filter lassen sich frei kombinieren — alle Parameter funktionieren zusammen:

```bash
# kostenpflichtige Arcade-Spiele, bestbewertete zuerst
curl "https://play-api.faizankhichi.me/?q=minecraft&price=paid&category=game_arcade&sort=rating"

# kostenlose Kommunikations-Apps, auf Urdu, für Pakistan
curl "https://play-api.faizankhichi.me/?q=chat&price=free&category=communication&hl=ur&gl=PK"

# meistinstallierte Foto-Editoren, zuletzt aktualisierte zuerst
curl "https://play-api.faizankhichi.me/?q=photo+editor&sort=installs"
```

## Eine einzelne App

Eine App über ihren Paketnamen abfragen, mit vollständigen Details — inklusive ihres Beta-Beitrittslinks:

```
GET https://play-api.faizankhichi.me/app?id=<package>
```

```bash
curl "https://play-api.faizankhichi.me/app?id=com.whatsapp"
curl "https://play-api.faizankhichi.me/app?id=com.mojang.minecraftpe"
```

Die Antwort ist `{ "success": true, "app": { … } }` — das `app`-Objekt hat genau die unten aufgeführten Felder. Unbekanntes Paket → `404`.

## Antwort

Felder der obersten Ebene:

| Feld | Typ | Beschreibung |
|---|---|---|
| `success` | boolean | `true`, wenn die Anfrage funktioniert hat. |
| `query` | string | Die Anfrage wie empfangen. |
| `count` | number | Anzahl der Ergebnisse in dieser Antwort. |
| `source` | string | `"play-html"` — die Ergebnisse stammen von Google Plays eigenen Webseiten. |
| `filters` | object | Die angewendeten Filter, zurückgegeben: `category`, `price`, `sort`, `hl`, `gl`, `maxResults`, `details`. |
| `results` | array | Die Ergebnisse (siehe unten). |

### Beispielantwort

`GET https://play-api.faizankhichi.me/?q=whatsapp&maxResults=1`

```json
{
  "success": true,
  "query": "whatsapp",
  "count": 1,
  "source": "play-html",
  "filters": {
    "price": "all",
    "sort": "relevance",
    "hl": "en",
    "gl": "US",
    "maxResults": 1,
    "details": "full"
  },
  "results": [
    {
      "package": "com.whatsapp",
      "name": "WhatsApp",
      "url": "https://play.google.com/store/apps/details?id=com.whatsapp&hl=en&gl=US",
      "icon": "https://play-lh.googleusercontent.com/Gqxk4T0uZsDwFp07DE-508hkyvcNmgFuRwPiwTEfF7D7OzGv1FdHDzEyMxNsSBZLOJlGpe3ULvVM2RgrRAlBqA",
      "developer": {
        "name": "WhatsApp LLC",
        "id": "5191370068110375942",
        "url": "https://play.google.com/store/apps/developer?id=WhatsApp+LLC",
        "email": "android@support.whatsapp.com",
        "website": "http://www.whatsapp.com/",
        "address": "Meta Platforms, Inc., 1 Meta Way\nMenlo Park, CA 94025-1444\nUnited States, +1 650-853-1300"
      },
      "summary": "Simple. Reliable. Private.",
      "installs": "10,000,000,000+",
      "installsMax": 12531168437,
      "score": 4.630478,
      "ratings": 248305554,
      "price": 0,
      "priceText": "Free",
      "currency": "USD",
      "free": true,
      "offersIAP": true,
      "containsAds": false,
      "contentRating": "Everyone",
      "genre": "Communication",
      "genreId": "COMMUNICATION",
      "description": "WhatsApp from Meta is a FREE messaging and video calling app. It’s used by over 2B people in more than 180 countries. It’s simple, reliable, and private, so you can easily keep in touch with your friends and family. What […] (truncated — full text in the live response)",
      "installsMin": 10000000000,
      "scoreText": "4.6",
      "ratingsText": "248,305,554",
      "reviews": 1938636,
      "histogram": {
        "1": 10282614,
        "2": 3406261,
        "3": 9020583,
        "4": 22363799,
        "5": 203232217
      },
      "iapRange": "$0.40 - $199.99 per item",
      "headerImage": "https://play-lh.googleusercontent.com/eLX_xvno2Kq_zvpkkP8yPBRfo2TEQSrv7m7WrQ6xloPsDRJ85XQ28kGYuoR-_EBWh2Uoq2GMlDvlBEIxHRyhuSg",
      "screenshots": [
        "https://play-lh.googleusercontent.com/GQF4h3VL-kklOrVS_f1QRAJJZa2zQyVNcFbKdOIkvI_Pcu1op0Sy3uiryiTxpdcCDs7vEDA9eq0eWbxo0fx_wc8",
        "https://play-lh.googleusercontent.com/Q042rTbs53V_jW_c5fMqnNLCf-FBKiO2KiFKJJACa65oMCkaxqDSFKPQ7TH8nYetS-js-fMV7ptXSsF-aCkvcw",
        "… (7 more in the live response)"
      ],
      "contentRatingDescription": "Users Interact, Shares Location, In-App Purchases",
      "released": "Oct 18, 2010",
      "releasedTimestamp": 1287435533,
      "updated": "Nov 20, 2026",
      "updatedTimestamp": 1795229171,
      "version": null,
      "androidVersion": null,
      "androidMinSdk": null,
      "targetSdk": 35,
      "size": null,
      "editorsChoice": true,
      "chartRank": {
        "chart": "top free communication",
        "rank": "#1"
      },
      "recentChanges": "• You can now choose to share recent group history with new members when you add them to a group\n• Group calls now include the ability to transfer calls between […] (truncated)",
      "privacyPolicy": "https://www.whatsapp.com/legal/privacy-policy",
      "hasTrailer": false,
      "video": null,
      "beta": {
        "joinUrl": "https://play.google.com/apps/testing/com.whatsapp",
        "available": null,
        "note": "Beta availability is not verifiable without a Google sign-in — open the link to see whether the program is open, full, or unavailable."
      },
      "installsShort": "10B+"
    }
  ]
}
```

Echte Live-Ergebnisse, 22. November 2026. Die Zahlen ändern sich mit Plays Updates — `ratings`, `installsMax` und `updated` ändern sich im Laufe der Zeit.

## Ergebnisfelder

Jedes Ergebnis (Suche oder einzelne App) trägt diese Felder:

| Feld | Typ | Beschreibung |
|---|---|---|
| `package` | string | Paketname der App, z. B. `"com.whatsapp"`. |
| `name` | string | Name der App. |
| `url` | string | Play-Store-Seiten-URL der App. |
| `icon` | string | Icon-URL der App (512 px). |
| `headerImage` | string | Feature-Grafik-URL (leer, wenn die App keine hat). |
| `developer.name` | string | Name des Entwicklers. |
| `developer.id` | string | ID des Entwicklers. |
| `developer.url` | string | Entwicklerseite auf Play. |
| `developer.email` | string | Support-E-Mail des Entwicklers (leer, wenn nicht angegeben). |
| `developer.website` | string | Website des Entwicklers (leer, wenn nicht angegeben). |
| `developer.address` | string | Adresse des Entwicklers wie angegeben (leer, wenn nicht angegeben). |
| `summary` | string | Kurzer Slogan aus dem Store-Eintrag. |
| `description` | string | Vollständige Beschreibung, reiner Text (HTML entfernt). |
| `installs` | string | Installationen wie angezeigt, z. B. `"10,000,000,000+"`. |
| `installsMin` | number oder `null` | Minimale Installationen. |
| `installsMax` | number oder `null` | Maximale Installationen. |
| `installsShort` | string | Kurzform der Installationen, z. B. `"10B+"`. |
| `score` | number oder `null` | Durchschnittsbewertung, z. B. `4.630478`. |
| `scoreText` | string oder `null` | Bewertung wie angezeigt, z. B. `"4.6"`. |
| `ratings` | number oder `null` | Gesamtzahl der Bewertungen. |
| `ratingsText` | string oder `null` | Bewertungsanzahl wie angezeigt, z. B. `"248,305,554"`. |
| `reviews` | number oder `null` | Anzahl der geschriebenen Rezensionen. |
| `histogram` | object oder `null` | Bewertungsverteilung: `{"1": …, "2": …, "3": …, "4": …, "5": …}`. |
| `price` | number oder `null` | Preis in `currency`-Einheiten (`0` = kostenlos). `null`, wenn unbekannt. |
| `priceText` | string oder `null` | Preis wie angezeigt, z. B. `"$6.99"`, `"Free"`. |
| `currency` | string oder `null` | Währungscode, z. B. `"USD"`. |
| `free` | boolean oder `null` | `true` = kostenlos, `false` = kostenpflichtig. `null`, wenn unbekannt. |
| `offersIAP` | boolean oder `null` | `true`, wenn die App In-App-Käufe anbietet. |
| `iapRange` | string oder `null` | Preisspanne der In-App-Käufe wie angezeigt, z. B. `"$0.40 - $199.99 per item"`. `null`, wenn die App keine IAP hat. |
| `containsAds` | boolean oder `null` | `true`, wenn der Eintrag „Contains ads“ sagt. |
| `genre` | string | Kategoriename, z. B. `"Communication"`. |
| `genreId` | string | Kategorie-ID, z. B. `"COMMUNICATION"`. |
| `contentRating` | string | Altersfreigabe, z. B. `"Everyone"`, `"Teen"`. |
| `contentRatingDescription` | string | Inhaltsbeschreibungen, z. B. `"Users Interact, Shares Location"`. |
| `released` | string oder `null` | Veröffentlichungsdatum wie angezeigt, z. B. `"Oct 18, 2010"`. `null`, wenn Play keines listet. |
| `releasedTimestamp` | number oder `null` | Veröffentlichungsdatum als Unix-Timestamp. |
| `updated` | string oder `null` | Datum der letzten Aktualisierung wie angezeigt, z. B. `"Nov 20, 2026"`. |
| `updatedTimestamp` | number oder `null` | Datum der letzten Aktualisierung als Unix-Timestamp. |
| `version` | string oder `null` | Aktuelle Version, z. B. `"1.26.66.1"`. `null`, wenn Play für die App keine ausweist (WhatsApp ist so eine App). |
| `androidVersion` | string oder `null` | Label der minimalen Android-Version, z. B. `"8.0"`. |
| `androidMinSdk` | number oder `null` | Minimales Android-SDK-Level, z. B. `26`. |
| `targetSdk` | number oder `null` | Ziel-SDK-Level, z. B. `35`. |
| `size` | immer `null` | Die Downloadgröße wird von der Play-Store-Seite für anonyme Anfragen **nicht offengelegt** — der Store zeigt sie nur im angemeldeten Info-Dialog. Die API meldet `null`, statt zu raten. |
| `editorsChoice` | boolean | `true`, wenn die App das Editors'-Choice-Abzeichen trägt. |
| `chartRank` | object oder `null` | Store-Charts-Position, z. B. `{"chart": "top free communication", "rank": "#1"}`. `null`, wenn die App nicht in den Charts ist. |
| `recentChanges` | string | „Neuigkeiten“-Text, reiner Text (HTML entfernt). Leer, wenn die App keine listet. |
| `privacyPolicy` | string oder `null` | Datenschutz-URL (wenn angegeben). |
| `screenshots` | array aus strings | Screenshot-Bild-URLs. |
| `hasTrailer` | boolean | `true`, wenn der Eintrag einen Trailer-Button hat. |
| `video` | immer `null` | Es wird keine Trailer-Video-URL offengelegt — die Seite trägt nur einen Trailer-Button, das Video lädt beim Klick. `hasTrailer` ist das ehrliche Signal. |
| `beta` | object | Beta-Programm-Infos (siehe unten). |

### Was der alten API fehlte

Die frühere playstore-search lieferte nur drei Felder pro App — `package`, `name`, `rating`. Alles andere in der Tabelle oben ist neu: vollständige Beschreibung, Entwickler (Name, ID, Seite, E-Mail, Website, Adresse), Installationen (min / max / kurz), Bewertung + Bewertungen + Rezensionen + 1–5★-Histogramm, Preis + Währung + kostenlos/kostenpflichtig, IAP-Spanne, enthält Werbung, Genre, Altersfreigabe + Beschreibungen, Veröffentlichungs- und Aktualisierungsdaten, Version, Android-Version / SDK-Level, Screenshots, Feature-Grafik, Editors' Choice, Charts-Rang, Neuigkeiten, Datenschutz, Trailer-Kennzeichen und der Beta-Beitrittslink.

## Beta-Programm

Jedes Ergebnis trägt einen Beta-Beitrittslink:

```json
"beta": {
  "joinUrl": "https://play.google.com/apps/testing/com.whatsapp",
  "available": null,
  "note": "Beta availability is not verifiable without a Google sign-in — open the link to see whether the program is open, full, or unavailable."
}
```

Verwenden Sie `beta.joinUrl` als **Beta beitreten**-Button in Ihrer App oder Website — er öffnet die Beta-Seite von Google Play für diese App, wo der Nutzer sich anmeldet und sieht, ob das Programm offen, voll oder nicht verfügbar ist.

**Warum `available` `null` ist:** Die Beta-Seite (`play.google.com/apps/testing/<package>`) leitet für *jede* App zur Google-Anmeldung weiter — WhatsApp, das eine öffentliche Beta hat, und Netflix, das keine hat, leiten identisch weiter. Es gibt kein Signal in der Weiterleitung, das sagt, ob ein Beta-Programm existiert, also behauptet die API keines. Sie liefert den korrekten Beitrittslink und markiert die Verfügbarkeit als unbekannt, ehrlich. Vertrauen Sie niemals einer API, die Ihnen sagt, eine Beta „existiere“, ohne Anmeldung — sie rät.

## Paginierung

Es gibt kein Seiten-Token. Die Play-Websuche hat keine stabile Paginierung — die Suchseite listet höchstens ~30 Apps pro Anfrage, und `maxResults` ist auf 30 begrenzt. Mehr anzufordern liefert dieselben 30.

## details=basic

`details=basic` überspringt das Laden der Detailseite jeder App, ist also schneller — aber dünner. Die Suchseite rendert vollständige Metadaten (Bewertungen, Installationen, Preis, Zusammenfassung, Abzeichen) nur für das **erste** Ergebnis; die Ergebnisse 2+ sind kompakte Zeilen mit nur Name, Entwickler, Sternebewertung und Icon. Im Basic-Modus kommen diese Felder als `null` zurück, statt geraten zu werden. Verwenden Sie `details=full` (Standard), wenn Sie vollständige Daten brauchen.

## Fehler

| Status | Bedeutung |
|---|---|
| `400` | `q` fehlt oder ein ungültiger Wert für `category`, `price`, `sort`, `hl`, `gl`, `maxResults` oder `details`. Der Body nennt das Problem. |
| `403` | Nur Direktzugriff — der Endpoint verweigert Anfragen, die über einen Server oder kostenlosen Host geproxyt werden. |
| `404` | Unbekannter Pfad oder `/app` mit einem Paket, das nicht existiert. |
| `405` | Nur `GET` (und `OPTIONS` für CORS) wird unterstützt. |
| `502` | Google Play hat nicht geantwortet. Erneut versuchen. |

Eine Anfrage ohne Treffer liefert `200` mit `"success": true` und `"results": []` — kein Fehler.

## Hinweise

- Kein API-Key, keine Anmeldung, kein Rate-Limit-Header — vernünftig verwenden.
- `installs`, `ratingsText`, `scoreText`, `priceText`, `released` und `updated` sind die Strings, die Play anzeigt, also Anzeigetext; die numerischen `Min`- / `Max`-Felder daneben sind die Zahlen zum Sortieren oder Vergleichen.
- `version`, `androidVersion` und `released` sind für manche Apps `null`, weil Play sie für diese Apps einfach nicht ausweist — `null` bedeutet „nicht gelistet“, nicht „fehlt“.
- Die Ergebnisse stammen aus der Google-Play-Suche; was Sie bekommen, hängt von der Anfrage, der Region (`gl`) und der Sprache (`hl`) ab.
- CORS ist offen (`Access-Control-Allow-Origin: *`), der Endpoint funktioniert also von jeder Website oder App aus.

## Über

Gebaut von [FAK LABS](https://faizankhichi.me/) — seriöse Software, im Produktivbetrieb.
