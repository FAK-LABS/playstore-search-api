# Play Store Search API

A Google Play search API by [FAK LABS](https://faizankhichi.me/). Search apps on Google Play — with full app details, filters, and sorting. No API key needed.

> **This repo is documentation only.** It describes the public endpoint and how to use it. It contains no code.

## Read this in your language

- [🇬🇧 English](README.md) · [🇧🇷 Português](README.pt.md) · [🇸🇦 العربية (Arabic)](README.ar.md) · [🇫🇷 Français](README.fr.md) · [🇪🇸 Español](README.es.md) · [🇵🇰 اردو (Urdu)](README.ur.md) · [🇹🇿 Kiswahili](README.sw.md) · [🇩🇪 Deutsch](README.de.md) · [🇮🇩 Bahasa Indonesia](README.id.md) · [🇮🇳 हिन्दी (Hindi)](README.hi.md) · [🇦🇫 پښتو (Pashto)](README.ps.md) · [🇮🇷 فارسی (Farsi)](README.fa.md) · [🇨🇳 简体中文 (Chinese)](README.zh.md)

## Endpoint

```
GET https://play-api.faizankhichi.me/?q=<query>
```

Every request returns clean JSON. The only required parameter is `q`.

## Demo site

Try the API without writing code: **[live demo](https://play-api.faizankhichi.me/demo)** — a basic search page built on this API. Search apps, filter free/paid, open a card for full details and screenshots, and hit **Join Beta** to open an app's beta program page.

The demo is a showcase: it is what you can build with this API. Its code is in [example-site/](https://github.com/FAK-LABS/playstore-search-api/tree/main/example-site) — one static `index.html` plus a short README. Copy it, host it anywhere, or open it as-is; it calls the live API, no key needed.


## Quick start

```bash
curl "https://play-api.faizankhichi.me/?q=whatsapp"
```

```js
const res = await fetch("https://play-api.faizankhichi.me/?q=whatsapp&maxResults=5");
const data = await res.json();
data.results.forEach(a => console.log(a.name, a.package, a.score, a.installs));
```

## Parameters

| Parameter | Values | Default | Notes |
|---|---|---|---|
| `q` | search text (max 200 chars) | — | **Required.** |
| `category` | Play Store genre id, e.g. `communication`, `game_arcade`, `education`, `tools`, `photography`, `game_puzzle` | — | Implies `details=full` (fetches detail pages). Case-insensitive; the display name works too (`Communication`). |
| `price` | `free`, `paid`, `all` | `all` | Filtered by Play itself, so it costs no extra calls. |
| `sort` | `relevance`, `rating`, `installs`, `updated` | `relevance` | `installs` and `updated` imply `details=full`. `rating` sorts on the star rating printed on every card. |
| `hl` | language code, e.g. `en`, `ur`, `es` | `en` | Interface language of the results. |
| `gl` | region code, e.g. `US`, `PK`, `ES` | `US` | Region the results are localised for. |
| `maxResults` | 1–30 | `20` | The Play search page lists at most ~30 apps per query, so results cap at 30. |
| `details` | `full`, `basic` | `basic` | `basic` (default) reads one search page only — 1 upstream call. `full` fetches every app's details page (complete data). |

Filters combine freely — every parameter works together:

```bash
# paid arcade games, best rated first
curl "https://play-api.faizankhichi.me/?q=minecraft&price=paid&category=game_arcade&sort=rating"

# free communication apps, in Urdu, for Pakistan
curl "https://play-api.faizankhichi.me/?q=chat&price=free&category=communication&hl=ur&gl=PK"

# most-installed photo editors, recently updated first
curl "https://play-api.faizankhichi.me/?q=photo+editor&sort=installs"
```

## Single app

Look up one app by its package name, with full details — including its beta join link:

```
GET https://play-api.faizankhichi.me/app?id=<package>
```

```bash
curl "https://play-api.faizankhichi.me/app?id=com.whatsapp"
curl "https://play-api.faizankhichi.me/app?id=com.mojang.minecraftpe"
```

The response is `{ "success": true, "app": { … } }` — the `app` object has exactly the fields listed below. Unknown package → `404`.

## Response

Top-level fields:

| Field | Type | Description |
|---|---|---|
| `success` | boolean | `true` when the request worked. |
| `query` | string | The query as received. |
| `count` | number | Number of results in this response. |
| `source` | string | `"play-html"` — results come from Google Play's own web pages. |
| `filters` | object | The filters that were applied, echoed back: `category`, `price`, `sort`, `hl`, `gl`, `maxResults`, `details`. |
| `results` | array | The results (see below). |

### Example response

`GET https://play-api.faizankhichi.me/?q=whatsapp&maxResults=1&details=full`

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

Real live results, 22 November 2026. Numbers move as Play updates — `ratings`, `installsMax` and `updated` change over time.

## Result fields

Every result (search or single-app) carries these fields:

| Field | Type | Description |
|---|---|---|
| `package` | string | App package name, e.g. `"com.whatsapp"`. |
| `name` | string | App name. |
| `url` | string | Play Store page URL for the app. |
| `icon` | string | App icon URL (512 px). |
| `headerImage` | string | Feature graphic URL (empty when the app has none). |
| `developer.name` | string | Developer name. |
| `developer.id` | string | Developer id. |
| `developer.url` | string | Developer page on Play. |
| `developer.email` | string | Developer support email (empty when not listed). |
| `developer.website` | string | Developer website (empty when not listed). |
| `developer.address` | string | Developer address as listed (empty when not listed). |
| `summary` | string | Short tagline from the store listing. |
| `description` | string | Full description, plain text (HTML stripped). |
| `installs` | string | Installs as shown, e.g. `"10,000,000,000+"`. |
| `installsMin` | number or `null` | Minimum installs. |
| `installsMax` | number or `null` | Maximum installs. |
| `installsShort` | string | Short installs, e.g. `"10B+"`. |
| `score` | number or `null` | Average rating, e.g. `4.630478`. |
| `scoreText` | string or `null` | Rating as shown, e.g. `"4.6"`. |
| `ratings` | number or `null` | Total number of ratings. |
| `ratingsText` | string or `null` | Ratings count as shown, e.g. `"248,305,554"`. |
| `reviews` | number or `null` | Number of written reviews. |
| `histogram` | object or `null` | Rating breakdown: `{"1": …, "2": …, "3": …, "4": …, "5": …}`. |
| `price` | number or `null` | Price in `currency` units (`0` = free). `null` when unknown. |
| `priceText` | string or `null` | Price as shown, e.g. `"$6.99"`, `"Free"`. |
| `currency` | string or `null` | Currency code, e.g. `"USD"`. |
| `free` | boolean or `null` | `true` = free, `false` = paid. `null` when unknown. |
| `offersIAP` | boolean or `null` | `true` when the app offers in-app purchases. |
| `iapRange` | string or `null` | In-app purchase price range as shown, e.g. `"$0.40 - $199.99 per item"`. `null` when the app has no IAP. |
| `containsAds` | boolean or `null` | `true` when the listing says "Contains ads". |
| `genre` | string | Category name, e.g. `"Communication"`. |
| `genreId` | string | Category id, e.g. `"COMMUNICATION"`. |
| `contentRating` | string | Content rating, e.g. `"Everyone"`, `"Teen"`. |
| `contentRatingDescription` | string | Content descriptors, e.g. `"Users Interact, Shares Location"`. |
| `released` | string or `null` | Release date as shown, e.g. `"Oct 18, 2010"`. `null` when Play lists none. |
| `releasedTimestamp` | number or `null` | Release date as a Unix timestamp. |
| `updated` | string or `null` | Last update date as shown, e.g. `"Nov 20, 2026"`. |
| `updatedTimestamp` | number or `null` | Last update date as a Unix timestamp. |
| `version` | string or `null` | Current version, e.g. `"1.26.66.1"`. `null` when Play does not expose one for the app (WhatsApp is one such app). |
| `androidVersion` | string or `null` | Minimum Android version label, e.g. `"8.0"`. |
| `androidMinSdk` | number or `null` | Minimum Android SDK level, e.g. `26`. |
| `targetSdk` | number or `null` | Target SDK level, e.g. `35`. |
| `size` | always `null` | Download size is **not exposed** by the Play Store page for anonymous requests — the store only shows it inside the signed-in About dialog. The API reports `null` rather than guessing. |
| `editorsChoice` | boolean | `true` when the app carries the Editors' Choice badge. |
| `chartRank` | object or `null` | Store chart position, e.g. `{"chart": "top free communication", "rank": "#1"}`. `null` when the app is not charted. |
| `recentChanges` | string | "What's new" text, plain text (HTML stripped). Empty when the app lists none. |
| `privacyPolicy` | string or `null` | Privacy policy URL (when listed). |
| `screenshots` | array of strings | Screenshot image URLs. |
| `hasTrailer` | boolean | `true` when the listing has a trailer button. |
| `video` | always `null` | No trailer video URL is exposed — the page only carries a Trailer button, the video loads on click. `hasTrailer` is the honest signal. |
| `beta` | object | Beta program info (see below). |

### What the old API was missing

The earlier playstore-search returned only three fields per app — `package`, `name`, `rating`. Everything else in the table above is new: full description, developer (name, id, page, email, website, address), installs (min / max / short), score + ratings + reviews + 1–5★ histogram, price + currency + free/paid, IAP range, contains ads, genre, content rating + descriptors, released + updated dates, version, Android version / SDK levels, screenshots, header image, Editors' Choice, chart rank, recent changes, privacy policy, trailer flag, and the beta join link.

## Beta program

Every result carries a beta join link:

```json
"beta": {
  "joinUrl": "https://play.google.com/apps/testing/com.whatsapp",
  "available": null,
  "note": "Beta availability is not verifiable without a Google sign-in — open the link to see whether the program is open, full, or unavailable."
}
```

Use `beta.joinUrl` as the **Join beta** button in your app or site — it opens Google Play's beta page for that app, where the user signs in and sees whether the program is open, full, or unavailable.

**Why `available` is `null`:** the beta page (`play.google.com/apps/testing/<package>`) redirects to a Google sign-in for *every* app — WhatsApp, which has a public beta, and Netflix, which does not, redirect identically. There is no signal in the redirect that says whether a beta program exists, so the API does not claim one does. It gives you the correct join link and marks availability unknown, honestly. Never trust an API that tells you a beta "exists" without a sign-in — it is guessing.

## Pagination

There is no page token. Play's web search has no stable pagination — the search page lists at most ~30 apps for a query, and `maxResults` caps at 30. Asking for more returns the same 30.

## details=basic (the default)

`details=basic` is the default: one upstream call — the search page only, nothing else. It is thinner by design. The search page only renders full metadata (reviews, installs, price, summary, badges) for the **first** result; results 2+ are compact rows carrying only name, developer, star rating and icon. In basic mode those fields come back `null` rather than guessed. Every row still carries its star rating, so `sort=rating` works in basic mode, and `price` is filtered by Play itself, so it costs no extra calls either.

Detail pages are fetched only on purpose: pass `details=full` explicitly, or ask for something that needs a detail-only field — `category` (genre), `sort=installs` (install counts), `sort=updated` (updated date). In those cases the API fetches the detail pages and the echoed `details` in the response reads `"full"`.

## Errors

| Status | Meaning |
|---|---|
| `400` | Missing `q`, or an invalid value for `category`, `price`, `sort`, `hl`, `gl`, `maxResults` or `details`. The body names the problem. |
| `403` | Direct access only — the endpoint refuses requests proxied through a server or free host. |
| `404` | Unknown path, or `/app` with a package that does not exist. |
| `405` | Only `GET` (and `OPTIONS` for CORS) is supported. |
| `502` | Google Play did not answer. Retry. |

A query with no matches returns `200` with `"success": true` and `"results": []` — not an error.

## Notes

- No API key, no sign-in, no rate-limit header — use it reasonably.
- `installs`, `ratingsText`, `scoreText`, `priceText`, `released` and `updated` are the strings Play shows, so they are display text; the `Min` / `Max` / numeric fields next to them are the numbers to sort or compare on.
- `version`, `androidVersion` and `released` are `null` for some apps because Play simply does not expose them for those apps — `null` means "not listed", not "missing".
- Results come from Google Play's search; what you get depends on the query, region (`gl`) and language (`hl`).
- CORS is open (`Access-Control-Allow-Origin: *`), so the endpoint works from any website or app.

## About

Built by [FAK LABS](https://faizankhichi.me/) — serious software, running in production.
