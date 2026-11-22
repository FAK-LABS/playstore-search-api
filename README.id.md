# Play Store Search API

API pencarian Google Play dari [FAK LABS](https://faizankhichi.me/). Cari aplikasi di Google Play — dengan detail aplikasi lengkap, filter, dan pengurutan. Tidak perlu API key.

> **Repo ini hanya dokumentasi.** Repo ini menjelaskan endpoint publik dan cara menggunakannya. Repo ini tidak berisi kode.

## Baca dalam bahasa Anda

- [🇬🇧 English](README.md) · [🇧🇷 Português](README.pt.md) · [🇸🇦 العربية (Arabic)](README.ar.md) · [🇫🇷 Français](README.fr.md) · [🇪🇸 Español](README.es.md) · [🇵🇰 اردو (Urdu)](README.ur.md) · [🇹🇿 Kiswahili](README.sw.md) · [🇩🇪 Deutsch](README.de.md) · [🇮🇩 Bahasa Indonesia](README.id.md) · [🇮🇳 हिन्दी (Hindi)](README.hi.md) · [🇦🇫 پښتو (Pashto)](README.ps.md) · [🇮🇷 فارسی (Farsi)](README.fa.md) · [🇨🇳 简体中文 (Chinese)](README.zh.md)

## Endpoint

```
GET https://play-api.faizankhichi.me/?q=<query>
```

Setiap permintaan mengembalikan JSON yang bersih. Satu-satunya parameter wajib adalah `q`.

## Situs demo

Coba API tanpa menulis kode: **[demo langsung](https://play-api.faizankhichi.me/demo)** — halaman pencarian sederhana yang dibangun dengan API ini. Cari aplikasi, filter gratis/berbayar, buka kartu untuk detail lengkap dan tangkapan layar, lalu ketuk **Join Beta** untuk membuka halaman program beta aplikasi.

Demo ini adalah etalase: inilah yang bisa Anda bangun dengan API ini. Kodenya ada di [example-site/](https://github.com/FAK-LABS/playstore-search-api/tree/main/example-site) — satu `index.html` statis plus README singkat. Salin, hosting di mana saja, atau buka apa adanya; ia memanggil API langsung, tanpa kunci.


## Mulai cepat

```bash
curl "https://play-api.faizankhichi.me/?q=whatsapp"
```

```js
const res = await fetch("https://play-api.faizankhichi.me/?q=whatsapp&maxResults=5");
const data = await res.json();
data.results.forEach(a => console.log(a.name, a.package, a.score, a.installs));
```

## Parameter

| Parameter | Nilai | Default | Catatan |
|---|---|---|---|
| `q` | teks pencarian (maks. 200 karakter) | — | **Wajib.** |
| `category` | id genre Play Store, mis. `communication`, `game_arcade`, `education`, `tools`, `photography`, `game_puzzle` | — | Perlu `details=full` (default). Tidak peka huruf besar/kecil; nama tampilan juga bisa (`Communication`). |
| `price` | `free`, `paid`, `all` | `all` | |
| `sort` | `relevance`, `rating`, `installs`, `updated` | `relevance` | `updated` perlu `details=full` (default). |
| `hl` | kode bahasa, mis. `en`, `ur`, `es` | `en` | Bahasa antarmuka hasil. |
| `gl` | kode wilayah, mis. `US`, `PK`, `ES` | `US` | Wilayah tempat hasil dilokalkan. |
| `maxResults` | 1–30 | `20` | Halaman pencarian Play menampilkan paling banyak ~30 aplikasi per kueri, jadi hasil dibatasi 30. |
| `details` | `full`, `basic` | `full` | `full` mengambil halaman detail setiap aplikasi (data lengkap). `basic` lebih cepat tapi lebih tipis — lihat di bawah. |

Filter dapat digabung bebas — semua parameter bekerja bersama:

```bash
# game arcade berbayar, rating tertinggi dulu
curl "https://play-api.faizankhichi.me/?q=minecraft&price=paid&category=game_arcade&sort=rating"

# aplikasi komunikasi gratis, dalam bahasa Urdu, untuk Pakistan
curl "https://play-api.faizankhichi.me/?q=chat&price=free&category=communication&hl=ur&gl=PK"

# editor foto paling banyak diinstal, yang baru diperbarui dulu
curl "https://play-api.faizankhichi.me/?q=photo+editor&sort=installs"
```

## Satu aplikasi

Cari satu aplikasi berdasarkan nama paketnya, dengan detail lengkap — termasuk tautan bergabung beta-nya:

```
GET https://play-api.faizankhichi.me/app?id=<package>
```

```bash
curl "https://play-api.faizankhichi.me/app?id=com.whatsapp"
curl "https://play-api.faizankhichi.me/app?id=com.mojang.minecraftpe"
```

Responsnya adalah `{ "success": true, "app": { … } }` — objek `app` memiliki persis field yang tercantum di bawah. Paket tidak dikenal → `404`.

## Respons

Field tingkat atas:

| Field | Tipe | Deskripsi |
|---|---|---|
| `success` | boolean | `true` saat permintaan berhasil. |
| `query` | string | Kueri sebagaimana diterima. |
| `count` | number | Jumlah hasil dalam respons ini. |
| `source` | string | `"play-html"` — hasil berasal dari halaman web Google Play sendiri. |
| `filters` | object | Filter yang diterapkan, dikembalikan: `category`, `price`, `sort`, `hl`, `gl`, `maxResults`, `details`. |
| `results` | array | Hasil (lihat di bawah). |

### Contoh respons

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

Hasil nyata langsung, 22 November 2026. Angka berubah seiring Play memperbarui — `ratings`, `installsMax`, dan `updated` berubah seiring waktu.

## Field hasil

Setiap hasil (pencarian atau satu aplikasi) memiliki field ini:

| Field | Tipe | Deskripsi |
|---|---|---|
| `package` | string | Nama paket aplikasi, mis. `"com.whatsapp"`. |
| `name` | string | Nama aplikasi. |
| `url` | string | URL halaman aplikasi di Play Store. |
| `icon` | string | URL ikon aplikasi (512 px). |
| `headerImage` | string | URL gambar utama (kosong jika aplikasi tidak punya). |
| `developer.name` | string | Nama developer. |
| `developer.id` | string | Id developer. |
| `developer.url` | string | Halaman developer di Play. |
| `developer.email` | string | Email dukungan developer (kosong jika tidak tercantum). |
| `developer.website` | string | Situs web developer (kosong jika tidak tercantum). |
| `developer.address` | string | Alamat developer seperti tercantum (kosong jika tidak tercantum). |
| `summary` | string | Slogan singkat dari listing toko. |
| `description` | string | Deskripsi lengkap, teks biasa (HTML dihapus). |
| `installs` | string | Instalasi seperti ditampilkan, mis. `"10,000,000,000+"`. |
| `installsMin` | number atau `null` | Instalasi minimum. |
| `installsMax` | number atau `null` | Instalasi maksimum. |
| `installsShort` | string | Instalasi singkat, mis. `"10B+"`. |
| `score` | number atau `null` | Rating rata-rata, mis. `4.630478`. |
| `scoreText` | string atau `null` | Rating seperti ditampilkan, mis. `"4.6"`. |
| `ratings` | number atau `null` | Jumlah total rating. |
| `ratingsText` | string atau `null` | Jumlah rating seperti ditampilkan, mis. `"248,305,554"`. |
| `reviews` | number atau `null` | Jumlah ulasan tertulis. |
| `histogram` | object atau `null` | Rincian rating: `{"1": …, "2": …, "3": …, "4": …, "5": …}`. |
| `price` | number atau `null` | Harga dalam satuan `currency` (`0` = gratis). `null` jika tidak diketahui. |
| `priceText` | string atau `null` | Harga seperti ditampilkan, mis. `"$6.99"`, `"Free"`. |
| `currency` | string atau `null` | Kode mata uang, mis. `"USD"`. |
| `free` | boolean atau `null` | `true` = gratis, `false` = berbayar. `null` jika tidak diketahui. |
| `offersIAP` | boolean atau `null` | `true` jika aplikasi menawarkan pembelian dalam aplikasi. |
| `iapRange` | string atau `null` | Kisaran harga pembelian dalam aplikasi seperti ditampilkan, mis. `"$0.40 - $199.99 per item"`. `null` jika aplikasi tidak punya IAP. |
| `containsAds` | boolean atau `null` | `true` jika listing menyebut "Contains ads". |
| `genre` | string | Nama kategori, mis. `"Communication"`. |
| `genreId` | string | Id kategori, mis. `"COMMUNICATION"`. |
| `contentRating` | string | Rating konten, mis. `"Everyone"`, `"Teen"`. |
| `contentRatingDescription` | string | Deskripsi konten, mis. `"Users Interact, Shares Location"`. |
| `released` | string atau `null` | Tanggal rilis seperti ditampilkan, mis. `"Oct 18, 2010"`. `null` jika Play tidak mencantumkan tanggal. |
| `releasedTimestamp` | number atau `null` | Tanggal rilis sebagai timestamp Unix. |
| `updated` | string atau `null` | Tanggal pembaruan terakhir seperti ditampilkan, mis. `"Nov 20, 2026"`. |
| `updatedTimestamp` | number atau `null` | Tanggal pembaruan terakhir sebagai timestamp Unix. |
| `version` | string atau `null` | Versi saat ini, mis. `"1.26.66.1"`. `null` jika Play tidak menampilkan versi untuk aplikasi itu (WhatsApp adalah salah satu aplikasi itu). |
| `androidVersion` | string atau `null` | Label versi Android minimum, mis. `"8.0"`. |
| `androidMinSdk` | number atau `null` | Level SDK Android minimum, mis. `26`. |
| `targetSdk` | number atau `null` | Level SDK target, mis. `35`. |
| `size` | selalu `null` | Ukuran unduhan **tidak ditampilkan** di halaman Play Store untuk permintaan anonim — toko hanya menampilkannya di dalam dialog Tentang setelah masuk. API mengembalikan `null` daripada menebak. |
| `editorsChoice` | boolean | `true` jika aplikasi membawa lencana Editors' Choice. |
| `chartRank` | object atau `null` | Posisi di chart toko, mis. `{"chart": "top free communication", "rank": "#1"}`. `null` jika aplikasi tidak ada di chart. |
| `recentChanges` | string | Teks "Yang baru", teks biasa (HTML dihapus). Kosong jika aplikasi tidak mencantumkan apa pun. |
| `privacyPolicy` | string atau `null` | URL kebijakan privasi (jika tercantum). |
| `screenshots` | array of strings | URL gambar tangkapan layar. |
| `hasTrailer` | boolean | `true` jika listing memiliki tombol trailer. |
| `video` | selalu `null` | Tidak ada URL video trailer yang ditampilkan — halaman hanya membawa tombol Trailer, video dimuat saat diklik. `hasTrailer` adalah sinyal yang jujur. |
| `beta` | object | Info program beta (lihat di bawah). |

### Apa yang tidak dimiliki API lama

Playstore-search lama hanya mengembalikan tiga field per aplikasi — `package`, `name`, `rating`. Semua hal lain dalam tabel di atas adalah baru: deskripsi lengkap, developer (nama, id, halaman, email, situs, alamat), instalasi (min / maks / singkat), skor + rating + ulasan + histogram 1–5★, harga + mata uang + gratis/berbayar, kisaran IAP, mengandung iklan, genre, rating konten + deskripsi, tanggal rilis dan pembaruan, versi, versi Android / level SDK, tangkapan layar, gambar utama, Editors' Choice, peringkat chart, perubahan terbaru, kebijakan privasi, indikator trailer, dan tautan bergabung beta.

## Program beta

Setiap hasil membawa tautan bergabung beta:

```json
"beta": {
  "joinUrl": "https://play.google.com/apps/testing/com.whatsapp",
  "available": null,
  "note": "Beta availability is not verifiable without a Google sign-in — open the link to see whether the program is open, full, or unavailable."
}
```

Gunakan `beta.joinUrl` sebagai tombol **Gabung beta** di aplikasi atau situs Anda — tautan itu membuka halaman beta Google Play untuk aplikasi itu, tempat pengguna masuk dan melihat apakah program terbuka, penuh, atau tidak tersedia.

**Mengapa `available` adalah `null`:** halaman beta (`play.google.com/apps/testing/<package>`) mengarahkan ke login Google untuk *setiap* aplikasi — WhatsApp, yang punya beta publik, dan Netflix, yang tidak punya, dialihkan dengan cara yang sama. Tidak ada sinyal dalam pengalihan itu yang mengatakan apakah ada program beta, jadi API tidak mengklaim ada. API memberi Anda tautan bergabung yang benar dan menandai ketersediaan sebagai tidak diketahui, dengan jujur. Jangan pernah percaya API yang memberi tahu Anda bahwa beta "ada" tanpa masuk — API itu menebak.

## Pagination

Tidak ada token halaman. Pencarian web Play tidak memiliki pagination yang stabil — halaman pencarian menampilkan paling banyak ~30 aplikasi per kueri, dan `maxResults` dibatasi 30. Meminta lebih banyak mengembalikan 30 yang sama.

## details=basic

`details=basic` melewati pengambilan halaman detail setiap aplikasi, jadi lebih cepat — tapi lebih tipis. Halaman pencarian hanya menampilkan metadata lengkap (rating, instalasi, harga, ringkasan, lencana) untuk hasil **pertama**; hasil ke-2 dan seterusnya adalah baris ringkas yang hanya membawa nama, developer, rating bintang, dan ikon. Dalam mode basic, field itu kembali `null` daripada ditebak. Gunakan `details=full` (default) saat Anda membutuhkan data lengkap.

## Error

| Status | Arti |
|---|---|
| `400` | `q` hilang, atau nilai tidak valid untuk `category`, `price`, `sort`, `hl`, `gl`, `maxResults`, atau `details`. Badan respons menyebutkan masalahnya. |
| `403` | Hanya akses langsung — endpoint menolak permintaan yang di-proxy melalui server atau hosting gratis. |
| `404` | Jalur tidak dikenal, atau `/app` dengan paket yang tidak ada. |
| `405` | Hanya `GET` (dan `OPTIONS` untuk CORS) yang didukung. |
| `502` | Google Play tidak menjawab. Coba lagi. |

Kueri tanpa hasil mengembalikan `200` dengan `"success": true` dan `"results": []` — bukan error.

## Catatan

- Tanpa API key, tanpa masuk, tanpa header batas laju — gunakan dengan bijak.
- `installs`, `ratingsText`, `scoreText`, `priceText`, `released`, dan `updated` adalah string yang ditampilkan Play, jadi itu teks tampilan; field numerik `Min` / `Max` di sebelahnya adalah angka untuk diurutkan atau dibandingkan.
- `version`, `androidVersion`, dan `released` adalah `null` untuk beberapa aplikasi karena Play memang tidak menampilkannya untuk aplikasi itu — `null` berarti "tidak tercantum", bukan "hilang".
- Hasil berasal dari pencarian Google Play; apa yang Anda dapatkan tergantung pada kueri, wilayah (`gl`), dan bahasa (`hl`).
- CORS terbuka (`Access-Control-Allow-Origin: *`), jadi endpoint bekerja dari situs web atau aplikasi mana pun.

## Tentang

Dibuat oleh [FAK LABS](https://faizankhichi.me/) — perangkat lunak serius, berjalan di produksi.
