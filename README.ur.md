# Play Store Search API

[FAK LABS](https://faizankhichi.me/) کی Google Play سرچ API۔ Google Play پر ایپس تلاش کریں — ایپ کی مکمل تفصیلات، فلٹرز اور ترتیب کے ساتھ۔ API key کی ضرورت نہیں۔

> **یہ ریپو صرف دستاویزات کے لیے ہے۔** یہ عوامی endpoint اور اس کے استعمال کا طریقہ بتاتا ہے۔ اس میں کوئی کوڈ نہیں ہے۔

## اپنی زبان میں پڑھیں

- [🇬🇧 English](README.md) · [🇧🇷 Português](README.pt.md) · [🇸🇦 العربية (Arabic)](README.ar.md) · [🇫🇷 Français](README.fr.md) · [🇪🇸 Español](README.es.md) · [🇵🇰 اردو (Urdu)](README.ur.md) · [🇹🇿 Kiswahili](README.sw.md) · [🇩🇪 Deutsch](README.de.md) · [🇮🇩 Bahasa Indonesia](README.id.md) · [🇮🇳 हिन्दी (Hindi)](README.hi.md) · [🇦🇫 پښتو (Pashto)](README.ps.md) · [🇮🇷 فارسی (Farsi)](README.fa.md) · [🇨🇳 简体中文 (Chinese)](README.zh.md)

## Endpoint

```
GET https://play-api.faizankhichi.me/?q=<query>
```

ہر درخواست صاف JSON واپس کرتی ہے۔ واحد لازمی پیرامیٹر `q` ہے۔

## ڈیمو سائٹ

بغیر کوڈ لکھے API آزمائیں: **[لائیو ڈیمو](https://play-api.faizankhichi.me/demo)** — اس API پر بنی ایک سادہ سرچ صفحہ۔ ایپس تلاش کریں، مفت/ادا شدہ فلٹر کریں، مکمل تفصیلات اور اسکرین شاٹس کے لیے کارڈ کھولیں، اور ایپ کے بیٹا پروگرام کا صفحہ کھولنے کے لیے **Join Beta** دبائیں۔

ڈیمو ایک نمائش ہے: یہ وہی ہے جو آپ اس API سے بنا سکتے ہیں۔ اس کا کوڈ [example-site/](https://github.com/FAK-LABS/playstore-search-api/tree/main/example-site) میں ہے — ایک اسٹیٹک `index.html` اور ایک مختصر README۔ اسے کاپی کریں، کہیں بھی ہوسٹ کریں یا جیسے ہے ویسے کھولیں؛ یہ لائیو API کو کال کرتا ہے، بغیر کسی کی کے۔


## فوری آغاز

```bash
curl "https://play-api.faizankhichi.me/?q=whatsapp"
```

```js
const res = await fetch("https://play-api.faizankhichi.me/?q=whatsapp&maxResults=5");
const data = await res.json();
data.results.forEach(a => console.log(a.name, a.package, a.score, a.installs));
```

## پیرامیٹرز

| پیرامیٹر | اقدار | ڈیفالٹ | نوٹس |
|---|---|---|---|
| `q` | سرچ کا متن (زیادہ سے زیادہ 200 حروف) | — | **لازمی۔** |
| `category` | Play Store کی کیٹگری id، جیسے `communication`، `game_arcade`، `education`، `tools`، `photography`، `game_puzzle` | — | `details=full` (ڈیفالٹ) درکار ہے۔ بڑے/چھوٹے حروف سے فرق نہیں پڑتا؛ دکھایا جانے والا نام بھی چلتا ہے (`Communication`)۔ |
| `price` | `free`، `paid`، `all` | `all` | |
| `sort` | `relevance`، `rating`، `installs`، `updated` | `relevance` | `updated` کے لیے `details=full` (ڈیفالٹ) درکار ہے۔ |
| `hl` | زبان کا کوڈ، جیسے `en`، `ur`، `es` | `en` | نتائج کی انٹرفیس زبان۔ |
| `gl` | علاقے کا کوڈ، جیسے `US`، `PK`، `ES` | `US` | وہ علاقہ جس کے لیے نتائج مقامی کیے گئے ہوں۔ |
| `maxResults` | 1–30 | `20` | Play کا سرچ صفحہ ہر سوال پر زیادہ سے زیادہ ~30 ایپس دکھاتا ہے، اس لیے نتائج 30 تک محدود ہیں۔ |
| `details` | `full`، `basic` | `full` | `full` ہر ایپ کا تفصیلی صفحہ لاتا ہے (مکمل ڈیٹا)۔ `basic` تیز ہے مگر ہلکا — نیچے دیکھیں۔ |

فلٹرز آزادانہ ملتے ہیں — ہر پیرامیٹر ایک ساتھ کام کرتا ہے:

```bash
# ادا شدہ arcade گیمز، سب سے زیادہ ریٹنگ والے پہلے
curl "https://play-api.faizankhichi.me/?q=minecraft&price=paid&category=game_arcade&sort=rating"

# مفت communication ایپس، اردو میں، پاکستان کے لیے
curl "https://play-api.faizankhichi.me/?q=chat&price=free&category=communication&hl=ur&gl=PK"

# سب سے زیادہ انسٹال شدہ photo editors، حال ہی میں اپڈیٹ شدہ پہلے
curl "https://play-api.faizankhichi.me/?q=photo+editor&sort=installs"
```

## ایک ایپ

ایک ایپ کو اس کے package نام سے دیکھیں، مکمل تفصیلات کے ساتھ — بشمول beta میں شامل ہونے کا لنک:

```
GET https://play-api.faizankhichi.me/app?id=<package>
```

```bash
curl "https://play-api.faizankhichi.me/app?id=com.whatsapp"
curl "https://play-api.faizankhichi.me/app?id=com.mojang.minecraftpe"
```

جواب `{ "success": true, "app": { … } }` ہوتا ہے — `app` آبجیکٹ میں بالکل وہی فیلڈز ہیں جو نیچے درج ہیں۔ نامعلوم package ← `404`۔

## جواب

اوپری سطح کے فیلڈز:

| فیلڈ | قسم | تفصیل |
|---|---|---|
| `success` | boolean | `true` جب درخواست کام کرے۔ |
| `query` | string | سوال جیسا موصول ہوا۔ |
| `count` | number | اس جواب میں نتائج کی تعداد۔ |
| `source` | string | `"play-html"` — نتائج Google Play کے اپنے ویب صفحات سے آتے ہیں۔ |
| `filters` | object | لگائے گئے فلٹرز، واپس دکھائے گئے: `category`، `price`، `sort`، `hl`، `gl`، `maxResults`، `details`۔ |
| `results` | array | نتائج (نیچے دیکھیں)۔ |

### جواب کی مثال

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

اصل براہ راست نتائج، 22 نومبر 2026۔ Play کی اپڈیٹس کے ساتھ اعداد بدلتے ہیں — `ratings`، `installsMax` اور `updated` وقت کے ساتھ بدلتے ہیں۔

## نتیجے کے فیلڈز

ہر نتیجہ (سرچ یا ایک ایپ) یہ فیلڈز رکھتا ہے:

| فیلڈ | قسم | تفصیل |
|---|---|---|
| `package` | string | ایپ کا package نام، جیسے `"com.whatsapp"`۔ |
| `name` | string | ایپ کا نام۔ |
| `url` | string | ایپ کے Play Store صفحے کا URL۔ |
| `icon` | string | ایپ کے آئیکن کا URL (512 px)۔ |
| `headerImage` | string | فیچر تصویر کا URL (جب ایپ کی نہ ہو تو خالی)۔ |
| `developer.name` | string | ڈویلپر کا نام۔ |
| `developer.id` | string | ڈویلپر کی id۔ |
| `developer.url` | string | Play پر ڈویلپر کا صفحہ۔ |
| `developer.email` | string | ڈویلپر کا سپورٹ ای میل (جب درج نہ ہو تو خالی)۔ |
| `developer.website` | string | ڈویلپر کی ویب سائٹ (جب درج نہ ہو تو خالی)۔ |
| `developer.address` | string | ڈویلپر کا پتہ جیسا درج ہے (جب درج نہ ہو تو خالی)۔ |
| `summary` | string | اسٹور لسٹنگ کی مختصر لائن۔ |
| `description` | string | مکمل تفصیل، سادہ متن (HTML ہٹا دیا گیا)۔ |
| `installs` | string | انسٹالز جیسے دکھائے جاتے ہیں، جیسے `"10,000,000,000+"`۔ |
| `installsMin` | number یا `null` | کم سے کم انسٹالز۔ |
| `installsMax` | number یا `null` | زیادہ سے زیادہ انسٹالز۔ |
| `installsShort` | string | مختصر انسٹالز، جیسے `"10B+"`۔ |
| `score` | number یا `null` | اوسط ریٹنگ، جیسے `4.630478`۔ |
| `scoreText` | string یا `null` | ریٹنگ جیسے دکھائی جاتی ہے، جیسے `"4.6"`۔ |
| `ratings` | number یا `null` | ریٹنگز کی کل تعداد۔ |
| `ratingsText` | string یا `null` | ریٹنگز کی تعداد جیسے دکھائی جاتی ہے، جیسے `"248,305,554"`۔ |
| `reviews` | number یا `null` | تحریری جائزوں کی تعداد۔ |
| `histogram` | object یا `null` | ریٹنگ کی تقسیم: `{"1": …, "2": …, "3": …, "4": …, "5": …}`۔ |
| `price` | number یا `null` | `currency` کی اکائیوں میں قیمت (`0` = مفت)۔ نامعلوم ہو تو `null`۔ |
| `priceText` | string یا `null` | قیمت جیسے دکھائی جاتی ہے، جیسے `"$6.99"`، `"Free"`۔ |
| `currency` | string یا `null` | کرنسی کا کوڈ، جیسے `"USD"`۔ |
| `free` | boolean یا `null` | `true` = مفت، `false` = ادا شدہ۔ نامعلوم ہو تو `null`۔ |
| `offersIAP` | boolean یا `null` | `true` جب ایپ in-app purchases دیتی ہو۔ |
| `iapRange` | string یا `null` | in-app purchases کی قیمت کی حد جیسے دکھائی جاتی ہے، جیسے `"$0.40 - $199.99 per item"`۔ جب IAP نہ ہو تو `null`۔ |
| `containsAds` | boolean یا `null` | `true` جب لسٹنگ میں "Contains ads" لکھا ہو۔ |
| `genre` | string | کیٹگری کا نام، جیسے `"Communication"`۔ |
| `genreId` | string | کیٹگری کی id، جیسے `"COMMUNICATION"`۔ |
| `contentRating` | string | مواد کی ریٹنگ، جیسے `"Everyone"`، `"Teen"`۔ |
| `contentRatingDescription` | string | مواد کی تفصیل، جیسے `"Users Interact, Shares Location"`۔ |
| `released` | string یا `null` | ریلیز کی تاریخ جیسے دکھائی جاتی ہے، جیسے `"Oct 18, 2010"`۔ جب Play کوئی تاریخ نہ دے تو `null`۔ |
| `releasedTimestamp` | number یا `null` | ریلیز کی تاریخ Unix timestamp کے طور پر۔ |
| `updated` | string یا `null` | آخری اپڈیٹ کی تاریخ جیسے دکھائی جاتی ہے، جیسے `"Nov 20, 2026"`۔ |
| `updatedTimestamp` | number یا `null` | آخری اپڈیٹ کی تاریخ Unix timestamp کے طور پر۔ |
| `version` | string یا `null` | موجودہ ورژن، جیسے `"1.26.66.1"`۔ جب Play کسی ایپ کا ورژن نہ دے تو `null` (WhatsApp ایسی ہی ایک ایپ ہے)۔ |
| `androidVersion` | string یا `null` | کم سے کم Android ورژن کا لیبل، جیسے `"8.0"`۔ |
| `androidMinSdk` | number یا `null` | کم سے کم Android SDK لیول، جیسے `26`۔ |
| `targetSdk` | number یا `null` | ٹارگٹ SDK لیول، جیسے `35`۔ |
| `size` | ہمیشہ `null` | ڈاؤن لوڈ سائز Play Store کے صفحے پر گمنام درخواستوں کے لیے **دکھایا نہیں جاتا** — اسٹور اسے صرف سائن ان کے بعد About ڈائیلاگ میں دکھاتا ہے۔ API اندازہ لگانے کے بجائے `null` دیتی ہے۔ |
| `editorsChoice` | boolean | `true` جب ایپ پر Editors' Choice بیج ہو۔ |
| `chartRank` | object یا `null` | اسٹور چارٹ میں مقام، جیسے `{"chart": "top free communication", "rank": "#1"}`۔ جب ایپ چارٹ میں نہ ہو تو `null`۔ |
| `recentChanges` | string | "What's new" کا متن، سادہ متن (HTML ہٹا دیا گیا)۔ جب ایپ کچھ نہ دے تو خالی۔ |
| `privacyPolicy` | string یا `null` | پرائیویسی پالیسی کا URL (جب درج ہو)۔ |
| `screenshots` | array of strings | اسکرین شاٹس کے تصویری URLs۔ |
| `hasTrailer` | boolean | `true` جب لسٹنگ میں trailer بٹن ہو۔ |
| `video` | ہمیشہ `null` | trailer ویڈیو کا کوئی URL نہیں ملتا — صفحے پر صرف Trailer بٹن ہوتا ہے، ویڈیو کلک پر لوڈ ہوتی ہے۔ `hasTrailer` ہی سچا اشارہ ہے۔ |
| `beta` | object | Beta پروگرام کی معلومات (نیچے دیکھیں)۔ |

### پرانی API میں کیا کمی تھی

پرانی playstore-search ہر ایپ کے صرف تین فیلڈز دیتی تھی — `package`، `name`، `rating`۔ اوپر والی جدول میں باقی سب نیا ہے: مکمل تفصیل، ڈویلپر (نام، id، صفحہ، ای میل، ویب سائٹ، پتہ)، انسٹالز (کم / زیادہ / مختصر)، اسکور + ریٹنگز + جائزے + 1–5★ تقسیم، قیمت + کرنسی + مفت/ادا شدہ، IAP حد، اشتہارات، کیٹگری، مواد کی ریٹنگ + تفصیل، ریلیز + اپڈیٹ کی تاریخیں، ورژن، Android ورژن / SDK لیولز، اسکرین شاٹس، ہیڈر تصویر، Editors' Choice، چارٹ مقام، حالیہ تبدیلیاں، پرائیویسی پالیسی، trailer اشارہ، اور beta میں شامل ہونے کا لنک۔

## Beta پروگرام

ہر نتیجے میں beta میں شامل ہونے کا لنک ہوتا ہے:

```json
"beta": {
  "joinUrl": "https://play.google.com/apps/testing/com.whatsapp",
  "available": null,
  "note": "Beta availability is not verifiable without a Google sign-in — open the link to see whether the program is open, full, or unavailable."
}
```

`beta.joinUrl` کو اپنی ایپ یا سائٹ میں **Join beta** بٹن کے طور پر استعمال کریں — یہ اس ایپ کے لیے Google Play کا beta صفحہ کھولتا ہے، جہاں صارف سائن ان کر کے دیکھتا ہے کہ پروگرام کھلا ہے، بھرا ہوا ہے، یا دستیاب نہیں۔

**`available` کیوں `null` ہے:** beta صفحہ (`play.google.com/apps/testing/<package>`) *ہر* ایپ کے لیے Google سائن ان پر بھیجتا ہے — WhatsApp، جس کا عوامی beta ہے، اور Netflix، جس کا نہیں ہے، دونوں ایک جیسے redirect ہوتے ہیں۔ redirect میں کوئی اشارہ نہیں ہوتا کہ beta پروگرام موجود ہے یا نہیں، اس لیے API اس کا دعویٰ نہیں کرتی۔ یہ صحیح join لنک دیتی ہے اور دستیابی کو نامعلوم قرار دیتی ہے، ایمانداری سے۔ کبھی ایسی API پر بھروسہ نہ کریں جو سائن ان کے بغیر بتائے کہ beta "موجود" ہے — وہ اندازہ لگا رہی ہے۔

## صفحہ بندی

کوئی page token نہیں ہے۔ Play کی ویب سرچ میں مستحکم صفحہ بندی نہیں ہے — سرچ صفحہ ہر سوال پر زیادہ سے زیادہ ~30 ایپس دکھاتا ہے، اور `maxResults` 30 تک محدود ہے۔ زیادہ مانگنے پر وہی 30 ملتے ہیں۔

## details=basic

`details=basic` ہر ایپ کا تفصیلی صفحہ لانا چھوڑ دیتا ہے، اس لیے یہ تیز ہے — مگر ہلکا۔ سرچ صفحہ مکمل میٹا ڈیٹا (ریٹنگز، انسٹالز، قیمت، خلاصہ، بیجز) صرف **پہلے** نتیجے کے لیے دکھاتا ہے؛ دوسرے سے آگے کے نتائج مختصر قطاریں ہوتی ہیں جن میں صرف نام، ڈویلپر، اسٹار ریٹنگ اور آئیکن ہوتا ہے۔ basic موڈ میں وہ فیلڈز اندازے کے بجائے `null` آتے ہیں۔ مکمل ڈیٹا چاہیے تو `details=full` (ڈیفالٹ) استعمال کریں۔

## غلطیاں

| اسٹیٹس | مطلب |
|---|---|
| `400` | `q` غائب ہے، یا `category`، `price`، `sort`، `hl`، `gl`، `maxResults` یا `details` کی غلط قیمت ہے۔ جواب کا جسم مسئلہ بتاتا ہے۔ |
| `403` | صرف براہ راست رسائی — endpoint سرور یا مفت ہوسٹنگ کے ذریعے proxy درخواستوں کو رد کرتا ہے۔ |
| `404` | نامعلوم راستہ، یا `/app` جس کا package موجود نہ ہو۔ |
| `405` | صرف `GET` (اور CORS کے لیے `OPTIONS`) سپورٹ ہے۔ |
| `502` | Google Play نے جواب نہیں دیا۔ دوبارہ کوشش کریں۔ |

بغیر نتائج والا سوال `200` دیتا ہے جس میں `"success": true` اور `"results": []` ہوتا ہے — یہ غلطی نہیں ہے۔

## نوٹس

- کوئی API key نہیں، کوئی سائن ان نہیں، کوئی rate-limit ہیڈر نہیں — سمجھداری سے استعمال کریں۔
- `installs`، `ratingsText`، `scoreText`، `priceText`، `released` اور `updated` وہ strings ہیں جو Play دکھاتا ہے، اس لیے یہ دکھاوے کا متن ہیں؛ ان کے ساتھ والے `Min` / `Max` عددی فیلڈز وہ نمبر ہیں جن پر ترتیب یا موازنہ کرنا ہے۔
- `version`، `androidVersion` اور `released` کچھ ایپس کے لیے `null` ہوتے ہیں کیونکہ Play ان ایپس کے لیے انہیں دکھاتا ہی نہیں — `null` کا مطلب "درج نہیں"، "غائب" نہیں۔
- نتائج Google Play کی سرچ سے آتے ہیں؛ آپ کو کیا ملتا ہے یہ سوال، علاقے (`gl`) اور زبان (`hl`) پر منحصر ہے۔
- CORS کھلا ہے (`Access-Control-Allow-Origin: *`)، اس لیے endpoint کسی بھی ویب سائٹ یا ایپ سے کام کرتا ہے۔

## بارے میں

[FAK LABS](https://faizankhichi.me/) کی بنائی ہوئی — سنجیدہ سافٹ ویئر، پروڈکشن میں چل رہا ہے۔
