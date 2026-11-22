# Play Store Search API

Une API de recherche Google Play par [FAK LABS](https://faizankhichi.me/). Recherchez des apps sur Google Play — avec les détails complets de l'app, des filtres et du tri. Aucune clé API nécessaire.

> **Ce dépôt est uniquement de la documentation.** Il décrit l'endpoint public et comment l'utiliser. Il ne contient aucun code.

## Lisez dans votre langue

- [🇬🇧 English](README.md) · [🇧🇷 Português](README.pt.md) · [🇸🇦 العربية (Arabic)](README.ar.md) · [🇫🇷 Français](README.fr.md) · [🇪🇸 Español](README.es.md) · [🇵🇰 اردو (Urdu)](README.ur.md) · [🇹🇿 Kiswahili](README.sw.md) · [🇩🇪 Deutsch](README.de.md) · [🇮🇩 Bahasa Indonesia](README.id.md) · [🇮🇳 हिन्दी (Hindi)](README.hi.md) · [🇦🇫 پښتو (Pashto)](README.ps.md) · [🇮🇷 فارسی (Farsi)](README.fa.md) · [🇨🇳 简体中文 (Chinese)](README.zh.md)

## Endpoint

```
GET https://play-api.faizankhichi.me/?q=<query>
```

Chaque requête renvoie du JSON propre. Le seul paramètre obligatoire est `q`.

## Site de démonstration

Essayez l'API sans écrire de code : **[démo en direct](https://play-api.faizankhichi.me/demo)** — une page de recherche simple construite avec cette API. Recherchez des applications, filtrez gratuites/payantes, ouvrez une fiche pour les détails complets et les captures d'écran, et touchez **Join Beta** pour ouvrir la page du programme bêta de l'application.

La démo est une vitrine : c'est ce que vous pouvez construire avec cette API. Son code est dans [example-site/](https://github.com/FAK-LABS/playstore-search-api/tree/main/example-site) — un seul fichier `index.html` statique plus un court README. Copiez-le, hébergez-le partout ou ouvrez-le tel quel ; il appelle l'API en direct, sans clé.


## Démarrage rapide

```bash
curl "https://play-api.faizankhichi.me/?q=whatsapp"
```

```js
const res = await fetch("https://play-api.faizankhichi.me/?q=whatsapp&maxResults=5");
const data = await res.json();
data.results.forEach(a => console.log(a.name, a.package, a.score, a.installs));
```

## Paramètres

| Paramètre | Valeurs | Défaut | Notes |
|---|---|---|---|
| `q` | texte de recherche (200 caractères max) | — | **Obligatoire.** |
| `category` | id de genre du Play Store, ex. `communication`, `game_arcade`, `education`, `tools`, `photography`, `game_puzzle` | — | Nécessite `details=full` (le défaut). Insensible à la casse ; le nom affiché fonctionne aussi (`Communication`). |
| `price` | `free`, `paid`, `all` | `all` | |
| `sort` | `relevance`, `rating`, `installs`, `updated` | `relevance` | `updated` nécessite `details=full` (le défaut). |
| `hl` | code de langue, ex. `en`, `ur`, `es` | `en` | Langue d'interface des résultats. |
| `gl` | code de région, ex. `US`, `PK`, `ES` | `US` | Région pour laquelle les résultats sont localisés. |
| `maxResults` | 1–30 | `20` | La page de recherche Play liste au maximum ~30 apps par requête, donc les résultats sont plafonnés à 30. |
| `details` | `full`, `basic` | `full` | `full` récupère la page de détails de chaque app (données complètes). `basic` est plus rapide mais plus léger — voir ci-dessous. |

Les filtres se combinent librement — tous les paramètres fonctionnent ensemble :

```bash
# jeux d'arcade payants, les mieux notés d'abord
curl "https://play-api.faizankhichi.me/?q=minecraft&price=paid&category=game_arcade&sort=rating"

# apps de communication gratuites, en ourdou, pour le Pakistan
curl "https://play-api.faizankhichi.me/?q=chat&price=free&category=communication&hl=ur&gl=PK"

# éditeurs photo les plus installés, mis à jour récemment d'abord
curl "https://play-api.faizankhichi.me/?q=photo+editor&sort=installs"
```

## Une seule app

Consultez une app par son nom de package, avec les détails complets — y compris son lien pour rejoindre la bêta :

```
GET https://play-api.faizankhichi.me/app?id=<package>
```

```bash
curl "https://play-api.faizankhichi.me/app?id=com.whatsapp"
curl "https://play-api.faizankhichi.me/app?id=com.mojang.minecraftpe"
```

La réponse est `{ "success": true, "app": { … } }` — l'objet `app` contient exactement les champs listés ci-dessous. Package inconnu → `404`.

## Réponse

Champs de premier niveau :

| Champ | Type | Description |
|---|---|---|
| `success` | boolean | `true` quand la requête a fonctionné. |
| `query` | string | La requête telle que reçue. |
| `count` | number | Nombre de résultats dans cette réponse. |
| `source` | string | `"play-html"` — les résultats viennent des propres pages web de Google Play. |
| `filters` | object | Les filtres appliqués, renvoyés : `category`, `price`, `sort`, `hl`, `gl`, `maxResults`, `details`. |
| `results` | array | Les résultats (voir ci-dessous). |

### Exemple de réponse

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

Vrais résultats en direct, 22 novembre 2026. Les chiffres évoluent au fil des mises à jour de Play — `ratings`, `installsMax` et `updated` changent avec le temps.

## Champs du résultat

Chaque résultat (recherche ou app unique) porte ces champs :

| Champ | Type | Description |
|---|---|---|
| `package` | string | Nom de package de l'app, ex. `"com.whatsapp"`. |
| `name` | string | Nom de l'app. |
| `url` | string | URL de la page Play Store de l'app. |
| `icon` | string | URL de l'icône de l'app (512 px). |
| `headerImage` | string | URL de l'image de présentation (vide quand l'app n'en a pas). |
| `developer.name` | string | Nom du développeur. |
| `developer.id` | string | Id du développeur. |
| `developer.url` | string | Page du développeur sur Play. |
| `developer.email` | string | E-mail de support du développeur (vide quand non listé). |
| `developer.website` | string | Site web du développeur (vide quand non listé). |
| `developer.address` | string | Adresse du développeur telle que listée (vide quand non listée). |
| `summary` | string | Slogan court de la fiche du store. |
| `description` | string | Description complète, en texte brut (HTML supprimé). |
| `installs` | string | Installations telles qu'affichées, ex. `"10,000,000,000+"`. |
| `installsMin` | number ou `null` | Installations minimales. |
| `installsMax` | number ou `null` | Installations maximales. |
| `installsShort` | string | Installations abrégées, ex. `"10B+"`. |
| `score` | number ou `null` | Note moyenne, ex. `4.630478`. |
| `scoreText` | string ou `null` | Note telle qu'affichée, ex. `"4.6"`. |
| `ratings` | number ou `null` | Nombre total de notes. |
| `ratingsText` | string ou `null` | Nombre de notes tel qu'affiché, ex. `"248,305,554"`. |
| `reviews` | number ou `null` | Nombre d'avis écrits. |
| `histogram` | object ou `null` | Répartition des notes : `{"1": …, "2": …, "3": …, "4": …, "5": …}`. |
| `price` | number ou `null` | Prix en unités de `currency` (`0` = gratuit). `null` quand inconnu. |
| `priceText` | string ou `null` | Prix tel qu'affiché, ex. `"$6.99"`, `"Free"`. |
| `currency` | string ou `null` | Code de devise, ex. `"USD"`. |
| `free` | boolean ou `null` | `true` = gratuit, `false` = payant. `null` quand inconnu. |
| `offersIAP` | boolean ou `null` | `true` quand l'app propose des achats intégrés. |
| `iapRange` | string ou `null` | Fourchette de prix des achats intégrés telle qu'affichée, ex. `"$0.40 - $199.99 per item"`. `null` quand l'app n'a pas d'achats intégrés. |
| `containsAds` | boolean ou `null` | `true` quand la fiche indique "Contains ads". |
| `genre` | string | Nom de catégorie, ex. `"Communication"`. |
| `genreId` | string | Id de catégorie, ex. `"COMMUNICATION"`. |
| `contentRating` | string | Classification du contenu, ex. `"Everyone"`, `"Teen"`. |
| `contentRatingDescription` | string | Descripteurs de contenu, ex. `"Users Interact, Shares Location"`. |
| `released` | string ou `null` | Date de sortie telle qu'affichée, ex. `"Oct 18, 2010"`. `null` quand Play n'en liste aucune. |
| `releasedTimestamp` | number ou `null` | Date de sortie en timestamp Unix. |
| `updated` | string ou `null` | Date de dernière mise à jour telle qu'affichée, ex. `"Nov 20, 2026"`. |
| `updatedTimestamp` | number ou `null` | Date de dernière mise à jour en timestamp Unix. |
| `version` | string ou `null` | Version actuelle, ex. `"1.26.66.1"`. `null` quand Play n'en expose pas pour l'app (WhatsApp est l'une de ces apps). |
| `androidVersion` | string ou `null` | Libellé de la version Android minimale, ex. `"8.0"`. |
| `androidMinSdk` | number ou `null` | Niveau SDK Android minimal, ex. `26`. |
| `targetSdk` | number ou `null` | Niveau SDK cible, ex. `35`. |
| `size` | toujours `null` | La taille de téléchargement **n'est pas exposée** par la page Play Store pour les requêtes anonymes — le store ne l'affiche que dans le dialogue À propos une fois connecté. L'API renvoie `null` plutôt que de deviner. |
| `editorsChoice` | boolean | `true` quand l'app porte le badge Editors' Choice. |
| `chartRank` | object ou `null` | Position au classement du store, ex. `{"chart": "top free communication", "rank": "#1"}`. `null` quand l'app n'est pas classée. |
| `recentChanges` | string | Texte « Nouveautés », en texte brut (HTML supprimé). Vide quand l'app n'en liste aucun. |
| `privacyPolicy` | string ou `null` | URL de la politique de confidentialité (quand listée). |
| `screenshots` | array de strings | URLs des captures d'écran. |
| `hasTrailer` | boolean | `true` quand la fiche a un bouton de bande-annonce. |
| `video` | toujours `null` | Aucune URL de vidéo de bande-annonce n'est exposée — la page ne porte qu'un bouton Trailer, la vidéo se charge au clic. `hasTrailer` est le signal honnête. |
| `beta` | object | Infos du programme bêta (voir ci-dessous). |

### Ce que l'ancienne API n'avait pas

L'ancienne playstore-search ne renvoyait que trois champs par app — `package`, `name`, `rating`. Tout le reste du tableau ci-dessus est nouveau : description complète, développeur (nom, id, page, e-mail, site, adresse), installations (min / max / abrégé), note + nombre de notes + avis + histogramme 1–5★, prix + devise + gratuit/payant, fourchette d'achats intégrés, contient des pubs, genre, classification du contenu + descripteurs, dates de sortie et de mise à jour, version, version Android / niveaux SDK, captures d'écran, image de présentation, Editors' Choice, position au classement, nouveautés, politique de confidentialité, indicateur de bande-annonce et lien pour rejoindre la bêta.

## Programme bêta

Chaque résultat porte un lien pour rejoindre la bêta :

```json
"beta": {
  "joinUrl": "https://play.google.com/apps/testing/com.whatsapp",
  "available": null,
  "note": "Beta availability is not verifiable without a Google sign-in — open the link to see whether the program is open, full, or unavailable."
}
```

Utilisez `beta.joinUrl` comme bouton **Rejoindre la bêta** dans votre app ou votre site — il ouvre la page bêta de Google Play pour cette app, où l'utilisateur se connecte et voit si le programme est ouvert, complet ou indisponible.

**Pourquoi `available` est `null` :** la page bêta (`play.google.com/apps/testing/<package>`) redirige vers une connexion Google pour *toutes* les apps — WhatsApp, qui a une bêta publique, et Netflix, qui n'en a pas, redirigent de façon identique. Aucun signal dans la redirection ne dit si un programme bêta existe, donc l'API n'affirme pas qu'il existe. Elle donne le bon lien et marque la disponibilité comme inconnue, honnêtement. Ne faites jamais confiance à une API qui vous dit qu'une bêta « existe » sans connexion — elle devine.

## Pagination

Il n'y a pas de token de page. La recherche web de Play n'a pas de pagination stable — la page de recherche liste au maximum ~30 apps par requête, et `maxResults` est plafonné à 30. En demander plus renvoie les mêmes 30.

## details=basic

`details=basic` saute la récupération de la page de détails de chaque app, donc c'est plus rapide — mais plus léger. La page de recherche ne rend les métadonnées complètes (notes, installations, prix, résumé, badges) que pour le **premier** résultat ; les résultats 2+ sont des lignes compactes avec seulement le nom, le développeur, la note en étoiles et l'icône. En mode basic, ces champs reviennent `null` plutôt que devinés. Utilisez `details=full` (le défaut) quand vous avez besoin de données complètes.

## Erreurs

| Statut | Signification |
|---|---|
| `400` | `q` manquant, ou valeur invalide pour `category`, `price`, `sort`, `hl`, `gl`, `maxResults` ou `details`. Le corps nomme le problème. |
| `403` | Accès direct uniquement — l'endpoint refuse les requêtes proxifiées via un serveur ou un hébergement gratuit. |
| `404` | Chemin inconnu, ou `/app` avec un package qui n'existe pas. |
| `405` | Seul `GET` (et `OPTIONS` pour CORS) est supporté. |
| `502` | Google Play n'a pas répondu. Réessayez. |

Une requête sans résultat renvoie `200` avec `"success": true` et `"results": []` — ce n'est pas une erreur.

## Notes

- Pas de clé API, pas de connexion, pas d'en-tête de limite de débit — utilisez-le raisonnablement.
- `installs`, `ratingsText`, `scoreText`, `priceText`, `released` et `updated` sont les chaînes affichées par Play, donc du texte d'affichage ; les champs numériques `Min` / `Max` à côté sont les nombres à trier ou comparer.
- `version`, `androidVersion` et `released` sont `null` pour certaines apps parce que Play ne les expose tout simplement pas pour ces apps — `null` signifie « non listé », pas « manquant ».
- Les résultats viennent de la recherche Google Play ; ce que vous obtenez dépend de la requête, de la région (`gl`) et de la langue (`hl`).
- Le CORS est ouvert (`Access-Control-Allow-Origin: *`), donc l'endpoint fonctionne depuis n'importe quel site ou app.

## À propos

Construit par [FAK LABS](https://faizankhichi.me/) — du logiciel sérieux, en production.
