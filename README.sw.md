# Play Store Search API

API ya kutafuta Google Play kutoka [FAK LABS](https://faizankhichi.me/). Tafuta programu kwenye Google Play — pamoja na maelezo kamili ya programu, vichujio na mpangilio. Hakuna API key inayohitajika.

> **Repo hii ni nyaraka tu.** Inaeleza endpoint ya umma na jinsi ya kuitumia. Haina code yoyote.

## Soma kwa lugha yako

- [🇬🇧 English](README.md) · [🇧🇷 Português](README.pt.md) · [🇸🇦 العربية (Arabic)](README.ar.md) · [🇫🇷 Français](README.fr.md) · [🇪🇸 Español](README.es.md) · [🇵🇰 اردو (Urdu)](README.ur.md) · [🇹🇿 Kiswahili](README.sw.md) · [🇩🇪 Deutsch](README.de.md) · [🇮🇩 Bahasa Indonesia](README.id.md) · [🇮🇳 हिन्दी (Hindi)](README.hi.md) · [🇦🇫 پښتو (Pashto)](README.ps.md) · [🇮🇷 فارسی (Farsi)](README.fa.md) · [🇨🇳 简体中文 (Chinese)](README.zh.md)

## Endpoint

```
GET https://play-api.faizankhichi.me/?q=<query>
```

Kila ombi linarudisha JSON safi. Kigezo pekee cha lazima ni `q`.

## Tovuti ya onyesho

Jaribu API bila kuandika msimbo: **[onyesho la moja kwa moja](https://play-api.faizankhichi.me/demo)** — ukurasa rahisi wa kutafuta uliojengwa kwa API hii. Tafuta programu, chuja za bure/zilipwa, fungua kadi kwa maelezo kamili na picha za skrini, na gusa **Join Beta** ili kufungua ukurasa wa programu ya beta ya programu.

Onyesho hili ni mfano: hii ndiyo unayoweza kujenga kwa API hii. Msimbo wake uko katika [example-site/](https://github.com/FAK-LABS/playstore-search-api/tree/main/example-site) — faili moja tuli la `index.html` pamoja na README fupi. Inakili, ipangishe popote, au ifungue kama ilivyo; inaita API ya moja kwa moja, bila ufunguo.


## Anza haraka

```bash
curl "https://play-api.faizankhichi.me/?q=whatsapp"
```

```js
const res = await fetch("https://play-api.faizankhichi.me/?q=whatsapp&maxResults=5");
const data = await res.json();
data.results.forEach(a => console.log(a.name, a.package, a.score, a.installs));
```

## Vigezo

| Kigezo | Thamani | Chaguo-msingi | Maelezo |
|---|---|---|---|
| `q` | maandishi ya utafutaji (herufi 200 kama kiwango cha juu) | — | **Lazima.** |
| `category` | id ya aina ya Play Store, mf. `communication`, `game_arcade`, `education`, `tools`, `photography`, `game_puzzle` | — | Inahitaji `details=full` (chaguo-msingi). Haijali herufi kubwa/ndogo; jina linaloonyeshwa pia linafanya kazi (`Communication`). |
| `price` | `free`, `paid`, `all` | `all` | |
| `sort` | `relevance`, `rating`, `installs`, `updated` | `relevance` | `updated` inahitaji `details=full` (chaguo-msingi). |
| `hl` | msimbo wa lugha, mf. `en`, `ur`, `es` | `en` | Lugha ya kiolesura cha matokeo. |
| `gl` | msimbo wa eneo, mf. `US`, `PK`, `ES` | `US` | Eneo ambalo matokeo yamelengwa. |
| `maxResults` | 1–30 | `20` | Ukurasa wa utafutaji wa Play unaorodhesha programu ~30 kama kiwango cha juu kwa swali moja, kwa hivyo matokeo yamewekewa kikomo cha 30. |
| `details` | `full`, `basic` | `full` | `full` inachukua ukurasa wa maelezo ya kila programu (data kamili). `basic` ni haraka lakini nyepesi — tazama hapa chini. |

Vichujio vinachanganyika kwa uhuru — kila kigezo kinafanya kazi pamoja:

```bash
# michezo ya arcade inayolipwa, yenye alama bora kwanza
curl "https://play-api.faizankhichi.me/?q=minecraft&price=paid&category=game_arcade&sort=rating"

# programu za mawasiliano za bure, kwa Kiurdu, kwa Pakistan
curl "https://play-api.faizankhichi.me/?q=chat&price=free&category=communication&hl=ur&gl=PK"

# vihariri vya picha vilivyopakuliwa zaidi, vilivyosasishwa hivi karibuni kwanza
curl "https://play-api.faizankhichi.me/?q=photo+editor&sort=installs"
```

## Programu moja

Tafuta programu moja kwa jina lake la kifurushi, pamoja na maelezo kamili — ikiwa ni pamoja na kiungo chake cha kujiunga na beta:

```
GET https://play-api.faizankhichi.me/app?id=<package>
```

```bash
curl "https://play-api.faizankhichi.me/app?id=com.whatsapp"
curl "https://play-api.faizankhichi.me/app?id=com.mojang.minecraftpe"
```

Jibu ni `{ "success": true, "app": { … } }` — kitu cha `app` kina sehemu zilezile zilizoorodheshwa hapa chini. Kifurushi kisichojulikana → `404`.

## Jibu

Sehemu za ngazi ya juu:

| Sehemu | Aina | Maelezo |
|---|---|---|
| `success` | boolean | `true` ombi likifanikiwa. |
| `query` | string | Swali kama lilivyopokelewa. |
| `count` | number | Idadi ya matokeo katika jibu hili. |
| `source` | string | `"play-html"` — matokeo yanatoka kwenye kurasa za wavuti za Google Play zenyewe. |
| `filters` | object | Vichujio vilivyotumika, vimerudishwa: `category`, `price`, `sort`, `hl`, `gl`, `maxResults`, `details`. |
| `results` | array | Matokeo (tazama hapa chini). |

### Mfano wa jibu

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

Matokeo halisi ya moja kwa moja, 22 Novemba 2026. Nambari hubadilika Play inaposasisha — `ratings`, `installsMax` na `updated` hubadilika kadri muda unavyopita.

## Sehemu za matokeo

Kila tokeo (utafutaji au programu moja) lina sehemu hizi:

| Sehemu | Aina | Maelezo |
|---|---|---|
| `package` | string | Jina la kifurushi cha programu, mf. `"com.whatsapp"`. |
| `name` | string | Jina la programu. |
| `url` | string | URL ya ukurasa wa programu kwenye Play Store. |
| `icon` | string | URL ya ikoni ya programu (pikseli 512). |
| `headerImage` | string | URL ya picha ya kichwa (tupu programu isipo na). |
| `developer.name` | string | Jina la msanidi. |
| `developer.id` | string | Id ya msanidi. |
| `developer.url` | string | Ukurasa wa msanidi kwenye Play. |
| `developer.email` | string | Barua pepe ya msaada ya msanidi (tupu isipoorodheshwa). |
| `developer.website` | string | Tovuti ya msanidi (tupu isipoorodheshwa). |
| `developer.address` | string | Anwani ya msanidi kama ilivyoorodheshwa (tupu isipoorodheshwa). |
| `summary` | string | Muhtasari mfupi kutoka kwenye orodha ya duka. |
| `description` | string | Maelezo kamili, maandishi wazi (HTML imeondolewa). |
| `installs` | string | Usakinishaji kama unavyoonyeshwa, mf. `"10,000,000,000+"`. |
| `installsMin` | number au `null` | Usakinishaji wa chini kabisa. |
| `installsMax` | number au `null` | Usakinishaji wa juu kabisa. |
| `installsShort` | string | Usakinishaji mfupi, mf. `"10B+"`. |
| `score` | number au `null` | Alama ya wastani, mf. `4.630478`. |
| `scoreText` | string au `null` | Alama kama inavyoonyeshwa, mf. `"4.6"`. |
| `ratings` | number au `null` | Jumla ya idadi ya alama. |
| `ratingsText` | string au `null` | Idadi ya alama kama inavyoonyeshwa, mf. `"248,305,554"`. |
| `reviews` | number au `null` | Idadi ya mapitio yaliyoandikwa. |
| `histogram` | object au `null` | Mgawanyo wa alama: `{"1": …, "2": …, "3": …, "4": …, "5": …}`. |
| `price` | number au `null` | Bei katika vitengo vya `currency` (`0` = bure). `null` isipojulikana. |
| `priceText` | string au `null` | Bei kama inavyoonyeshwa, mf. `"$6.99"`, `"Free"`. |
| `currency` | string au `null` | Msimbo wa sarafu, mf. `"USD"`. |
| `free` | boolean au `null` | `true` = bure, `false` = inalipwa. `null` isipojulikana. |
| `offersIAP` | boolean au `null` | `true` programu inapotoa ununuzi wa ndani ya programu. |
| `iapRange` | string au `null` | Kiwango cha bei za ununuzi wa ndani ya programu kama kinavyoonyeshwa, mf. `"$0.40 - $199.99 per item"`. `null` programu isipo na IAP. |
| `containsAds` | boolean au `null` | `true` orodha inapose "Contains ads". |
| `genre` | string | Jina la aina, mf. `"Communication"`. |
| `genreId` | string | Id ya aina, mf. `"COMMUNICATION"`. |
| `contentRating` | string | Ukadiriaji wa maudhui, mf. `"Everyone"`, `"Teen"`. |
| `contentRatingDescription` | string | Maelezo ya maudhui, mf. `"Users Interact, Shares Location"`. |
| `released` | string au `null` | Tarehe ya kutolewa kama inavyoonyeshwa, mf. `"Oct 18, 2010"`. `null` Play isipoorodhesha tarehe. |
| `releasedTimestamp` | number au `null` | Tarehe ya kutolewa kama Unix timestamp. |
| `updated` | string au `null` | Tarehe ya sasisho la mwisho kama inavyoonyeshwa, mf. `"Nov 20, 2026"`. |
| `updatedTimestamp` | number au `null` | Tarehe ya sasisho la mwisho kama Unix timestamp. |
| `version` | string au `null` | Toleo la sasa, mf. `"1.26.66.1"`. `null` Play isipoonyesha toleo la programu (WhatsApp ni moja ya programu hizo). |
| `androidVersion` | string au `null` | Lebo ya toleo la chini kabisa la Android, mf. `"8.0"`. |
| `androidMinSdk` | number au `null` | Kiwango cha chini cha Android SDK, mf. `26`. |
| `targetSdk` | number au `null` | Kiwango cha SDK kinacholengwa, mf. `35`. |
| `size` | daima `null` | Ukubwa wa upakuaji **hauonyeshwi** kwenye ukurasa wa Play Store kwa maombi yasiyo na jina — duka huuonyesha tu ndani ya kisanduku cha Kuhusu baada ya kuingia. API inarudisha `null` badala ya kubahatisha. |
| `editorsChoice` | boolean | `true` programu inapobeba beji ya Editors' Choice. |
| `chartRank` | object au `null` | Nafasi kwenye chati ya duka, mf. `{"chart": "top free communication", "rank": "#1"}`. `null` programu isipo kwenye chati. |
| `recentChanges` | string | Maandishi ya "Nini kipya", maandishi wazi (HTML imeondolewa). Tupu programu isipoorodhesha chochote. |
| `privacyPolicy` | string au `null` | URL ya sera ya faragha (inapoorodheshwa). |
| `screenshots` | array ya strings | URL za picha za skrini. |
| `hasTrailer` | boolean | `true` orodha inapokuwa na kitufe cha trela. |
| `video` | daima `null` | Hakuna URL ya video ya trela inayoonyeshwa — ukurasa una kitufe cha Trailer tu, video inapakiwa kwa kubofya. `hasTrailer` ndiyo ishara ya kweli. |
| `beta` | object | Taarifa za programu ya beta (tazama hapa chini). |

### API ya zamani ilikosa nini

Playstore-search ya zamani ilirudisha sehemu tatu tu kwa kila programu — `package`, `name`, `rating`. Kila kitu kingine kwenye jedwali hapo juu ni kipya: maelezo kamili, msanidi (jina, id, ukurasa, barua pepe, tovuti, anwani), usakinishaji (chini / juu / mfupi), alama + alama + mapitio + mgawanyo wa 1–5★, bei + sarafu + bure/inalipwa, kiwango cha IAP, ina matangazo, aina, ukadiriaji wa maudhui + maelezo, tarehe za kutolewa na kusasishwa, toleo, toleo la Android / viwango vya SDK, picha za skrini, picha ya kichwa, Editors' Choice, nafasi kwenye chati, mabadiliko ya hivi karibuni, sera ya faragha, kiashiria cha trela, na kiungo cha kujiunga na beta.

## Programu ya beta

Kila tokeo lina kiungo cha kujiunga na beta:

```json
"beta": {
  "joinUrl": "https://play.google.com/apps/testing/com.whatsapp",
  "available": null,
  "note": "Beta availability is not verifiable without a Google sign-in — open the link to see whether the program is open, full, or unavailable."
}
```

Tumia `beta.joinUrl` kama kitufe cha **Jiunge na beta** kwenye programu au tovuti yako — inafungua ukurasa wa beta wa Google Play wa programu hiyo, ambapo mtumiaji anaingia na kuona kama programu iko wazi, imejaa, au haipatikani.

**Kwa nini `available` ni `null`:** ukurasa wa beta (`play.google.com/apps/testing/<package>`) unaelekeza kwenye kuingia kwa Google kwa *kila* programu — WhatsApp, ambayo ina beta ya umma, na Netflix, ambayo haina, zinaelekezwa kwa njia ileile. Hakuna ishara kwenye uelekezaji inayosema kama programu ya beta ipo, kwa hivyo API haidai kuwa ipo. Inakupa kiungo sahihi cha kujiunga na kuweka upatikanaji kuwa haujulikani, kwa uaminifu. Kamwe usiamini API inayokuambia beta "ipo" bila kuingia — inabahatisha.

## Kurasa (pagination)

Hakuna token ya ukurasa. Utafutaji wa wavuti wa Play hauna kurasa thabiti — ukurasa wa utafutaji unaorodhesha programu ~30 kama kiwango cha juu kwa swali moja, na `maxResults` imewekewa kikomo cha 30. Kuomba zaidi kunarudisha zile 30 zilezile.

## details=basic

`details=basic` inaruka kuchukua ukurasa wa maelezo ya kila programu, kwa hivyo ni haraka — lakini nyepesi. Ukurasa wa utafutaji unaonyesha metadata kamili (alama, usakinishaji, bei, muhtasari, beji) kwa tokeo la **kwanza** tu; matokeo ya 2+ ni safu fupi zenye jina, msanidi, alama ya nyota na ikoni tu. Katika hali ya basic, sehemu hizo zinarudi `null` badala ya kubahatisha. Tumia `details=full` (chaguo-msingi) unapohitaji data kamili.

## Makosa

| Hali | Maana |
|---|---|
| `400` | `q` haipo, au thamani batili kwa `category`, `price`, `sort`, `hl`, `gl`, `maxResults` au `details`. Mwili unataja tatizo. |
| `403` | Ufikiaji wa moja kwa moja tu — endpoint inakataa maombi yaliyopitishwa kupitia seva au mwenyeji wa bure. |
| `404` | Njia isiyojulikana, au `/app` yenye kifurushi kisichopo. |
| `405` | `GET` tu (na `OPTIONS` kwa CORS) ndiyo inayotumika. |
| `502` | Google Play haikujibu. Jaribu tena. |

Swali lisilo na matokeo linarudisha `200` pamoja na `"success": true` na `"results": []` — si kosa.

## Maelezo

- Hakuna API key, hakuna kuingia, hakuna kichwa cha kikomo cha kiwango — itumie kwa busara.
- `installs`, `ratingsText`, `scoreText`, `priceText`, `released` na `updated` ni maandishi ambayo Play inaonyesha, kwa hivyo ni maandishi ya kuonyesha; sehemu za nambari `Min` / `Max` karibu nazo ndizo nambari za kupanga au kulinganisha.
- `version`, `androidVersion` na `released` ni `null` kwa baadhi ya programu kwa sababu Play hazioni kwa programu hizo — `null` inamaanisha "haijaorodheshwa", si "imekosekana".
- Matokeo yanatoka kwenye utafutaji wa Google Play; unachopata kinategemea swali, eneo (`gl`) na lugha (`hl`).
- CORS iko wazi (`Access-Control-Allow-Origin: *`), kwa hivyo endpoint inafanya kazi kutoka kwenye tovuti au programu yoyote.

## Kuhusu

Imejengwa na [FAK LABS](https://faizankhichi.me/) — programu nzito, inayoendeshwa kwenye uzalishaji.
