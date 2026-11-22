# Play Store Search API

یک API جستجوی Google Play از [FAK LABS](https://faizankhichi.me/). در Google Play برنامه جستجو کنید — با جزئیات کامل برنامه، فیلترها و مرتب‌سازی. بدون نیاز به API key.

> **این مخزن فقط مستندات است.** نقطه پایانی (endpoint) عمومی و نحوه استفاده از آن را توصیف می‌کند. هیچ کدی ندارد.

## به زبان خودتان بخوانید

- [🇬🇧 English](README.md) · [🇧🇷 Português](README.pt.md) · [🇸🇦 العربية (Arabic)](README.ar.md) · [🇫🇷 Français](README.fr.md) · [🇪🇸 Español](README.es.md) · [🇵🇰 اردو (Urdu)](README.ur.md) · [🇹🇿 Kiswahili](README.sw.md) · [🇩🇪 Deutsch](README.de.md) · [🇮🇩 Bahasa Indonesia](README.id.md) · [🇮🇳 हिन्दी (Hindi)](README.hi.md) · [🇦🇫 پښتو (Pashto)](README.ps.md) · [🇮🇷 فارسی (Farsi)](README.fa.md) · [🇨🇳 简体中文 (Chinese)](README.zh.md)

## Endpoint

```
GET https://play-api.faizankhichi.me/?q=<query>
```

هر درخواست JSON تمیز برمی‌گرداند. تنها پارامتر اجباری `q` است.

## سایت نمایشی

بدون نوشتن کد، API را امتحان کنید: **[نمایش زنده](https://play-api.faizankhichi.me/demo)** — یک صفحه جستجوی ساده که با این API ساخته شده است. برنامه‌ها را جستجو کنید، رایگان/پولی را فیلتر کنید، برای جزئیات کامل و تصاویر صفحه، یک کارت را باز کنید و برای باز کردن صفحه برنامه بتای برنامه، **Join Beta** را بزنید.

این نمایش یک ویترین است: این همان چیزی است که می‌توانید با این API بسازید. کد آن در [example-site/](https://github.com/FAK-LABS/playstore-search-api/tree/main/example-site) است — یک فایل استاتیک `index.html` به همراه یک README کوتاه. آن را کپی کنید، هر جا میزبانی کنید یا همان‌طور باز کنید؛ مستقیماً API زنده را فرا می‌خواند، بدون کلید.


## شروع سریع

```bash
curl "https://play-api.faizankhichi.me/?q=whatsapp"
```

```js
const res = await fetch("https://play-api.faizankhichi.me/?q=whatsapp&maxResults=5");
const data = await res.json();
data.results.forEach(a => console.log(a.name, a.package, a.score, a.installs));
```

## پارامترها

| پارامتر | مقدارها | پیش‌فرض | نکته‌ها |
|---|---|---|---|
| `q` | متن جستجو (حداکثر ۲۰۰ کاراکتر) | — | **اجباری.** |
| `category` | شناسه ژانر Play Store، مثل `communication`، `game_arcade`، `education`، `tools`، `photography`، `game_puzzle` | — | به‌معنای `details=full` است (صفحه جزئیات را می‌گیرد). به بزرگی/کوچکی حروف حساس نیست؛ نام نمایشی هم کار می‌کند (`Communication`). |
| `price` | `free`، `paid`، `all` | `all` | خود Play فیلتر می‌کند، پس تماس اضافی ندارد. |
| `sort` | `relevance`، `rating`، `installs`، `updated` | `relevance` | `installs` و `updated` به‌معنای `details=full` (پیش‌فرض) هستند. `rating` بر اساس امتیاز ستاره‌ای چاپ‌شده روی هر کارت مرتب می‌کند. |
| `hl` | کد زبان، مثل `en`، `ur`، `es` | `en` | زبان رابط نتایج. |
| `gl` | کد منطقه، مثل `US`، `PK`، `ES` | `US` | منطقه‌ای که نتایج برای آن بومی‌سازی شده‌اند. |
| `maxResults` | ۱–۳۰ | `20` | صفحه جستجوی Play حداکثر ~۳۰ برنامه برای هر پرس‌وجو فهرست می‌کند، پس نتایج به ۳۰ محدود می‌شوند. |
| `details` | `full`، `basic` | `basic` | `basic` (پیش‌فرض) فقط یک صفحه جستجو را می‌خواند — ۱ تماس. `full` صفحه جزئیات هر برنامه را می‌گیرد (داده کامل). |

فیلترها آزادانه ترکیب می‌شوند — همه پارامترها با هم کار می‌کنند:

```bash
# بازی‌های arcade پولی، با بالاترین امتیاز اول
curl "https://play-api.faizankhichi.me/?q=minecraft&price=paid&category=game_arcade&sort=rating"

# برنامه‌های ارتباطی رایگان، به اردو، برای پاکستان
curl "https://play-api.faizankhichi.me/?q=chat&price=free&category=communication&hl=ur&gl=PK"

# ویرایشگرهای عکس با بیشترین نصب، تازه به‌روزشده‌ها اول
curl "https://play-api.faizankhichi.me/?q=photo+editor&sort=installs"
```

## یک برنامه

یک برنامه را با نام بسته‌اش پیدا کنید، با جزئیات کامل — از جمله لینک پیوستن به بتای آن:

```
GET https://play-api.faizankhichi.me/app?id=<package>
```

```bash
curl "https://play-api.faizankhichi.me/app?id=com.whatsapp"
curl "https://play-api.faizankhichi.me/app?id=com.mojang.minecraftpe"
```

پاسخ `{ "success": true, "app": { … } }` است — شیء `app` دقیقاً همان فیلدهایی را دارد که پایین فهرست شده‌اند. بسته ناشناخته ← `404`.

## پاسخ

فیلدهای سطح بالا:

| فیلد | نوع | توضیح |
|---|---|---|
| `success` | boolean | `true` وقتی درخواست کار کند. |
| `query` | string | پرس‌وجو همان‌طور که دریافت شد. |
| `count` | number | تعداد نتایج در این پاسخ. |
| `source` | string | `"play-html"` — نتایج از خود صفحات وب Google Play می‌آیند. |
| `filters` | object | فیلترهای اعمال‌شده، بازگردانده شده: `category`، `price`، `sort`، `hl`، `gl`، `maxResults`، `details`. |
| `results` | array | نتایج (پایین را ببینید). |

### نمونه پاسخ

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

نتایج واقعی زنده، ۲۲ نوامبر ۲۰۲۶. اعداد با به‌روزرسانی Play تغییر می‌کنند — `ratings`، `installsMax` و `updated` با گذر زمان تغییر می‌کنند.

## فیلدهای نتیجه

هر نتیجه (جستجو یا یک برنامه) این فیلدها را دارد:

| فیلد | نوع | توضیح |
|---|---|---|
| `package` | string | نام بسته برنامه، مثل `"com.whatsapp"`. |
| `name` | string | نام برنامه. |
| `url` | string | URL صفحه برنامه در Play Store. |
| `icon` | string | URL آیکن برنامه (۵۱۲ پیکسل). |
| `headerImage` | string | URL تصویر سربرگ (وقتی برنامه ندارد خالی است). |
| `developer.name` | string | نام توسعه‌دهنده. |
| `developer.id` | string | شناسه توسعه‌دهنده. |
| `developer.url` | string | صفحه توسعه‌دهنده در Play. |
| `developer.email` | string | ایمیل پشتیبانی توسعه‌دهنده (وقتی درج نشده خالی است). |
| `developer.website` | string | وب‌سایت توسعه‌دهنده (وقتی درج نشده خالی است). |
| `developer.address` | string | آدرس توسعه‌دهنده همان‌طور که درج شده (وقتی درج نشده خالی است). |
| `summary` | string | شعار کوتاه از فهرست فروشگاه. |
| `description` | string | توضیح کامل، متن ساده (HTML حذف شده). |
| `installs` | string | نصب‌ها همان‌طور که نمایش داده می‌شوند، مثل `"10,000,000,000+"`. |
| `installsMin` | number یا `null` | حداقل نصب‌ها. |
| `installsMax` | number یا `null` | حداکثر نصب‌ها. |
| `installsShort` | string | نصب‌های خلاصه، مثل `"10B+"`. |
| `score` | number یا `null` | امتیاز میانگین، مثل `4.630478`. |
| `scoreText` | string یا `null` | امتیاز همان‌طور که نمایش داده می‌شود، مثل `"4.6"`. |
| `ratings` | number یا `null` | تعداد کل امتیازها. |
| `ratingsText` | string یا `null` | تعداد امتیازها همان‌طور که نمایش داده می‌شود، مثل `"248,305,554"`. |
| `reviews` | number یا `null` | تعداد نقدهای نوشته‌شده. |
| `histogram` | object یا `null` | تفکیک امتیازها: `{"1": …, "2": …, "3": …, "4": …, "5": …}`. |
| `price` | number یا `null` | قیمت به واحدهای `currency` (`0` = رایگان). وقتی نامعلوم است `null`. |
| `priceText` | string یا `null` | قیمت همان‌طور که نمایش داده می‌شود، مثل `"$6.99"`، `"Free"`. |
| `currency` | string یا `null` | کد ارز، مثل `"USD"`. |
| `free` | boolean یا `null` | `true` = رایگان، `false` = پولی. وقتی نامعلوم است `null`. |
| `offersIAP` | boolean یا `null` | `true` وقتی برنامه خرید درون‌برنامه‌ای ارائه می‌دهد. |
| `iapRange` | string یا `null` | محدوده قیمت خریدهای درون‌برنامه‌ای همان‌طور که نمایش داده می‌شود، مثل `"$0.40 - $199.99 per item"`. وقتی برنامه IAP ندارد `null`. |
| `containsAds` | boolean یا `null` | `true` وقتی فهرست می‌گوید "Contains ads". |
| `genre` | string | نام دسته، مثل `"Communication"`. |
| `genreId` | string | شناسه دسته، مثل `"COMMUNICATION"`. |
| `contentRating` | string | رده‌بندی محتوا، مثل `"Everyone"`، `"Teen"`. |
| `contentRatingDescription` | string | توصیف‌کننده‌های محتوا، مثل `"Users Interact, Shares Location"`. |
| `released` | string یا `null` | تاریخ انتشار همان‌طور که نمایش داده می‌شود، مثل `"Oct 18, 2010"`. وقتی Play تاریخی فهرست نمی‌کند `null`. |
| `releasedTimestamp` | number یا `null` | تاریخ انتشار به‌صورت Unix timestamp. |
| `updated` | string یا `null` | تاریخ آخرین به‌روزرسانی همان‌طور که نمایش داده می‌شود، مثل `"Nov 20, 2026"`. |
| `updatedTimestamp` | number یا `null` | تاریخ آخرین به‌روزرسانی به‌صورت Unix timestamp. |
| `version` | string یا `null` | نسخه کنونی، مثل `"1.26.66.1"`. وقتی Play برای برنامه نسخه‌ای نشان نمی‌دهد `null` (واتس‌اپ یکی از این برنامه‌هاست). |
| `androidVersion` | string یا `null` | برچسب حداقل نسخه اندروید، مثل `"8.0"`. |
| `androidMinSdk` | number یا `null` | حداقل سطح Android SDK، مثل `26`. |
| `targetSdk` | number یا `null` | سطح SDK هدف، مثل `35`. |
| `size` | همیشه `null` | اندازه دانلود در صفحه Play Store برای درخواست‌های ناشناس **نشان داده نمی‌شود** — فروشگاه آن را فقط داخل گفتگوی «درباره» پس از ورود نشان می‌دهد. API به‌جای حدس زدن `null` برمی‌گرداند. |
| `editorsChoice` | boolean | `true` وقتی برنامه نشان Editors' Choice دارد. |
| `chartRank` | object یا `null` | جایگاه در جدول فروشگاه، مثل `{"chart": "top free communication", "rank": "#1"}`. وقتی برنامه در جدول نیست `null`. |
| `recentChanges` | string | متن «تازه‌ها»، متن ساده (HTML حذف شده). وقتی برنامه چیزی فهرست نمی‌کند خالی است. |
| `privacyPolicy` | string یا `null` | URL سیاست حریم خصوصی (وقتی درج شده). |
| `screenshots` | array of strings | URLهای تصاویر اسکرین‌شات. |
| `hasTrailer` | boolean | `true` وقتی فهرست دکمه تریلر دارد. |
| `video` | همیشه `null` | هیچ URL ویدیوی تریلری نشان داده نمی‌شود — صفحه فقط دکمه Trailer دارد، ویدیو با کلیک بارگذاری می‌شود. `hasTrailer` نشانه صادقانه است. |
| `beta` | object | اطلاعات برنامه بتا (پایین را ببینید). |

### API قدیمی چه چیزی نداشت

playstore-search قدیمی برای هر برنامه فقط سه فیلد برمی‌گرداند — `package`، `name`، `rating`. بقیه جدول بالا همه جدید است: توضیح کامل، توسعه‌دهنده (نام، شناسه، صفحه، ایمیل، وب‌سایت، آدرس)، نصب‌ها (حداقل / حداکثر / خلاصه)، امتیاز + امتیازها + نقدها + هیستوگرام ۱–۵★، قیمت + ارز + رایگان/پولی، محدوده IAP، دارای تبلیغات، ژانر، رده‌بندی محتوا + توصیف‌کننده‌ها، تاریخ‌های انتشار و به‌روزرسانی، نسخه، نسخه اندروید / سطح‌های SDK، اسکرین‌شات‌ها، تصویر سربرگ، Editors' Choice، رتبه جدول، تغییرات اخیر، سیاست حریم خصوصی، نشان تریلر، و لینک پیوستن به بتا.

## برنامه بتا

هر نتیجه یک لینک پیوستن به بتا دارد:

```json
"beta": {
  "joinUrl": "https://play.google.com/apps/testing/com.whatsapp",
  "available": null,
  "note": "Beta availability is not verifiable without a Google sign-in — open the link to see whether the program is open, full, or unavailable."
}
```

از `beta.joinUrl` به‌عنوان دکمه **پیوستن به بتا** در برنامه یا سایت خود استفاده کنید — صفحه بتای Google Play را برای آن برنامه باز می‌کند، جایی که کاربر وارد می‌شود و می‌بیند برنامه باز است، پر است یا در دسترس نیست.

**چرا `available` برابر `null` است:** صفحه بتا (`play.google.com/apps/testing/<package>`) برای *هر* برنامه به ورود Google هدایت می‌کند — واتس‌اپ که بتای عمومی دارد و نتفلیکس که ندارد، به یک شکل هدایت می‌شوند. هیچ نشانه‌ای در هدایت نیست که بگوید برنامه بتا وجود دارد یا نه، پس API ادعا نمی‌کند که وجود دارد. لینک صحیح پیوستن را می‌دهد و در دسترس بودن را نامعلوم علامت می‌زند، صادقانه. هرگز به APIای اعتماد نکنید که بدون ورود به شما می‌گوید بتا «وجود دارد» — دارد حدس می‌زند.

## صفحه‌بندی

هیچ توکن صفحه‌ای نیست. جستجوی وب Play صفحه‌بندی پایدار ندارد — صفحه جستجو حداکثر ~۳۰ برنامه برای هر پرس‌وجو فهرست می‌کند و `maxResults` به ۳۰ محدود است. درخواست بیشتر همان ۳۰ را برمی‌گرداند.

## details=basic

`details=basic` پیش‌فرض است: فقط یک تماس — فقط صفحه جستجو، نه چیز دیگر. این عمداً نازک‌تر است. صفحه جستجو فراداده کامل (امتیازها، نصب‌ها، قیمت، خلاصه، نشان‌ها) را فقط برای نتیجه **اول** نمایش می‌دهد؛ نتایج ۲+ ردیف‌های فشرده‌ای هستند که فقط نام، توسعه‌دهنده، امتیاز ستاره‌ای و آیکن دارند. در حالت basic، آن فیلدها به‌جای حدس زدن `null` برمی‌گردند. هر ردیف همچنان امتیاز ستاره‌ای خودش را دارد، پس `sort=rating` در حالت basic کار می‌کند، و `price` را خود Play فیلتر می‌کند، بدون تماس اضافی. صفحات جزئیات فقط با هدف گرفته می‌شوند: `details=full` را صریحاً بفرستید، یا چیزی بخواهید که به فیلد مخصوص جزئیات نیاز دارد — `category` (ژانر)، `sort=installs` (تعداد نصب)، `sort=updated` (تاریخ به‌روزرسانی). در این موارد API صفحات جزئیات را می‌گیرد و `details` برگردانده‌شده در پاسخ `"full"` است.

## خطاها

| وضعیت | معنی |
|---|---|
| `400` | `q` گم شده، یا مقدار نامعتبر برای `category`، `price`، `sort`، `hl`، `gl`، `maxResults` یا `details`. بدنه مشکل را نام می‌برد. |
| `403` | فقط دسترسی مستقیم — نقطه پایانی درخواست‌های پروکسی‌شده از طریق سرور یا میزبانی رایگان را رد می‌کند. |
| `404` | مسیر ناشناخته، یا `/app` با بسته‌ای که وجود ندارد. |
| `405` | فقط `GET` (و `OPTIONS` برای CORS) پشتیبانی می‌شود. |
| `502` | Google Play پاسخ نداد. دوباره تلاش کنید. |

پرس‌وجویی بدون نتیجه `200` با `"success": true` و `"results": []` برمی‌گرداند — خطا نیست.

## نکته‌ها

- بدون API key، بدون ورود، بدون سربرگ محدودیت نرخ — با عقل استفاده کنید.
- `installs`، `ratingsText`، `scoreText`، `priceText`، `released` و `updated` رشته‌هایی هستند که Play نشان می‌دهد، پس متن نمایشی‌اند؛ فیلدهای عددی `Min` / `Max` کنار آن‌ها اعدادی‌اند که باید مرتب یا مقایسه شوند.
- `version`، `androidVersion` و `released` برای بعضی برنامه‌ها `null` هستند چون Play آن‌ها را برای آن برنامه‌ها اصلاً نشان نمی‌دهد — `null` یعنی «فهرست نشده»، نه «گم‌شده».
- نتایج از جستجوی Google Play می‌آیند؛ آنچه می‌گیرید به پرس‌وجو، منطقه (`gl`) و زبان (`hl`) بستگی دارد.
- CORS باز است (`Access-Control-Allow-Origin: *`)، پس نقطه پایانی از هر وب‌سایت یا برنامه‌ای کار می‌کند.

## درباره

ساخته‌شده توسط [FAK LABS](https://faizankhichi.me/) — نرم‌افزار جدی، در حال اجرا در تولید.
