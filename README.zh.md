# Play Store Search API

由 [FAK LABS](https://faizankhichi.me/) 提供的 Google Play 搜索 API。在 Google Play 上搜索应用——包含完整的应用详情、筛选和排序。无需 API key。

> **本仓库仅为文档。** 它描述公共 endpoint 及其用法。不包含任何代码。

## 用您的语言阅读

- [🇬🇧 English](README.md) · [🇧🇷 Português](README.pt.md) · [🇸🇦 العربية (Arabic)](README.ar.md) · [🇫🇷 Français](README.fr.md) · [🇪🇸 Español](README.es.md) · [🇵🇰 اردو (Urdu)](README.ur.md) · [🇹🇿 Kiswahili](README.sw.md) · [🇩🇪 Deutsch](README.de.md) · [🇮🇩 Bahasa Indonesia](README.id.md) · [🇮🇳 हिन्दी (Hindi)](README.hi.md) · [🇦🇫 پښتو (Pashto)](README.ps.md) · [🇮🇷 فارسی (Farsi)](README.fa.md) · [🇨🇳 简体中文 (Chinese)](README.zh.md)

## Endpoint

```
GET https://play-api.faizankhichi.me/?q=<query>
```

每次请求都返回干净的 JSON。唯一必填参数是 `q`。

## 演示站点

无需编写代码即可试用 API：**[在线演示](https://play-api.faizankhichi.me/demo)**——一个基于此 API 构建的简单搜索页面。搜索应用、按免费/付费筛选、打开卡片查看完整详情和截图，点击 **Join Beta** 打开该应用的 Beta 计划页面。

这个演示是一个展示：这就是您可以用此 API 构建的东西。代码位于 [example-site/](https://github.com/FAK-LABS/playstore-search-api/tree/main/example-site)——一个静态 `index.html` 文件加一个简短的 README。复制它、托管在任何地方，或直接打开；它直接调用在线 API，无需密钥。


## 快速开始

```bash
curl "https://play-api.faizankhichi.me/?q=whatsapp"
```

```js
const res = await fetch("https://play-api.faizankhichi.me/?q=whatsapp&maxResults=5");
const data = await res.json();
data.results.forEach(a => console.log(a.name, a.package, a.score, a.installs));
```

## 参数

| 参数 | 取值 | 默认值 | 说明 |
|---|---|---|---|
| `q` | 搜索文本（最多 200 个字符） | — | **必填。** |
| `category` | Play Store 类别 id，例如 `communication`、`game_arcade`、`education`、`tools`、`photography`、`game_puzzle` | — | 意味着 `details=full`（会抓取详情页）。不区分大小写；显示名称也可以（`Communication`）。 |
| `price` | `free`、`paid`、`all` | `all` | 由 Play 自行过滤，无需额外调用。 |
| `sort` | `relevance`、`rating`、`installs`、`updated` | `relevance` | `installs` 和 `updated` 意味着 `details=full`。`rating` 按每张卡片上印制的星级评分排序。 |
| `hl` | 语言代码，例如 `en`、`ur`、`es` | `en` | 结果的界面语言。 |
| `gl` | 地区代码，例如 `US`、`PK`、`ES` | `US` | 结果本地化的地区。 |
| `maxResults` | 1–30 | `20` | Play 搜索页面每次查询最多列出约 30 个应用，因此结果上限为 30。 |
| `details` | `full`、`basic` | `basic` | `basic`（默认值）只读取一个搜索页面——1 次调用。`full` 会抓取每个应用的详情页（完整数据）。 |

筛选条件可自由组合——所有参数都可一起使用：

```bash
# 付费街机游戏，评分最高的排在前面
curl "https://play-api.faizankhichi.me/?q=minecraft&price=paid&category=game_arcade&sort=rating"

# 免费通讯应用，乌尔都语，面向巴基斯坦
curl "https://play-api.faizankhichi.me/?q=chat&price=free&category=communication&hl=ur&gl=PK"

# 安装量最多的照片编辑器，最近更新的排在前面
curl "https://play-api.faizankhichi.me/?q=photo+editor&sort=installs"
```

## 单个应用

按包名查询单个应用，包含完整详情——包括其 beta 加入链接：

```
GET https://play-api.faizankhichi.me/app?id=<package>
```

```bash
curl "https://play-api.faizankhichi.me/app?id=com.whatsapp"
curl "https://play-api.faizankhichi.me/app?id=com.mojang.minecraftpe"
```

响应为 `{ "success": true, "app": { … } }`——`app` 对象恰好包含下面列出的字段。未知包名 → `404`。

## 响应

顶层字段：

| 字段 | 类型 | 说明 |
|---|---|---|
| `success` | boolean | 请求成功时为 `true`。 |
| `query` | string | 收到的查询。 |
| `count` | number | 本次响应中的结果数。 |
| `source` | string | `"play-html"`——结果来自 Google Play 自身的网页。 |
| `filters` | object | 已应用的筛选条件，回显：`category`、`price`、`sort`、`hl`、`gl`、`maxResults`、`details`。 |
| `results` | array | 结果（见下文）。 |

### 响应示例

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

真实实时结果，2026 年 11 月 22 日。数字会随 Play 的更新而变化——`ratings`、`installsMax` 和 `updated` 会随时间变化。

## 结果字段

每个结果（搜索或单个应用）都包含以下字段：

| 字段 | 类型 | 说明 |
|---|---|---|
| `package` | string | 应用包名，例如 `"com.whatsapp"`。 |
| `name` | string | 应用名称。 |
| `url` | string | 应用在 Play Store 的页面 URL。 |
| `icon` | string | 应用图标 URL（512 px）。 |
| `headerImage` | string | 特色图片 URL（应用没有时为空）。 |
| `developer.name` | string | 开发者名称。 |
| `developer.id` | string | 开发者 id。 |
| `developer.url` | string | 开发者在 Play 的页面。 |
| `developer.email` | string | 开发者支持邮箱（未列出时为空）。 |
| `developer.website` | string | 开发者网站（未列出时为空）。 |
| `developer.address` | string | 开发者地址（如所列，未列出时为空）。 |
| `summary` | string | 商店列表中的简短标语。 |
| `description` | string | 完整描述，纯文本（已去除 HTML）。 |
| `installs` | string | 显示的安装量，例如 `"10,000,000,000+"`。 |
| `installsMin` | number 或 `null` | 最低安装量。 |
| `installsMax` | number 或 `null` | 最高安装量。 |
| `installsShort` | string | 简写安装量，例如 `"10B+"`。 |
| `score` | number 或 `null` | 平均评分，例如 `4.630478`。 |
| `scoreText` | string 或 `null` | 显示的评分，例如 `"4.6"`。 |
| `ratings` | number 或 `null` | 评分总数。 |
| `ratingsText` | string 或 `null` | 显示的评分数，例如 `"248,305,554"`。 |
| `reviews` | number 或 `null` | 书面评论数。 |
| `histogram` | object 或 `null` | 评分分布：`{"1": …, "2": …, "3": …, "4": …, "5": …}`。 |
| `price` | number 或 `null` | 以 `currency` 为单位的价格（`0` = 免费）。未知时为 `null`。 |
| `priceText` | string 或 `null` | 显示的价格，例如 `"$6.99"`、`"Free"`。 |
| `currency` | string 或 `null` | 货币代码，例如 `"USD"`。 |
| `free` | boolean 或 `null` | `true` = 免费，`false` = 付费。未知时为 `null`。 |
| `offersIAP` | boolean 或 `null` | 应用提供应用内购买时为 `true`。 |
| `iapRange` | string 或 `null` | 显示的应用内购买价格区间，例如 `"$0.40 - $199.99 per item"`。应用没有 IAP 时为 `null`。 |
| `containsAds` | boolean 或 `null` | 列表写明 "Contains ads" 时为 `true`。 |
| `genre` | string | 类别名称，例如 `"Communication"`。 |
| `genreId` | string | 类别 id，例如 `"COMMUNICATION"`。 |
| `contentRating` | string | 内容分级，例如 `"Everyone"`、`"Teen"`。 |
| `contentRatingDescription` | string | 内容描述，例如 `"Users Interact, Shares Location"`。 |
| `released` | string 或 `null` | 显示的发布日期，例如 `"Oct 18, 2010"`。Play 未列出时为 `null`。 |
| `releasedTimestamp` | number 或 `null` | 发布日期的 Unix 时间戳。 |
| `updated` | string 或 `null` | 显示的最近更新日期，例如 `"Nov 20, 2026"`。 |
| `updatedTimestamp` | number 或 `null` | 最近更新日期的 Unix 时间戳。 |
| `version` | string 或 `null` | 当前版本，例如 `"1.26.66.1"`。Play 未为该应用提供版本时为 `null`（WhatsApp 就是这样的应用）。 |
| `androidVersion` | string 或 `null` | 最低 Android 版本标签，例如 `"8.0"`。 |
| `androidMinSdk` | number 或 `null` | 最低 Android SDK 级别，例如 `26`。 |
| `targetSdk` | number 或 `null` | 目标 SDK 级别，例如 `35`。 |
| `size` | 始终为 `null` | 匿名请求的 Play Store 页面**不提供**下载大小——商店仅在登录后的"关于"对话框中显示它。API 返回 `null` 而不是猜测。 |
| `editorsChoice` | boolean | 应用带有 Editors' Choice 徽章时为 `true`。 |
| `chartRank` | object 或 `null` | 商店排行榜位置，例如 `{"chart": "top free communication", "rank": "#1"}`。应用未上榜时为 `null`。 |
| `recentChanges` | string | "更新内容"文本，纯文本（已去除 HTML）。应用未列出时为空。 |
| `privacyPolicy` | string 或 `null` | 隐私政策 URL（列出时）。 |
| `screenshots` | array of strings | 截图图片 URL。 |
| `hasTrailer` | boolean | 列表有预告片按钮时为 `true`。 |
| `video` | 始终为 `null` | 不提供预告片视频 URL——页面只有一个 Trailer 按钮，视频在点击时加载。`hasTrailer` 才是诚实的信号。 |
| `beta` | object | Beta 计划信息（见下文）。 |

### 旧 API 缺少什么

旧的 playstore-search 每个应用只返回三个字段——`package`、`name`、`rating`。上表中其余所有内容都是新增的：完整描述、开发者（名称、id、页面、邮箱、网站、地址）、安装量（最低 / 最高 / 简写）、评分 + 评分数 + 评论数 + 1–5★ 分布、价格 + 货币 + 免费/付费、IAP 价格区间、包含广告、类别、内容分级 + 描述、发布和更新日期、版本、Android 版本 / SDK 级别、截图、特色图片、Editors' Choice、排行榜位置、更新内容、隐私政策、预告片标志，以及 beta 加入链接。

## Beta 计划

每个结果都带有 beta 加入链接：

```json
"beta": {
  "joinUrl": "https://play.google.com/apps/testing/com.whatsapp",
  "available": null,
  "note": "Beta availability is not verifiable without a Google sign-in — open the link to see whether the program is open, full, or unavailable."
}
```

将 `beta.joinUrl` 用作您的应用或网站中的 **加入 Beta** 按钮——它会打开该应用的 Google Play beta 页面，用户登录后可查看该计划是开放、已满还是不可用。

**为什么 `available` 是 `null`：** beta 页面（`play.google.com/apps/testing/<package>`）对*每个*应用都会重定向到 Google 登录——有公开 beta 的 WhatsApp 和没有的 Netflix，重定向方式完全相同。重定向中没有任何信号能说明 beta 计划是否存在，因此 API 不会声称它存在。它提供正确的加入链接，并诚实地将可用性标记为未知。永远不要相信一个无需登录就告诉您 beta"存在"的 API——它在猜测。

## 分页

没有翻页 token。Play 的网页搜索没有稳定的分页——搜索页面每次查询最多列出约 30 个应用，`maxResults` 上限为 30。要求更多也只会返回同样的 30 个。

## details=basic

`details=basic` 是默认值：仅一次调用——仅搜索页面，别无其他。这是特意设计的简略模式。搜索页面仅为**第一个**结果渲染完整元数据（评分、安装量、价格、摘要、徽章）；第 2 个及之后的结果是紧凑行，仅包含名称、开发者、星级评分和图标。在 basic 模式下，这些字段返回 `null` 而不是猜测值。每行仍然带有自己的星级评分，因此 `sort=rating` 在 basic 模式下可用，而 `price` 由 Play 自行过滤，无需额外调用。详情页仅在有明确目的时才抓取：显式传入 `details=full`，或请求需要详情字段的内容——`category`（类别）、`sort=installs`（安装量）、`sort=updated`（更新日期）。在这些情况下，API 会抓取详情页，响应中回显的 `details` 为 `"full"`。

## 错误

| 状态 | 含义 |
|---|---|
| `400` | 缺少 `q`，或 `category`、`price`、`sort`、`hl`、`gl`、`maxResults`、`details` 的值无效。正文会说明问题。 |
| `403` | 仅限直接访问——endpoint 拒绝通过服务器或免费主机代理的请求。 |
| `404` | 未知路径，或 `/app` 的包不存在。 |
| `405` | 仅支持 `GET`（CORS 用 `OPTIONS`）。 |
| `502` | Google Play 未响应。请重试。 |

无结果的查询返回 `200`，其中 `"success": true` 且 `"results": []`——不是错误。

## 说明

- 无 API key、无需登录、无速率限制头——请合理使用。
- `installs`、`ratingsText`、`scoreText`、`priceText`、`released` 和 `updated` 是 Play 显示的字符串，因此是显示文本；旁边的 `Min` / `Max` 数字字段才是用于排序或比较的数字。
- `version`、`androidVersion` 和 `released` 对某些应用为 `null`，因为 Play 根本不为这些应用提供它们——`null` 表示"未列出"，而不是"缺失"。
- 结果来自 Google Play 搜索；您得到什么取决于查询、地区（`gl`）和语言（`hl`）。
- CORS 已开放（`Access-Control-Allow-Origin: *`），因此 endpoint 可从任何网站或应用使用。

## 关于

由 [FAK LABS](https://faizankhichi.me/) 构建——严肃的软件，正在生产环境中运行。
