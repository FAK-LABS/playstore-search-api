# Play Store Search API

واجهة برمجة تطبيقات للبحث في Google Play من [FAK LABS](https://faizankhichi.me/). ابحث عن التطبيقات في Google Play — مع تفاصيل كاملة عن التطبيق وفلاتر وترتيب. لا حاجة إلى مفتاح API.

> **هذا المستودع للتوثيق فقط.** يصف نقطة النهاية (endpoint) العامة وكيفية استخدامها. لا يحتوي على أي كود.

## اقرأ بلغتك

- [🇬🇧 English](README.md) · [🇧🇷 Português](README.pt.md) · [🇸🇦 العربية (Arabic)](README.ar.md) · [🇫🇷 Français](README.fr.md) · [🇪🇸 Español](README.es.md) · [🇵🇰 اردو (Urdu)](README.ur.md) · [🇹🇿 Kiswahili](README.sw.md) · [🇩🇪 Deutsch](README.de.md) · [🇮🇩 Bahasa Indonesia](README.id.md) · [🇮🇳 हिन्दी (Hindi)](README.hi.md) · [🇦🇫 پښتو (Pashto)](README.ps.md) · [🇮🇷 فارسی (Farsi)](README.fa.md) · [🇨🇳 简体中文 (Chinese)](README.zh.md)

## Endpoint

```
GET https://play-api.faizankhichi.me/?q=<query>
```

كل طلب يعيد JSON نظيفًا. المعامل الوحيد المطلوب هو `q`.

## موقع تجريبي

جرّب واجهة برمجة التطبيقات دون كتابة أي كود: **[عرض حي](https://play-api.faizankhichi.me/demo)** — صفحة بحث بسيطة مبنية على هذه الواجهة. ابحث عن التطبيقات، وصفِّ حسب المجاني/المدفوع، وافتح بطاقة لعرض التفاصيل الكاملة ولقطات الشاشة، واضغط **Join Beta** لفتح صفحة برنامج النسخة التجريبية للتطبيق.

العرض التوضيحي هو واجهة عرض: هذا ما يمكنك بناؤه باستخدام هذه الواجهة. الكود موجود في [example-site/](https://github.com/FAK-LABS/playstore-search-api/tree/main/example-site) — ملف `index.html` ثابت واحد بالإضافة إلى ملف README قصير. انسخه أو استضفه في أي مكان أو افتحه كما هو؛ فهو يستدعي الواجهة الحية، دون الحاجة إلى مفتاح.


## البدء السريع

```bash
curl "https://play-api.faizankhichi.me/?q=whatsapp"
```

```js
const res = await fetch("https://play-api.faizankhichi.me/?q=whatsapp&maxResults=5");
const data = await res.json();
data.results.forEach(a => console.log(a.name, a.package, a.score, a.installs));
```

## المعاملات

| المعامل | القيم | الافتراضي | ملاحظات |
|---|---|---|---|
| `q` | نص البحث (بحد أقصى 200 حرف) | — | **مطلوب.** |
| `category` | معرّف فئة متجر Play، مثل `communication` و`game_arcade` و`education` و`tools` و`photography` و`game_puzzle` | — | يستلزم `details=full` (يجلب صفحات التفاصيل). غير حساس لحالة الأحرف؛ الاسم المعروض يعمل أيضًا (`Communication`). |
| `price` | `free` أو `paid` أو `all` | `all` | تُرشَّح بواسطة Play نفسها، فلا يكلّف مكالمات إضافية. |
| `sort` | `relevance` أو `rating` أو `installs` أو `updated` | `relevance` | `installs` و`updated` يستلزمان `details=full`. أما `rating` فيرتّب حسب تقييم النجوم المطبوع على كل بطاقة. |
| `hl` | رمز اللغة، مثل `en` و`ur` و`es` | `en` | لغة واجهة النتائج. |
| `gl` | رمز المنطقة، مثل `US` و`PK` و`ES` | `US` | المنطقة التي تُوطَّن لها النتائج. |
| `maxResults` | 1–30 | `20` | صفحة بحث Play تعرض 30 تطبيقًا كحد أقصى لكل استعلام، لذا النتائج محدودة بـ 30. |
| `details` | `full` أو `basic` | `basic` | `basic` (الافتراضي) يقرأ صفحة بحث واحدة فقط — مكالمة واحدة. `full` يجلب صفحة تفاصيل كل تطبيق (بيانات كاملة). |

الفلاتر تتجمع بحرية — كل المعاملات تعمل معًا:

```bash
# ألعاب آركيد مدفوعة، الأعلى تقييمًا أولًا
curl "https://play-api.faizankhichi.me/?q=minecraft&price=paid&category=game_arcade&sort=rating"

# تطبيقات تواصل مجانية، بالأردية، لباكستان
curl "https://play-api.faizankhichi.me/?q=chat&price=free&category=communication&hl=ur&gl=PK"

# محررات صور الأكثر تثبيتًا، الأحدث تحديثًا أولًا
curl "https://play-api.faizankhichi.me/?q=photo+editor&sort=installs"
```

## تطبيق واحد

ابحث عن تطبيق واحد باسم الحزمة، مع التفاصيل الكاملة — بما في ذلك رابط الانضمام إلى النسخة التجريبية (beta):

```
GET https://play-api.faizankhichi.me/app?id=<package>
```

```bash
curl "https://play-api.faizankhichi.me/app?id=com.whatsapp"
curl "https://play-api.faizankhichi.me/app?id=com.mojang.minecraftpe"
```

الاستجابة هي `{ "success": true, "app": { … } }` — كائن `app` يحمل بالضبط الحقول المذكورة أدناه. حزمة غير معروفة ← `404`.

## الاستجابة

حقول المستوى الأعلى:

| الحقل | النوع | الوصف |
|---|---|---|
| `success` | boolean | `true` عندما يعمل الطلب. |
| `query` | string | الاستعلام كما ورد. |
| `count` | number | عدد النتائج في هذه الاستجابة. |
| `source` | string | `"play-html"` — النتائج تأتي من صفحات ويب Google Play نفسها. |
| `filters` | object | الفلاتر المطبقة، تُعاد: `category` و`price` و`sort` و`hl` و`gl` و`maxResults` و`details`. |
| `results` | array | النتائج (انظر أدناه). |

### مثال على الاستجابة

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

نتائج حقيقية مباشرة، 22 نوفمبر 2026. الأرقام تتغير مع تحديث Play — `ratings` و`installsMax` و`updated` تتغير مع الوقت.

## حقول النتيجة

كل نتيجة (بحث أو تطبيق واحد) تحمل هذه الحقول:

| الحقل | النوع | الوصف |
|---|---|---|
| `package` | string | اسم حزمة التطبيق، مثل `"com.whatsapp"`. |
| `name` | string | اسم التطبيق. |
| `url` | string | رابط صفحة التطبيق في متجر Play. |
| `icon` | string | رابط أيقونة التطبيق (512 بكسل). |
| `headerImage` | string | رابط الصورة الرئيسية (فارغ عندما لا توجد). |
| `developer.name` | string | اسم المطوّر. |
| `developer.id` | string | معرّف المطوّر. |
| `developer.url` | string | صفحة المطوّر في Play. |
| `developer.email` | string | بريد دعم المطوّر (فارغ عندما لا يُذكر). |
| `developer.website` | string | موقع المطوّر (فارغ عندما لا يُذكر). |
| `developer.address` | string | عنوان المطوّر كما هو مذكور (فارغ عندما لا يُذكر). |
| `summary` | string | سطر تعريفي قصير من بطاقة المتجر. |
| `description` | string | الوصف الكامل، نص عادي (أُزيل HTML). |
| `installs` | string | التثبيتات كما تظهر، مثل `"10,000,000,000+"`. |
| `installsMin` | number أو `null` | الحد الأدنى للتثبيتات. |
| `installsMax` | number أو `null` | الحد الأقصى للتثبيتات. |
| `installsShort` | string | التثبيتات المختصرة، مثل `"10B+"`. |
| `score` | number أو `null` | متوسط التقييم، مثل `4.630478`. |
| `scoreText` | string أو `null` | التقييم كما يظهر، مثل `"4.6"`. |
| `ratings` | number أو `null` | العدد الإجمالي للتقييمات. |
| `ratingsText` | string أو `null` | عدد التقييمات كما يظهر، مثل `"248,305,554"`. |
| `reviews` | number أو `null` | عدد المراجعات المكتوبة. |
| `histogram` | object أو `null` | توزيع التقييمات: `{"1": …, "2": …, "3": …, "4": …, "5": …}`. |
| `price` | number أو `null` | السعر بوحدات `currency` (`0` = مجاني). `null` عند عدم المعرفة. |
| `priceText` | string أو `null` | السعر كما يظهر، مثل `"$6.99"` أو `"Free"`. |
| `currency` | string أو `null` | رمز العملة، مثل `"USD"`. |
| `free` | boolean أو `null` | `true` = مجاني، `false` = مدفوع. `null` عند عدم المعرفة. |
| `offersIAP` | boolean أو `null` | `true` عندما يقدم التطبيق مشتريات داخل التطبيق. |
| `iapRange` | string أو `null` | نطاق أسعار المشتريات داخل التطبيق كما يظهر، مثل `"$0.40 - $199.99 per item"`. `null` عندما لا توجد مشتريات. |
| `containsAds` | boolean أو `null` | `true` عندما تقول البطاقة "Contains ads". |
| `genre` | string | اسم الفئة، مثل `"Communication"`. |
| `genreId` | string | معرّف الفئة، مثل `"COMMUNICATION"`. |
| `contentRating` | string | تصنيف المحتوى، مثل `"Everyone"` أو `"Teen"`. |
| `contentRatingDescription` | string | واصفات المحتوى، مثل `"Users Interact, Shares Location"`. |
| `released` | string أو `null` | تاريخ الإصدار كما يظهر، مثل `"Oct 18, 2010"`. `null` عندما لا تذكر Play تاريخًا. |
| `releasedTimestamp` | number أو `null` | تاريخ الإصدار كطابع زمني Unix. |
| `updated` | string أو `null` | تاريخ آخر تحديث كما يظهر، مثل `"Nov 20, 2026"`. |
| `updatedTimestamp` | number أو `null` | تاريخ آخر تحديث كطابع زمني Unix. |
| `version` | string أو `null` | الإصدار الحالي، مثل `"1.26.66.1"`. `null` عندما لا تعرض Play إصدارًا للتطبيق (واتساب أحد هذه التطبيقات). |
| `androidVersion` | string أو `null` | تسمية الحد الأدنى لإصدار أندرويد، مثل `"8.0"`. |
| `androidMinSdk` | number أو `null` | الحد الأدنى لمستوى Android SDK، مثل `26`. |
| `targetSdk` | number أو `null` | مستوى SDK المستهدف، مثل `35`. |
| `size` | دائمًا `null` | حجم التنزيل **غير معروض** في صفحة متجر Play للطلبات المجهولة — المتجر يعرضه فقط داخل نافذة "حول" بعد تسجيل الدخول. الواجهة تعيد `null` بدلًا من التخمين. |
| `editorsChoice` | boolean | `true` عندما يحمل التطبيق شارة Editors' Choice. |
| `chartRank` | object أو `null` | موضع التطبيق في مخطط المتجر، مثل `{"chart": "top free communication", "rank": "#1"}`. `null` عندما لا يكون التطبيق في المخطط. |
| `recentChanges` | string | نص "ما الجديد"، نص عادي (أُزيل HTML). فارغ عندما لا يذكر التطبيق شيئًا. |
| `privacyPolicy` | string أو `null` | رابط سياسة الخصوصية (عند ذكره). |
| `screenshots` | array من strings | روابط صور لقطات الشاشة. |
| `hasTrailer` | boolean | `true` عندما تحمل البطاقة زر إعلان تشويقي (trailer). |
| `video` | دائمًا `null` | لا يوجد رابط فيديو للإعلان التشويقي — الصفحة تحمل زر Trailer فقط، والفيديو يُحمَّل عند النقر. `hasTrailer` هو الإشارة الصادقة. |
| `beta` | object | معلومات برنامج النسخة التجريبية (انظر أدناه). |

### ما الذي كانت تفتقده الواجهة القديمة

كانت playstore-search السابقة تعيد ثلاثة حقول فقط لكل تطبيق — `package` و`name` و`rating`. كل ما تبقى في الجدول أعلاه جديد: الوصف الكامل، المطوّر (الاسم والمعرّف والصفحة والبريد والموقع والعنوان)، التثبيتات (الحد الأدنى / الأقصى / المختصر)، التقييم + عدد التقييمات + المراجعات + توزيع 1–5★، السعر + العملة + مجاني/مدفوع، نطاق المشتريات داخل التطبيق، يحتوي على إعلانات، الفئة، تصنيف المحتوى + الواصفات، تاريخا الإصدار والتحديث، الإصدار، إصدار أندرويد / مستويات SDK، لقطات الشاشة، الصورة الرئيسية، Editors' Choice، موضع المخطط، ما الجديد، سياسة الخصوصية، مؤشر الإعلان التشويقي، ورابط الانضمام إلى النسخة التجريبية.

## برنامج النسخة التجريبية (beta)

كل نتيجة تحمل رابط الانضمام إلى النسخة التجريبية:

```json
"beta": {
  "joinUrl": "https://play.google.com/apps/testing/com.whatsapp",
  "available": null,
  "note": "Beta availability is not verifiable without a Google sign-in — open the link to see whether the program is open, full, or unavailable."
}
```

استخدم `beta.joinUrl` كزر **الانضمام إلى النسخة التجريبية** في تطبيقك أو موقعك — يفتح صفحة النسخة التجريبية في Google Play لذلك التطبيق، حيث يسجّل المستخدم الدخول ويرى ما إذا كان البرنامج مفتوحًا أو ممتلئًا أو غير متاح.

**لماذا `available` هي `null`:** صفحة النسخة التجريبية (`play.google.com/apps/testing/<package>`) تعيد التوجيه إلى تسجيل الدخول في Google لكل التطبيقات — واتساب، الذي لديه نسخة تجريبية عامة، ونتفليكس، التي ليس لديها، يعيدان التوجيه بالطريقة نفسها. لا توجد إشارة في إعادة التوجيه تقول ما إذا كان هناك برنامج تجريبي، لذا الواجهة لا تدّعي وجوده. تعطيك رابط الانضمام الصحيح وتضع التوفر "غير معروف"، بصدق. لا تثق أبدًا بواجهة تخبرك أن نسخة تجريبية "موجودة" دون تسجيل دخول — إنها تخمّن.

## ترقيم الصفحات

لا يوجد رمز للصفحة التالية. بحث ويب Play لا يدعم ترقيمًا مستقرًا — صفحة البحث تعرض 30 تطبيقًا كحد أقصى لكل استعلام، و`maxResults` محدود بـ 30. طلب المزيد يعيد نفس الـ 30.

## details=basic

`details=basic` هو الافتراضي: مكالمة واحدة — صفحة البحث فقط، ولا شيء غيرها. وهو أقل بالتصميم. صفحة البحث تعرض البيانات الكاملة (التقييمات والتثبيتات والسعر والملخص والشارات) للنتيجة **الأولى** فقط؛ النتائج من الثانية فصاعدًا صفوف مدمجة تحمل الاسم والمطوّر وتقييم النجوم والأيقونة فقط. في الوضع basic تعود تلك الحقول `null` بدلًا من تخمينها. كل صف ما زال يحمل تقييم نجومه، لذا يعمل `sort=rating` في الوضع basic، كما أن `price` ترشَّحه Play نفسها دون مكالمات إضافية. صفحات التفاصيل لا تُجلب إلا لغرض: مرّر `details=full` صراحةً، أو اطلب شيئًا يحتاج حقلًا من التفاصيل فقط — `category` (الفئة)، `sort=installs` (عدد التثبيتات)، `sort=updated` (تاريخ التحديث). في تلك الحالات تجلب API صفحات التفاصيل ويكون `details` المعاد في الرد `"full"`.

## الأخطاء

| الحالة | المعنى |
|---|---|
| `400` | `q` مفقود، أو قيمة غير صالحة لـ `category` أو `price` أو `sort` أو `hl` أو `gl` أو `maxResults` أو `details`. الجسم يسمّي المشكلة. |
| `403` | وصول مباشر فقط — نقطة النهاية ترفض الطلبات الممرَّرة عبر خادم أو استضافة مجانية. |
| `404` | مسار غير معروف، أو `/app` مع حزمة غير موجودة. |
| `405` | يُدعم `GET` فقط (و`OPTIONS` لـ CORS). |
| `502` | Google Play لم تجب. أعد المحاولة. |

الاستعلام بلا نتائج يعيد `200` مع `"success": true` و`"results": []` — ليس خطأ.

## ملاحظات

- لا مفتاح API، لا تسجيل دخول، لا ترويسة حد معدل — استخدمه بعقل.
- `installs` و`ratingsText` و`scoreText` و`priceText` و`released` و`updated` هي النصوص التي تعرضها Play، لذا هي نص عرض؛ الحقول الرقمية `Min` / `Max` بجانبها هي الأرقام التي تُرتَّب أو تُقارَن.
- `version` و`androidVersion` و`released` تكون `null` لبعض التطبيقات لأن Play ببساطة لا تعرضها لتلك التطبيقات — `null` تعني "غير مذكور"، لا "مفقود".
- النتائج تأتي من بحث Google Play؛ ما تحصل عليه يعتمد على الاستعلام والمنطقة (`gl`) واللغة (`hl`).
- CORS مفتوح (`Access-Control-Allow-Origin: *`)، لذا نقطة النهاية تعمل من أي موقع أو تطبيق.

## حول

بُنيت بواسطة [FAK LABS](https://faizankhichi.me/) — برمجيات جادة، تعمل في الإنتاج.
