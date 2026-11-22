# Play Store Search API

د [FAK LABS](https://faizankhichi.me/) لخوا د Google Play د لټون API۔ په Google Play کې اپلیکېشنونه ولټوئ — د اپلیکېشن د بشپړو جزیاتو، فلټرونو او ترتیب سره۔ API key ته اړتیا نشته۔

> **دا ریپو یوازې اسناد دي۔** دا عامه endpoint او د هغه د کارولو طریقه تشریح کوي۔ په کې هېڅ کوډ نشته۔

## په خپله ژبه یې ولولئ

- [🇬🇧 English](README.md) · [🇧🇷 Português](README.pt.md) · [🇸🇦 العربية (Arabic)](README.ar.md) · [🇫🇷 Français](README.fr.md) · [🇪🇸 Español](README.es.md) · [🇵🇰 اردو (Urdu)](README.ur.md) · [🇹🇿 Kiswahili](README.sw.md) · [🇩🇪 Deutsch](README.de.md) · [🇮🇩 Bahasa Indonesia](README.id.md) · [🇮🇳 हिन्दी (Hindi)](README.hi.md) · [🇦🇫 پښتو (Pashto)](README.ps.md) · [🇮🇷 فارسی (Farsi)](README.fa.md) · [🇨🇳 简体中文 (Chinese)](README.zh.md)

## Endpoint

```
GET https://play-api.faizankhichi.me/?q=<query>
```

هره غوښتنه پاک JSON بېرته ورکوي۔ یوازینی اړین پیرامیټر `q` دی۔

## چټک پیل

```bash
curl "https://play-api.faizankhichi.me/?q=whatsapp"
```

```js
const res = await fetch("https://play-api.faizankhichi.me/?q=whatsapp&maxResults=5");
const data = await res.json();
data.results.forEach(a => console.log(a.name, a.package, a.score, a.installs));
```

## پیرامیټرونه

| پیرامیټر | ارزښتونه | ډیفالټ | یادښتونه |
|---|---|---|---|
| `q` | د لټون متن (تر ۲۰۰ تورو پورې) | — | **اړین۔** |
| `category` | د Play Store د کټګورۍ id، لکه `communication`، `game_arcade`، `education`، `tools`، `photography`، `game_puzzle` | — | `details=full` (ډیفالټ) ته اړتیا لري۔ په لویو/وړو تورو کې توپیر نه کوي؛ ښودل شوی نوم هم کار کوي (`Communication`)۔ |
| `price` | `free`، `paid`، `all` | `all` | |
| `sort` | `relevance`، `rating`، `installs`، `updated` | `relevance` | `updated` لپاره `details=full` (ډیفالټ) ته اړتیا ده۔ |
| `hl` | د ژبې کوډ، لکه `en`، `ur`، `es` | `en` | د پایلو د انټرفېس ژبه۔ |
| `gl` | د سیمې کوډ، لکه `US`، `PK`، `ES` | `US` | هغه سیمه چې پایلې یې لپاره ځایي شوې دي۔ |
| `maxResults` | 1–30 | `20` | د Play د لټون پاڼه هرې پوښتنې ته تر ټولو زیات ~30 اپلیکېشنونه ښيي، نو پایلې تر 30 پورې محدودې دي۔ |
| `details` | `full`، `basic` | `full` | `full` د هر اپلیکېشن د جزیاتو پاڼه راوړي (بشپړ معلومات)۔ `basic` چټک دی خو لږ دی — لاندې وګورئ۔ |

فلټرونه په آزاده توګه سره یو ځای کېږي — هر پیرامیټر یو ځای کار کوي:

```bash
# پیسې ورکوونکې arcade لوبې، تر ټولو لوړې درجه بندۍ لرونکې لومړی
curl "https://play-api.faizankhichi.me/?q=minecraft&price=paid&category=game_arcade&sort=rating"

# وړیا communication اپلیکېشنونه، په اردو کې، د پاکستان لپاره
curl "https://play-api.faizankhichi.me/?q=chat&price=free&category=communication&hl=ur&gl=PK"

# تر ټولو ډېر نصب شوي photo editorونه، وروستي تازه شوي لومړی
curl "https://play-api.faizankhichi.me/?q=photo+editor&sort=installs"
```

## یو اپلیکېشن

یو اپلیکېشن د هغه د package نوم په واسطه وګورئ، د بشپړو جزیاتو سره — د هغه د beta سره د یوځای کېدو لینک په ګډون:

```
GET https://play-api.faizankhichi.me/app?id=<package>
```

```bash
curl "https://play-api.faizankhichi.me/app?id=com.whatsapp"
curl "https://play-api.faizankhichi.me/app?id=com.mojang.minecraftpe"
```

ځواب `{ "success": true, "app": { … } }` دی — د `app` آبجېکټ په سمه توګه هغه فیلډونه لري چې لاندې لیست شوي دي۔ ناپېژندل شوی package ← `404`۔

## ځواب

د پورتنۍ کچې فیلډونه:

| فیلډ | ډول | تشریح |
|---|---|---|
| `success` | boolean | `true` کله چې غوښتنه کار وکړي۔ |
| `query` | string | پوښتنه لکه څنګه چې ترلاسه شوې۔ |
| `count` | number | په دې ځواب کې د پایلو شمېر۔ |
| `source` | string | `"play-html"` — پایلې د Google Play له خپلو ویب پاڼو څخه راځي۔ |
| `filters` | object | پلي شوي فلټرونه، بېرته ښودل شوي: `category`، `price`، `sort`، `hl`، `gl`، `maxResults`، `details`۔ |
| `results` | array | پایلې (لاندې وګورئ)۔ |

### د ځواب بېلګه

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

رښتینې ژوندۍ پایلې، 22 نومبر 2026۔ شمېرې د Play د تازه کولو سره بدلیږي — `ratings`، `installsMax` او `updated` د وخت په تېرېدو سره بدلیږي۔

## د پایلې فیلډونه

هره پایله (لټون یا یو اپلیکېشن) دا فیلډونه لري:

| فیلډ | ډول | تشریح |
|---|---|---|
| `package` | string | د اپلیکېشن د package نوم، لکه `"com.whatsapp"`۔ |
| `name` | string | د اپلیکېشن نوم۔ |
| `url` | string | په Play Store کې د اپلیکېشن د پاڼې URL۔ |
| `icon` | string | د اپلیکېشن د آیکون URL (512 px)۔ |
| `headerImage` | string | د ځانګړي انځور URL (کله چې اپلیکېشن یې ونه لري خالي وي)۔ |
| `developer.name` | string | د جوړوونکي نوم۔ |
| `developer.id` | string | د جوړوونکي id۔ |
| `developer.url` | string | په Play کې د جوړوونکي پاڼه۔ |
| `developer.email` | string | د جوړوونکي د ملاتړ برېښنالیک (کله چې لیست شوی نه وي خالي وي)۔ |
| `developer.website` | string | د جوړوونکي ویب پاڼه (کله چې لیست شوې نه وي خالي وي)۔ |
| `developer.address` | string | د جوړوونکي پته لکه څنګه چې لیست شوې ده (کله چې لیست شوې نه وي خالي وي)۔ |
| `summary` | string | د پلورنځي له لیست څخه لنډ شعار۔ |
| `description` | string | بشپړ تفصیل، ساده متن (HTML لرې شوی)۔ |
| `installs` | string | نصبونه لکه څنګه چې ښودل کېږي، لکه `"10,000,000,000+"`۔ |
| `installsMin` | number یا `null` | لږ تر لږه نصبونه۔ |
| `installsMax` | number یا `null` | تر ټولو ډېر نصبونه۔ |
| `installsShort` | string | لنډ نصبونه، لکه `"10B+"`۔ |
| `score` | number یا `null` | منځنۍ درجه بندي، لکه `4.630478`۔ |
| `scoreText` | string یا `null` | درجه بندي لکه څنګه چې ښودل کېږي، لکه `"4.6"`۔ |
| `ratings` | number یا `null` | د درجه بندۍ ټول شمېر۔ |
| `ratingsText` | string یا `null` | د درجه بندۍ شمېر لکه څنګه چې ښودل کېږي، لکه `"248,305,554"`۔ |
| `reviews` | number یا `null` | د لیکل شویو بیاکتنو شمېر۔ |
| `histogram` | object یا `null` | د درجه بندۍ وېش: `{"1": …, "2": …, "3": …, "4": …, "5": …}`۔ |
| `price` | number یا `null` | قیمت د `currency` په واحدونو کې (`0` = وړیا)۔ کله چې نامعلوم وي `null`۔ |
| `priceText` | string یا `null` | قیمت لکه څنګه چې ښودل کېږي، لکه `"$6.99"`، `"Free"`۔ |
| `currency` | string یا `null` | د اسعارو کوډ، لکه `"USD"`۔ |
| `free` | boolean یا `null` | `true` = وړیا، `false` = پیسې ورکوونکی۔ کله چې نامعلوم وي `null`۔ |
| `offersIAP` | boolean یا `null` | `true` کله چې اپلیکېشن د اپلیکېشن دننه پېرود وړاندې کوي۔ |
| `iapRange` | string یا `null` | د اپلیکېشن دننه پېرود د قیمت حد لکه څنګه چې ښودل کېږي، لکه `"$0.40 - $199.99 per item"`۔ کله چې اپلیکېشن IAP ونه لري `null`۔ |
| `containsAds` | boolean یا `null` | `true` کله چې لیست "Contains ads" ووايي۔ |
| `genre` | string | د کټګورۍ نوم، لکه `"Communication"`۔ |
| `genreId` | string | د کټګورۍ id، لکه `"COMMUNICATION"`۔ |
| `contentRating` | string | د محتوا درجه بندي، لکه `"Everyone"`، `"Teen"`۔ |
| `contentRatingDescription` | string | د محتوا تشریح کوونکي، لکه `"Users Interact, Shares Location"`۔ |
| `released` | string یا `null` | د خپرېدو نېټه لکه څنګه چې ښودل کېږي، لکه `"Oct 18, 2010"`۔ کله چې Play هېڅ نېټه ونه ښيي `null`۔ |
| `releasedTimestamp` | number یا `null` | د خپرېدو نېټه د Unix timestamp په توګه۔ |
| `updated` | string یا `null` | د وروستي تازه کولو نېټه لکه څنګه چې ښودل کېږي، لکه `"Nov 20, 2026"`۔ |
| `updatedTimestamp` | number یا `null` | د وروستي تازه کولو نېټه د Unix timestamp په توګه۔ |
| `version` | string یا `null` | اوسنی نسخه، لکه `"1.26.66.1"`۔ کله چې Play د اپلیکېشن لپاره نسخه ونه ښيي `null` (WhatsApp یو له دغو اپلیکېشنونو څخه دی)۔ |
| `androidVersion` | string یا `null` | د لږ تر لږه Android نسخې لیبل، لکه `"8.0"`۔ |
| `androidMinSdk` | number یا `null` | د لږ تر لږه Android SDK کچه، لکه `26`۔ |
| `targetSdk` | number یا `null` | د هدف SDK کچه، لکه `35`۔ |
| `size` | تل `null` | د ډاونلوډ اندازه د Play Store په پاڼه کې د نامعلومو غوښتنو لپاره **نه ښودل کېږي** — پلورنځی یې یوازې د ننوتلو وروسته د About په خبرو اترو کې ښيي۔ API د اټکل کولو پر ځای `null` ورکوي۔ |
| `editorsChoice` | boolean | `true` کله چې اپلیکېشن د Editors' Choice بیج ولري۔ |
| `chartRank` | object یا `null` | په پلورنځي کې د چارټ ځای، لکه `{"chart": "top free communication", "rank": "#1"}`۔ کله چې اپلیکېشن په چارټ کې نه وي `null`۔ |
| `recentChanges` | string | "څه نوي دي" متن، ساده متن (HTML لرې شوی)۔ کله چې اپلیکېشن هېڅ ونه ښيي خالي وي۔ |
| `privacyPolicy` | string یا `null` | د محرمیت پالیسۍ URL (کله چې لیست شوې وي)۔ |
| `screenshots` | array of strings | د سکرین شاټ انځورونو URLونه۔ |
| `hasTrailer` | boolean | `true` کله چې لیست د trailer تڼۍ ولري۔ |
| `video` | تل `null` | د trailer ویډیو هېڅ URL نه ښودل کېږي — پاڼه یوازې د Trailer تڼۍ لري، ویډیو په کلیک کولو سره لوډېږي۔ `hasTrailer` رښتینی سیګنال دی۔ |
| `beta` | object | د beta پروګرام معلومات (لاندې وګورئ)۔ |

### پخوانۍ API څه نه درلودل

پخوانۍ playstore-search هر اپلیکېشن ته یوازې درې فیلډونه ورکول — `package`، `name`، `rating`۔ په پورتني جدول کې نور هر څه نوي دي: بشپړ تفصیل، جوړوونکی (نوم، id، پاڼه، برېښنالیک، ویب پاڼه، پته)، نصبونه (لږ / ډېر / لنډ)، نمره + درجه بندۍ + بیاکتنې + 1–5★ وېش، قیمت + اسعار + وړیا/پیسې ورکوونکی، د IAP حد، اعلانونه لري، کټګوري، د محتوا درجه بندي + تشریح کوونکي، د خپرېدو + تازه کولو نېټې، نسخه، د Android نسخه / د SDK کچې، سکرین شاټونه، د سر انځور، Editors' Choice، د چارټ ځای، وروستي بدلونونه، د محرمیت پالیسي، د trailer نښه، او د beta سره د یوځای کېدو لینک۔

## Beta پروګرام

هره پایله د beta سره د یوځای کېدو لینک لري:

```json
"beta": {
  "joinUrl": "https://play.google.com/apps/testing/com.whatsapp",
  "available": null,
  "note": "Beta availability is not verifiable without a Google sign-in — open the link to see whether the program is open, full, or unavailable."
}
```

`beta.joinUrl` په خپل اپلیکېشن یا ویب پاڼه کې د **Join beta** تڼۍ په توګه وکاروئ — دا د هغه اپلیکېشن لپاره د Google Play beta پاڼه پرانیزي، چېرې کاروونکی ننوځي او ګوري چې پروګرام خلاص دی، ډک دی، یا شتون نه لري۔

**ولې `available` `null` دی:** د beta پاڼه (`play.google.com/apps/testing/<package>`) *هر* اپلیکېشن لپاره د Google ننوتلو ته لارښوونه کوي — WhatsApp، چې عامه beta لري، او Netflix، چې نه لري، په یو شان ډول لارښوونه کېږي۔ په لارښوونه کې هېڅ نښه نشته چې ووايي beta پروګرام شته که نه، نو API دا ادعا نه کوي چې شته۔ دا تاسو ته سم یوځای کېدو لینک درکوي او شتون یې نامعلوم نښه کوي، په ریښتینولۍ سره۔ هېڅکله په داسې API باور مه کوئ چې تاسو ته ووايي beta "شته" پرته له ننوتلو څخه — هغه اټکل کوي۔

## پاڼې کول

د پاڼې هېڅ نښه نشته۔ د Play ویب لټون هېڅ ثابت پاڼې کول نه لري — د لټون پاڼه هرې پوښتنې ته تر ټولو زیات ~30 اپلیکېشنونه ښيي، او `maxResults` تر 30 پورې محدود دی۔ د نورو غوښتلو سره هماغه 30 بېرته راځي۔

## details=basic

`details=basic` د هر اپلیکېشن د جزیاتو پاڼه راوړل پریږدي، نو چټک دی — خو لږ دی۔ د لټون پاڼه بشپړ میټاډاټا (درجه بندۍ، نصبونه، قیمت، لنډیز، بیجونه) یوازې د **لومړۍ** پایلې لپاره ښيي؛ له 2 څخه وروسته پایلې لنډې کرښې دي چې یوازې نوم، جوړوونکی، د ستورو درجه بندي او آیکون لري۔ په basic حالت کې هغه فیلډونه د اټکل کولو پر ځای `null` راځي۔ کله چې بشپړو معلوماتو ته اړتیا لرئ `details=full` (ډیفالټ) وکاروئ۔

## تېروتنې

| حالت | معنی |
|---|---|
| `400` | `q` ورک دی، یا د `category`، `price`، `sort`، `hl`، `gl`، `maxResults` یا `details` لپاره ناسم ارزښت دی۔ ځواب ستونزه نوموي۔ |
| `403` | یوازې مستقیم لاسرسی — endpoint د سرور یا وړیا کوربه توب له لارې د proxy غوښتنې ردوي۔ |
| `404` | ناپېژندل شوی لار، یا `/app` د هغه package سره چې شتون نه لري۔ |
| `405` | یوازې `GET` (او د CORS لپاره `OPTIONS`) ملاتړ کېږي۔ |
| `502` | Google Play ځواب ونه وایه۔ بیا هڅه وکړئ۔ |

بې پایلو پوښتنه `200` ورکوي چې `"success": true` او `"results": []` لري — دا تېروتنه نه ده۔

## یادښتونه

- هېڅ API key نشته، هېڅ ننوتل نشته، هېڅ rate-limit سرلیک نشته — په هوښیارۍ سره یې وکاروئ۔
- `installs`، `ratingsText`، `scoreText`، `priceText`، `released` او `updated` هغه strings دي چې Play یې ښيي، نو دا د ښودلو متن دی؛ د هغوی تر څنګ `Min` / `Max` عددي فیلډونه هغه شمېرې دي چې پرې ترتیب یا پرتله کېږي۔
- `version`، `androidVersion` او `released` د ځینو اپلیکېشنونو لپاره `null` دي ځکه چې Play یې د هغو اپلیکېشنونو لپاره په ساده ډول نه ښيي — `null` معنی "لیست شوی نه دی"، نه "ورک دی"۔
- پایلې د Google Play له لټون څخه راځي؛ هغه څه چې تاسو یې ترلاسه کوئ په پوښتنه، سیمه (`gl`) او ژبه (`hl`) پورې اړه لري۔
- CORS خلاص دی (`Access-Control-Allow-Origin: *`)، نو endpoint له هرې ویب پاڼې یا اپلیکېشن څخه کار کوي۔

## په اړه

د [FAK LABS](https://faizankhichi.me/) لخوا جوړ شوی — جدي سافټویر، په تولید کې چلېږي۔
