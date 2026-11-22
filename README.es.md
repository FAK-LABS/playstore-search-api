# Play Store Search API

Una API de búsqueda de Google Play por [FAK LABS](https://faizankhichi.me/). Busca apps en Google Play — con detalles completos de la app, filtros y ordenación. No necesita API key.

> **Este repositorio es solo documentación.** Describe el endpoint público y cómo usarlo. No contiene código.

## Lee en tu idioma

- [🇬🇧 English](README.md) · [🇧🇷 Português](README.pt.md) · [🇸🇦 العربية (Arabic)](README.ar.md) · [🇫🇷 Français](README.fr.md) · [🇪🇸 Español](README.es.md) · [🇵🇰 اردو (Urdu)](README.ur.md) · [🇹🇿 Kiswahili](README.sw.md) · [🇩🇪 Deutsch](README.de.md) · [🇮🇩 Bahasa Indonesia](README.id.md) · [🇮🇳 हिन्दी (Hindi)](README.hi.md) · [🇦🇫 پښتو (Pashto)](README.ps.md) · [🇮🇷 فارسی (Farsi)](README.fa.md) · [🇨🇳 简体中文 (Chinese)](README.zh.md)

## Endpoint

```
GET https://play-api.faizankhichi.me/?q=<query>
```

Cada petición devuelve JSON limpio. El único parámetro obligatorio es `q`.

## Sitio de demostración

Prueba la API sin escribir código: **[demo en vivo](https://play-api.faizankhichi.me/demo)** — una página de búsqueda sencilla construida con esta API. Busca apps, filtra gratis/de pago, abre una tarjeta para ver los detalles completos y las capturas, y toca **Join Beta** para abrir la página del programa beta de la app.

La demo es un escaparate: es lo que puedes construir con esta API. Su código está en [example-site/](https://github.com/FAK-LABS/playstore-search-api/tree/main/example-site) — un solo `index.html` estático más un README corto. Cópialo, alójalo donde quieras o ábrelo tal cual; llama a la API en vivo, sin clave.


## Inicio rápido

```bash
curl "https://play-api.faizankhichi.me/?q=whatsapp"
```

```js
const res = await fetch("https://play-api.faizankhichi.me/?q=whatsapp&maxResults=5");
const data = await res.json();
data.results.forEach(a => console.log(a.name, a.package, a.score, a.installs));
```

## Parámetros

| Parámetro | Valores | Predeterminado | Notas |
|---|---|---|---|
| `q` | texto de búsqueda (máx. 200 caracteres) | — | **Obligatorio.** |
| `category` | id de género de Play Store, ej. `communication`, `game_arcade`, `education`, `tools`, `photography`, `game_puzzle` | — | Necesita `details=full` (el predeterminado). No distingue mayúsculas; el nombre visible también funciona (`Communication`). |
| `price` | `free`, `paid`, `all` | `all` | |
| `sort` | `relevance`, `rating`, `installs`, `updated` | `relevance` | `updated` necesita `details=full` (el predeterminado). |
| `hl` | código de idioma, ej. `en`, `ur`, `es` | `en` | Idioma de la interfaz de los resultados. |
| `gl` | código de región, ej. `US`, `PK`, `ES` | `US` | Región para la que se localizan los resultados. |
| `maxResults` | 1–30 | `20` | La página de búsqueda de Play muestra como máximo ~30 apps por consulta, así que los resultados se limitan a 30. |
| `details` | `full`, `basic` | `full` | `full` obtiene la página de detalles de cada app (datos completos). `basic` es más rápido pero más simple — ver abajo. |

Los filtros se combinan libremente — todos los parámetros funcionan juntos:

```bash
# juegos arcade de pago, mejor valorados primero
curl "https://play-api.faizankhichi.me/?q=minecraft&price=paid&category=game_arcade&sort=rating"

# apps de comunicación gratis, en urdu, para Pakistán
curl "https://play-api.faizankhichi.me/?q=chat&price=free&category=communication&hl=ur&gl=PK"

# editores de fotos más instalados, actualizados recientemente primero
curl "https://play-api.faizankhichi.me/?q=photo+editor&sort=installs"
```

## Una sola app

Consulta una app por su nombre de paquete, con detalles completos — incluido su enlace para unirse a la beta:

```
GET https://play-api.faizankhichi.me/app?id=<package>
```

```bash
curl "https://play-api.faizankhichi.me/app?id=com.whatsapp"
curl "https://play-api.faizankhichi.me/app?id=com.mojang.minecraftpe"
```

La respuesta es `{ "success": true, "app": { … } }` — el objeto `app` tiene exactamente los campos listados abajo. Paquete desconocido → `404`.

## Respuesta

Campos de nivel superior:

| Campo | Tipo | Descripción |
|---|---|---|
| `success` | boolean | `true` cuando la petición funcionó. |
| `query` | string | La consulta tal como se recibió. |
| `count` | number | Número de resultados en esta respuesta. |
| `source` | string | `"play-html"` — los resultados vienen de las propias páginas web de Google Play. |
| `filters` | object | Los filtros aplicados, devueltos: `category`, `price`, `sort`, `hl`, `gl`, `maxResults`, `details`. |
| `results` | array | Los resultados (ver abajo). |

### Ejemplo de respuesta

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

Resultados reales en vivo, 22 de noviembre de 2026. Los números cambian a medida que Play actualiza — `ratings`, `installsMax` y `updated` cambian con el tiempo.

## Campos del resultado

Cada resultado (búsqueda o app única) trae estos campos:

| Campo | Tipo | Descripción |
|---|---|---|
| `package` | string | Nombre de paquete de la app, ej. `"com.whatsapp"`. |
| `name` | string | Nombre de la app. |
| `url` | string | URL de la página de la app en Play Store. |
| `icon` | string | URL del icono de la app (512 px). |
| `headerImage` | string | URL de la imagen destacada (vacía cuando la app no tiene). |
| `developer.name` | string | Nombre del desarrollador. |
| `developer.id` | string | Id del desarrollador. |
| `developer.url` | string | Página del desarrollador en Play. |
| `developer.email` | string | Correo de soporte del desarrollador (vacío cuando no aparece). |
| `developer.website` | string | Sitio web del desarrollador (vacío cuando no aparece). |
| `developer.address` | string | Dirección del desarrollador tal como aparece (vacía cuando no aparece). |
| `summary` | string | Eslogan corto de la ficha de la tienda. |
| `description` | string | Descripción completa, en texto plano (HTML eliminado). |
| `installs` | string | Instalaciones como se muestran, ej. `"10,000,000,000+"`. |
| `installsMin` | number o `null` | Instalaciones mínimas. |
| `installsMax` | number o `null` | Instalaciones máximas. |
| `installsShort` | string | Instalaciones resumidas, ej. `"10B+"`. |
| `score` | number o `null` | Valoración media, ej. `4.630478`. |
| `scoreText` | string o `null` | Valoración como se muestra, ej. `"4.6"`. |
| `ratings` | number o `null` | Número total de valoraciones. |
| `ratingsText` | string o `null` | Número de valoraciones como se muestra, ej. `"248,305,554"`. |
| `reviews` | number o `null` | Número de reseñas escritas. |
| `histogram` | object o `null` | Desglose de valoraciones: `{"1": …, "2": …, "3": …, "4": …, "5": …}`. |
| `price` | number o `null` | Precio en unidades de `currency` (`0` = gratis). `null` cuando se desconoce. |
| `priceText` | string o `null` | Precio como se muestra, ej. `"$6.99"`, `"Free"`. |
| `currency` | string o `null` | Código de moneda, ej. `"USD"`. |
| `free` | boolean o `null` | `true` = gratis, `false` = de pago. `null` cuando se desconoce. |
| `offersIAP` | boolean o `null` | `true` cuando la app ofrece compras dentro de la app. |
| `iapRange` | string o `null` | Rango de precios de las compras dentro de la app como se muestra, ej. `"$0.40 - $199.99 per item"`. `null` cuando la app no tiene IAP. |
| `containsAds` | boolean o `null` | `true` cuando la ficha dice "Contains ads". |
| `genre` | string | Nombre de la categoría, ej. `"Communication"`. |
| `genreId` | string | Id de la categoría, ej. `"COMMUNICATION"`. |
| `contentRating` | string | Clasificación de contenido, ej. `"Everyone"`, `"Teen"`. |
| `contentRatingDescription` | string | Descriptores de contenido, ej. `"Users Interact, Shares Location"`. |
| `released` | string o `null` | Fecha de lanzamiento como se muestra, ej. `"Oct 18, 2010"`. `null` cuando Play no muestra ninguna. |
| `releasedTimestamp` | number o `null` | Fecha de lanzamiento como timestamp Unix. |
| `updated` | string o `null` | Fecha de última actualización como se muestra, ej. `"Nov 20, 2026"`. |
| `updatedTimestamp` | number o `null` | Fecha de última actualización como timestamp Unix. |
| `version` | string o `null` | Versión actual, ej. `"1.26.66.1"`. `null` cuando Play no expone una para la app (WhatsApp es una de esas apps). |
| `androidVersion` | string o `null` | Etiqueta de la versión mínima de Android, ej. `"8.0"`. |
| `androidMinSdk` | number o `null` | Nivel mínimo del SDK de Android, ej. `26`. |
| `targetSdk` | number o `null` | Nivel del SDK objetivo, ej. `35`. |
| `size` | siempre `null` | El tamaño de descarga **no se expone** en la página de Play Store para peticiones anónimas — la tienda solo lo muestra dentro del diálogo Acerca de con sesión iniciada. La API devuelve `null` en lugar de adivinar. |
| `editorsChoice` | boolean | `true` cuando la app lleva la insignia Editors' Choice. |
| `chartRank` | object o `null` | Posición en el ranking de la tienda, ej. `{"chart": "top free communication", "rank": "#1"}`. `null` cuando la app no está en el ranking. |
| `recentChanges` | string | Texto de "Novedades", en texto plano (HTML eliminado). Vacío cuando la app no muestra ninguno. |
| `privacyPolicy` | string o `null` | URL de la política de privacidad (cuando aparece). |
| `screenshots` | array de strings | URLs de las capturas de pantalla. |
| `hasTrailer` | boolean | `true` cuando la ficha tiene un botón de tráiler. |
| `video` | siempre `null` | No se expone ninguna URL de vídeo del tráiler — la página solo trae un botón de Trailer, el vídeo carga al hacer clic. `hasTrailer` es la señal honesta. |
| `beta` | object | Información del programa beta (ver abajo). |

### Lo que le faltaba a la API antigua

La playstore-search anterior devolvía solo tres campos por app — `package`, `name`, `rating`. Todo lo demás de la tabla es nuevo: descripción completa, desarrollador (nombre, id, página, correo, sitio, dirección), instalaciones (mín. / máx. / resumido), valoración + valoraciones + reseñas + histograma de 1–5★, precio + moneda + gratis/de pago, rango de IAP, contiene anuncios, género, clasificación de contenido + descriptores, fechas de lanzamiento y actualización, versión, versión de Android / niveles de SDK, capturas de pantalla, imagen destacada, Editors' Choice, posición en el ranking, novedades, política de privacidad, indicador de tráiler y el enlace para unirse a la beta.

## Programa beta

Cada resultado trae un enlace para unirse a la beta:

```json
"beta": {
  "joinUrl": "https://play.google.com/apps/testing/com.whatsapp",
  "available": null,
  "note": "Beta availability is not verifiable without a Google sign-in — open the link to see whether the program is open, full, or unavailable."
}
```

Usa `beta.joinUrl` como el botón **Unirse a la beta** en tu app o sitio — abre la página beta de Google Play para esa app, donde el usuario inicia sesión y ve si el programa está abierto, lleno o no disponible.

**Por qué `available` es `null`:** la página beta (`play.google.com/apps/testing/<package>`) redirige al inicio de sesión de Google para *todas* las apps — WhatsApp, que tiene beta pública, y Netflix, que no la tiene, redirigen de forma idéntica. No hay ninguna señal en la redirección que diga si existe un programa beta, así que la API no afirma que exista. Te da el enlace correcto y marca la disponibilidad como desconocida, con honestidad. Nunca confíes en una API que te dice que una beta "existe" sin inicio de sesión — está adivinando.

## Paginación

No hay token de página. La búsqueda web de Play no tiene paginación estable — la página de búsqueda muestra como máximo ~30 apps por consulta, y `maxResults` se limita a 30. Pedir más devuelve las mismas 30.

## details=basic

`details=basic` omite la obtención de la página de detalles de cada app, así que es más rápido — pero más simple. La página de búsqueda solo muestra los metadatos completos (valoraciones, instalaciones, precio, resumen, insignias) para el **primer** resultado; los resultados 2+ son filas compactas con solo nombre, desarrollador, valoración en estrellas e icono. En modo basic, esos campos devuelven `null` en lugar de adivinados. Usa `details=full` (el predeterminado) cuando necesites datos completos.

## Errores

| Estado | Significado |
|---|---|
| `400` | Falta `q`, o valor no válido para `category`, `price`, `sort`, `hl`, `gl`, `maxResults` o `details`. El cuerpo dice cuál es el problema. |
| `403` | Solo acceso directo — el endpoint rechaza peticiones hechas por proxy de servidor o alojamiento gratuito. |
| `404` | Ruta desconocida, o `/app` con un paquete que no existe. |
| `405` | Solo se admite `GET` (y `OPTIONS` para CORS). |
| `502` | Google Play no respondió. Reintenta. |

Una consulta sin resultados devuelve `200` con `"success": true` y `"results": []` — no es un error.

## Notas

- Sin API key, sin inicio de sesión, sin cabecera de límite de peticiones — úsalo con sensatez.
- `installs`, `ratingsText`, `scoreText`, `priceText`, `released` y `updated` son las cadenas que muestra Play, así que son texto de visualización; los campos numéricos `Min` / `Max` junto a ellos son los números para ordenar o comparar.
- `version`, `androidVersion` y `released` son `null` para algunas apps porque Play simplemente no los expone para esas apps — `null` significa "no listado", no "falta".
- Los resultados vienen de la búsqueda de Google Play; lo que recibes depende de la consulta, la región (`gl`) y el idioma (`hl`).
- El CORS es abierto (`Access-Control-Allow-Origin: *`), así que el endpoint funciona desde cualquier sitio o app.

## Acerca de

Hecho por [FAK LABS](https://faizankhichi.me/) — software serio, corriendo en producción.
