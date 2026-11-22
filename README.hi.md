# Play Store Search API

[FAK LABS](https://faizankhichi.me/) द्वारा Google Play खोज API। Google Play पर ऐप्स खोजें — ऐप की पूरी जानकारी, फ़िल्टर और सॉर्टिंग के साथ। API key की ज़रूरत नहीं।

> **यह रेपो सिर्फ़ दस्तावेज़ों के लिए है।** यह सार्वजनिक endpoint और उसके इस्तेमाल का तरीका बताता है। इसमें कोई कोड नहीं है।

## अपनी भाषा में पढ़ें

- [🇬🇧 English](README.md) · [🇧🇷 Português](README.pt.md) · [🇸🇦 العربية (Arabic)](README.ar.md) · [🇫🇷 Français](README.fr.md) · [🇪🇸 Español](README.es.md) · [🇵🇰 اردو (Urdu)](README.ur.md) · [🇹🇿 Kiswahili](README.sw.md) · [🇩🇪 Deutsch](README.de.md) · [🇮🇩 Bahasa Indonesia](README.id.md) · [🇮🇳 हिन्दी (Hindi)](README.hi.md) · [🇦🇫 پښتو (Pashto)](README.ps.md) · [🇮🇷 فارسی (Farsi)](README.fa.md) · [🇨🇳 简体中文 (Chinese)](README.zh.md)

## Endpoint

```
GET https://play-api.faizankhichi.me/?q=<query>
```

हर अनुरोध साफ़ JSON लौटाता है। एकमात्र ज़रूरी पैरामीटर `q` है।

## त्वरित शुरुआत

```bash
curl "https://play-api.faizankhichi.me/?q=whatsapp"
```

```js
const res = await fetch("https://play-api.faizankhichi.me/?q=whatsapp&maxResults=5");
const data = await res.json();
data.results.forEach(a => console.log(a.name, a.package, a.score, a.installs));
```

## पैरामीटर

| पैरामीटर | मान | डिफ़ॉल्ट | नोट्स |
|---|---|---|---|
| `q` | खोज का टेक्स्ट (अधिकतम 200 अक्षर) | — | **ज़रूरी।** |
| `category` | Play Store की श्रेणी id, जैसे `communication`, `game_arcade`, `education`, `tools`, `photography`, `game_puzzle` | — | `details=full` (डिफ़ॉल्ट) चाहिए। बड़े/छोटे अक्षरों से फ़र्क नहीं पड़ता; दिखने वाला नाम भी चलता है (`Communication`)। |
| `price` | `free`, `paid`, `all` | `all` | |
| `sort` | `relevance`, `rating`, `installs`, `updated` | `relevance` | `updated` के लिए `details=full` (डिफ़ॉल्ट) चाहिए। |
| `hl` | भाषा कोड, जैसे `en`, `ur`, `es` | `en` | नतीजों की इंटरफ़ेस भाषा। |
| `gl` | क्षेत्र कोड, जैसे `US`, `PK`, `ES` | `US` | वह क्षेत्र जिसके लिए नतीजे स्थानीयकृत हैं। |
| `maxResults` | 1–30 | `20` | Play का खोज पृष्ठ हर क्वेरी पर अधिकतम ~30 ऐप्स दिखाता है, इसलिए नतीजे 30 तक सीमित हैं। |
| `details` | `full`, `basic` | `full` | `full` हर ऐप का विवरण पृष्ठ लाता है (पूरा डेटा)। `basic` तेज़ है पर हल्का — नीचे देखें। |

फ़िल्टर स्वतंत्र रूप से मिलते हैं — हर पैरामीटर साथ काम करता है:

```bash
# भुगतान वाले arcade गेम, सबसे ज़्यादा रेटिंग वाले पहले
curl "https://play-api.faizankhichi.me/?q=minecraft&price=paid&category=game_arcade&sort=rating"

# मुफ़्त communication ऐप्स, उर्दू में, पाकिस्तान के लिए
curl "https://play-api.faizankhichi.me/?q=chat&price=free&category=communication&hl=ur&gl=PK"

# सबसे ज़्यादा इंस्टॉल किए गए photo editor, हाल में अपडेट हुए पहले
curl "https://play-api.faizankhichi.me/?q=photo+editor&sort=installs"
```

## एक ऐप

एक ऐप को उसके package नाम से देखें, पूरी जानकारी के साथ — जिसमें उसका beta जॉइन लिंक भी है:

```
GET https://play-api.faizankhichi.me/app?id=<package>
```

```bash
curl "https://play-api.faizankhichi.me/app?id=com.whatsapp"
curl "https://play-api.faizankhichi.me/app?id=com.mojang.minecraftpe"
```

जवाब `{ "success": true, "app": { … } }` होता है — `app` ऑब्जेक्ट में बिल्कुल वही फ़ील्ड हैं जो नीचे सूचीबद्ध हैं। अज्ञात package → `404`।

## जवाब

ऊपरी स्तर के फ़ील्ड:

| फ़ील्ड | प्रकार | विवरण |
|---|---|---|
| `success` | boolean | `true` जब अनुरोध काम करे। |
| `query` | string | क्वेरी जैसी मिली। |
| `count` | number | इस जवाब में नतीजों की संख्या। |
| `source` | string | `"play-html"` — नतीजे Google Play के अपने वेब पृष्ठों से आते हैं। |
| `filters` | object | लगाए गए फ़िल्टर, वापस दिखाए गए: `category`, `price`, `sort`, `hl`, `gl`, `maxResults`, `details`। |
| `results` | array | नतीजे (नीचे देखें)। |

### जवाब का उदाहरण

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

असली लाइव नतीजे, 22 नवंबर 2026। Play के अपडेट के साथ संख्याएँ बदलती हैं — `ratings`, `installsMax` और `updated` समय के साथ बदलते हैं।

## नतीजे के फ़ील्ड

हर नतीजा (खोज या एक ऐप) ये फ़ील्ड रखता है:

| फ़ील्ड | प्रकार | विवरण |
|---|---|---|
| `package` | string | ऐप का package नाम, जैसे `"com.whatsapp"`। |
| `name` | string | ऐप का नाम। |
| `url` | string | ऐप के Play Store पृष्ठ का URL। |
| `icon` | string | ऐप के आइकन का URL (512 px)। |
| `headerImage` | string | फ़ीचर छवि का URL (जब ऐप की न हो तो खाली)। |
| `developer.name` | string | डेवलपर का नाम। |
| `developer.id` | string | डेवलपर की id। |
| `developer.url` | string | Play पर डेवलपर का पृष्ठ। |
| `developer.email` | string | डेवलपर का सपोर्ट ईमेल (जब दर्ज न हो तो खाली)। |
| `developer.website` | string | डेवलपर की वेबसाइट (जब दर्ज न हो तो खाली)। |
| `developer.address` | string | डेवलपर का पता जैसा दर्ज है (जब दर्ज न हो तो खाली)। |
| `summary` | string | स्टोर सूची की छोटी टैगलाइन। |
| `description` | string | पूरा विवरण, सादा टेक्स्ट (HTML हटाया गया)। |
| `installs` | string | इंस्टॉल जैसे दिखाए जाते हैं, जैसे `"10,000,000,000+"`। |
| `installsMin` | number या `null` | न्यूनतम इंस्टॉल। |
| `installsMax` | number या `null` | अधिकतम इंस्टॉल। |
| `installsShort` | string | संक्षिप्त इंस्टॉल, जैसे `"10B+"`। |
| `score` | number या `null` | औसत रेटिंग, जैसे `4.630478`। |
| `scoreText` | string या `null` | रेटिंग जैसे दिखाई जाती है, जैसे `"4.6"`। |
| `ratings` | number या `null` | रेटिंग की कुल संख्या। |
| `ratingsText` | string या `null` | रेटिंग की संख्या जैसे दिखाई जाती है, जैसे `"248,305,554"`। |
| `reviews` | number या `null` | लिखित समीक्षाओं की संख्या। |
| `histogram` | object या `null` | रेटिंग का बंटवारा: `{"1": …, "2": …, "3": …, "4": …, "5": …}`। |
| `price` | number या `null` | `currency` की इकाइयों में कीमत (`0` = मुफ़्त)। अज्ञात हो तो `null`। |
| `priceText` | string या `null` | कीमत जैसे दिखाई जाती है, जैसे `"$6.99"`, `"Free"`। |
| `currency` | string या `null` | मुद्रा कोड, जैसे `"USD"`। |
| `free` | boolean या `null` | `true` = मुफ़्त, `false` = भुगतान वाला। अज्ञात हो तो `null`। |
| `offersIAP` | boolean या `null` | `true` जब ऐप in-app purchases देता हो। |
| `iapRange` | string या `null` | in-app purchases की कीमत की सीमा जैसे दिखाई जाती है, जैसे `"$0.40 - $199.99 per item"`। जब IAP न हो तो `null`। |
| `containsAds` | boolean या `null` | `true` जब सूची में "Contains ads" लिखा हो। |
| `genre` | string | श्रेणी का नाम, जैसे `"Communication"`। |
| `genreId` | string | श्रेणी की id, जैसे `"COMMUNICATION"`। |
| `contentRating` | string | सामग्री रेटिंग, जैसे `"Everyone"`, `"Teen"`। |
| `contentRatingDescription` | string | सामग्री विवरण, जैसे `"Users Interact, Shares Location"`। |
| `released` | string या `null` | रिलीज़ की तारीख़ जैसे दिखाई जाती है, जैसे `"Oct 18, 2010"`। जब Play कोई तारीख़ न दे तो `null`। |
| `releasedTimestamp` | number या `null` | रिलीज़ की तारीख़ Unix timestamp के रूप में। |
| `updated` | string या `null` | आख़िरी अपडेट की तारीख़ जैसे दिखाई जाती है, जैसे `"Nov 20, 2026"`। |
| `updatedTimestamp` | number या `null` | आख़िरी अपडेट की तारीख़ Unix timestamp के रूप में। |
| `version` | string या `null` | मौजूदा वर्शन, जैसे `"1.26.66.1"`। जब Play किसी ऐप का वर्शन न दिखाए तो `null` (WhatsApp ऐसा ही एक ऐप है)। |
| `androidVersion` | string या `null` | न्यूनतम Android वर्शन का लेबल, जैसे `"8.0"`। |
| `androidMinSdk` | number या `null` | न्यूनतम Android SDK लेवल, जैसे `26`। |
| `targetSdk` | number या `null` | टारगेट SDK लेवल, जैसे `35`। |
| `size` | हमेशा `null` | डाउनलोड साइज़ Play Store के पृष्ठ पर गुमनाम अनुरोधों के लिए **दिखाया नहीं जाता** — स्टोर इसे सिर्फ़ साइन-इन के बाद About डायलॉग में दिखाता है। API अंदाज़ा लगाने के बजाय `null` देती है। |
| `editorsChoice` | boolean | `true` जब ऐप पर Editors' Choice बैज हो। |
| `chartRank` | object या `null` | स्टोर चार्ट में स्थान, जैसे `{"chart": "top free communication", "rank": "#1"}`। जब ऐप चार्ट में न हो तो `null`। |
| `recentChanges` | string | "What's new" का टेक्स्ट, सादा टेक्स्ट (HTML हटाया गया)। जब ऐप कुछ न दे तो खाली। |
| `privacyPolicy` | string या `null` | प्राइवेसी पॉलिसी का URL (जब दर्ज हो)। |
| `screenshots` | array of strings | स्क्रीनशॉट छवियों के URL। |
| `hasTrailer` | boolean | `true` जब सूची में trailer बटन हो। |
| `video` | हमेशा `null` | trailer वीडियो का कोई URL नहीं मिलता — पृष्ठ पर सिर्फ़ Trailer बटन होता है, वीडियो क्लिक पर लोड होता है। `hasTrailer` ही सच्चा संकेत है। |
| `beta` | object | Beta प्रोग्राम की जानकारी (नीचे देखें)। |

### पुरानी API में क्या कमी थी

पुरानी playstore-search हर ऐप के सिर्फ़ तीन फ़ील्ड देती थी — `package`, `name`, `rating`। ऊपर की तालिका में बाक़ी सब नया है: पूरा विवरण, डेवलपर (नाम, id, पृष्ठ, ईमेल, वेबसाइट, पता), इंस्टॉल (कम / ज़्यादा / संक्षिप्त), स्कोर + रेटिंग + समीक्षाएँ + 1–5★ बंटवारा, कीमत + मुद्रा + मुफ़्त/भुगतान वाला, IAP सीमा, विज्ञापन, श्रेणी, सामग्री रेटिंग + विवरण, रिलीज़ + अपडेट की तारीख़ें, वर्शन, Android वर्शन / SDK लेवल, स्क्रीनशॉट, हेडर छवि, Editors' Choice, चार्ट स्थान, हाल के बदलाव, प्राइवेसी पॉलिसी, trailer संकेत, और beta में शामिल होने का लिंक।

## Beta प्रोग्राम

हर नतीजे में beta में शामिल होने का लिंक होता है:

```json
"beta": {
  "joinUrl": "https://play.google.com/apps/testing/com.whatsapp",
  "available": null,
  "note": "Beta availability is not verifiable without a Google sign-in — open the link to see whether the program is open, full, or unavailable."
}
```

`beta.joinUrl` को अपनी ऐप या साइट में **Join beta** बटन के रूप में इस्तेमाल करें — यह उस ऐप के लिए Google Play का beta पृष्ठ खोलता है, जहाँ उपयोगकर्ता साइन इन करके देखता है कि प्रोग्राम खुला है, भरा हुआ है, या उपलब्ध नहीं है।

**`available` क्यों `null` है:** beta पृष्ठ (`play.google.com/apps/testing/<package>`) *हर* ऐप के लिए Google साइन-इन पर भेजता है — WhatsApp, जिसका सार्वजनिक beta है, और Netflix, जिसका नहीं है, दोनों एक जैसे redirect होते हैं। redirect में कोई संकेत नहीं होता कि beta प्रोग्राम मौजूद है या नहीं, इसलिए API इसका दावा नहीं करती। यह सही जॉइन लिंक देती है और उपलब्धता को अज्ञात बताती है, ईमानदारी से। कभी ऐसी API पर भरोसा न करें जो साइन-इन के बिना बताए कि beta "मौजूद" है — वह अंदाज़ा लगा रही है।

## पृष्ठांकन

कोई page token नहीं है। Play की वेब खोज में स्थिर पृष्ठांकन नहीं है — खोज पृष्ठ हर क्वेरी पर अधिकतम ~30 ऐप्स दिखाता है, और `maxResults` 30 तक सीमित है। ज़्यादा माँगने पर वही 30 मिलते हैं।

## details=basic

`details=basic` हर ऐप का विवरण पृष्ठ लाना छोड़ देता है, इसलिए यह तेज़ है — पर हल्का। खोज पृष्ठ पूरा मेटाडेटा (रेटिंग, इंस्टॉल, कीमत, सारांश, बैज) सिर्फ़ **पहले** नतीजे के लिए दिखाता है; दूसरे से आगे के नतीजे छोटी पंक्तियाँ होती हैं जिनमें सिर्फ़ नाम, डेवलपर, स्टार रेटिंग और आइकन होता है। basic मोड में वे फ़ील्ड अंदाज़े के बजाय `null` आते हैं। पूरा डेटा चाहिए तो `details=full` (डिफ़ॉल्ट) इस्तेमाल करें।

## त्रुटियाँ

| स्थिति | अर्थ |
|---|---|
| `400` | `q` गायब है, या `category`, `price`, `sort`, `hl`, `gl`, `maxResults` या `details` का ग़लत मान है। जवाब का भाग समस्या बताता है। |
| `403` | सिर्फ़ सीधी पहुँच — endpoint सर्वर या मुफ़्त होस्टिंग के ज़रिए proxy अनुरोधों को मना करता है। |
| `404` | अज्ञात पथ, या `/app` जिसका package मौजूद न हो। |
| `405` | सिर्फ़ `GET` (और CORS के लिए `OPTIONS`) समर्थित है। |
| `502` | Google Play ने जवाब नहीं दिया। फिर से कोशिश करें। |

बिना नतीजों वाला सवाल `200` देता है जिसमें `"success": true` और `"results": []` होता है — यह त्रुटि नहीं है।

## नोट्स

- कोई API key नहीं, कोई साइन-इन नहीं, कोई rate-limit हेडर नहीं — समझदारी से इस्तेमाल करें।
- `installs`, `ratingsText`, `scoreText`, `priceText`, `released` और `updated` वे strings हैं जो Play दिखाता है, इसलिए यह दिखावे का टेक्स्ट है; उनके साथ वाले `Min` / `Max` संख्यात्मक फ़ील्ड वे संख्याएँ हैं जिन पर छाँटना या तुलना करना है।
- `version`, `androidVersion` और `released` कुछ ऐप्स के लिए `null` होते हैं क्योंकि Play उन ऐप्स के लिए उन्हें दिखाता ही नहीं — `null` का मतलब "दर्ज नहीं", "गायब" नहीं।
- नतीजे Google Play की खोज से आते हैं; आपको क्या मिलता है यह क्वेरी, क्षेत्र (`gl`) और भाषा (`hl`) पर निर्भर है।
- CORS खुला है (`Access-Control-Allow-Origin: *`), इसलिए endpoint किसी भी वेबसाइट या ऐप से काम करता है।

## बारे में

[FAK LABS](https://faizankhichi.me/) द्वारा बनाई गई — गंभीर सॉफ़्टवेयर, प्रोडक्शन में चल रहा है।
