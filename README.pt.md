# Play Store Search API

Uma API de pesquisa da Google Play por [FAK LABS](https://faizankhichi.me/). Pesquise apps na Google Play — com detalhes completos do app, filtros e ordenação. Não precisa de API key.

> **Este repositório é apenas documentação.** Descreve o endpoint público e como usá-lo. Não contém código.

## Leia no seu idioma

- [🇬🇧 English](README.md) · [🇧🇷 Português](README.pt.md) · [🇸🇦 العربية (Arabic)](README.ar.md) · [🇫🇷 Français](README.fr.md) · [🇪🇸 Español](README.es.md) · [🇵🇰 اردو (Urdu)](README.ur.md) · [🇹🇿 Kiswahili](README.sw.md) · [🇩🇪 Deutsch](README.de.md) · [🇮🇩 Bahasa Indonesia](README.id.md) · [🇮🇳 हिन्दी (Hindi)](README.hi.md) · [🇦🇫 پښتو (Pashto)](README.ps.md) · [🇮🇷 فارسی (Farsi)](README.fa.md) · [🇨🇳 简体中文 (Chinese)](README.zh.md)

## Endpoint

```
GET https://play-api.faizankhichi.me/?q=<query>
```

Cada requisição retorna JSON limpo. O único parâmetro obrigatório é `q`.

## Início rápido

```bash
curl "https://play-api.faizankhichi.me/?q=whatsapp"
```

```js
const res = await fetch("https://play-api.faizankhichi.me/?q=whatsapp&maxResults=5");
const data = await res.json();
data.results.forEach(a => console.log(a.name, a.package, a.score, a.installs));
```

## Parâmetros

| Parâmetro | Valores | Padrão | Notas |
|---|---|---|---|
| `q` | texto da pesquisa (máx. 200 caracteres) | — | **Obrigatório.** |
| `category` | id de gênero da Play Store, ex. `communication`, `game_arcade`, `education`, `tools`, `photography`, `game_puzzle` | — | Precisa de `details=full` (o padrão). Não diferencia maiúsculas de minúsculas; o nome de exibição também funciona (`Communication`). |
| `price` | `free`, `paid`, `all` | `all` | |
| `sort` | `relevance`, `rating`, `installs`, `updated` | `relevance` | `updated` precisa de `details=full` (o padrão). |
| `hl` | código de idioma, ex. `en`, `ur`, `es` | `en` | Idioma da interface dos resultados. |
| `gl` | código de região, ex. `US`, `PK`, `ES` | `US` | Região para a qual os resultados são localizados. |
| `maxResults` | 1–30 | `20` | A página de pesquisa da Play lista no máximo ~30 apps por consulta, então os resultados são limitados a 30. |
| `details` | `full`, `basic` | `full` | `full` busca a página de detalhes de cada app (dados completos). `basic` é mais rápido, mas mais simples — veja abaixo. |

Os filtros combinam livremente — todos os parâmetros funcionam juntos:

```bash
# jogos arcade pagos, melhor avaliados primeiro
curl "https://play-api.faizankhichi.me/?q=minecraft&price=paid&category=game_arcade&sort=rating"

# apps de comunicação grátis, em urdu, para o Paquistão
curl "https://play-api.faizankhichi.me/?q=chat&price=free&category=communication&hl=ur&gl=PK"

# editores de foto mais instalados, atualizados recentemente primeiro
curl "https://play-api.faizankhichi.me/?q=photo+editor&sort=installs"
```

## Um único app

Consulte um app pelo nome do pacote, com detalhes completos — incluindo o link para entrar no beta:

```
GET https://play-api.faizankhichi.me/app?id=<package>
```

```bash
curl "https://play-api.faizankhichi.me/app?id=com.whatsapp"
curl "https://play-api.faizankhichi.me/app?id=com.mojang.minecraftpe"
```

A resposta é `{ "success": true, "app": { … } }` — o objeto `app` tem exatamente os campos listados abaixo. Pacote desconhecido → `404`.

## Resposta

Campos de nível superior:

| Campo | Tipo | Descrição |
|---|---|---|
| `success` | boolean | `true` quando a requisição funcionou. |
| `query` | string | A consulta como recebida. |
| `count` | number | Número de resultados nesta resposta. |
| `source` | string | `"play-html"` — os resultados vêm das próprias páginas web da Google Play. |
| `filters` | object | Os filtros aplicados, devolvidos: `category`, `price`, `sort`, `hl`, `gl`, `maxResults`, `details`. |
| `results` | array | Os resultados (veja abaixo). |

### Exemplo de resposta

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

Resultados reais, ao vivo, 22 de novembro de 2026. Os números mudam conforme a Play atualiza — `ratings`, `installsMax` e `updated` mudam com o tempo.

## Campos do resultado

Cada resultado (pesquisa ou app único) traz estes campos:

| Campo | Tipo | Descrição |
|---|---|---|
| `package` | string | Nome do pacote do app, ex. `"com.whatsapp"`. |
| `name` | string | Nome do app. |
| `url` | string | URL da página do app na Play Store. |
| `icon` | string | URL do ícone do app (512 px). |
| `headerImage` | string | URL da imagem de destaque (vazia quando o app não tem). |
| `developer.name` | string | Nome do desenvolvedor. |
| `developer.id` | string | Id do desenvolvedor. |
| `developer.url` | string | Página do desenvolvedor na Play. |
| `developer.email` | string | E-mail de suporte do desenvolvedor (vazio quando não listado). |
| `developer.website` | string | Site do desenvolvedor (vazio quando não listado). |
| `developer.address` | string | Endereço do desenvolvedor como listado (vazio quando não listado). |
| `summary` | string | Slogan curto da ficha na loja. |
| `description` | string | Descrição completa, em texto simples (HTML removido). |
| `installs` | string | Instalações como exibidas, ex. `"10,000,000,000+"`. |
| `installsMin` | number ou `null` | Instalações mínimas. |
| `installsMax` | number ou `null` | Instalações máximas. |
| `installsShort` | string | Instalações resumidas, ex. `"10B+"`. |
| `score` | number ou `null` | Avaliação média, ex. `4.630478`. |
| `scoreText` | string ou `null` | Avaliação como exibida, ex. `"4.6"`. |
| `ratings` | number ou `null` | Número total de avaliações. |
| `ratingsText` | string ou `null` | Contagem de avaliações como exibida, ex. `"248,305,554"`. |
| `reviews` | number ou `null` | Número de avaliações escritas. |
| `histogram` | object ou `null` | Distribuição das avaliações: `{"1": …, "2": …, "3": …, "4": …, "5": …}`. |
| `price` | number ou `null` | Preço em unidades de `currency` (`0` = grátis). `null` quando desconhecido. |
| `priceText` | string ou `null` | Preço como exibido, ex. `"$6.99"`, `"Free"`. |
| `currency` | string ou `null` | Código da moeda, ex. `"USD"`. |
| `free` | boolean ou `null` | `true` = grátis, `false` = pago. `null` quando desconhecido. |
| `offersIAP` | boolean ou `null` | `true` quando o app oferece compras no app. |
| `iapRange` | string ou `null` | Faixa de preço das compras no app como exibida, ex. `"$0.40 - $199.99 per item"`. `null` quando o app não tem IAP. |
| `containsAds` | boolean ou `null` | `true` quando a ficha diz "Contains ads". |
| `genre` | string | Nome da categoria, ex. `"Communication"`. |
| `genreId` | string | Id da categoria, ex. `"COMMUNICATION"`. |
| `contentRating` | string | Classificação de conteúdo, ex. `"Everyone"`, `"Teen"`. |
| `contentRatingDescription` | string | Descritores de conteúdo, ex. `"Users Interact, Shares Location"`. |
| `released` | string ou `null` | Data de lançamento como exibida, ex. `"Oct 18, 2010"`. `null` quando a Play não lista nenhuma. |
| `releasedTimestamp` | number ou `null` | Data de lançamento como timestamp Unix. |
| `updated` | string ou `null` | Data da última atualização como exibida, ex. `"Nov 20, 2026"`. |
| `updatedTimestamp` | number ou `null` | Data da última atualização como timestamp Unix. |
| `version` | string ou `null` | Versão atual, ex. `"1.26.66.1"`. `null` quando a Play não expõe uma para o app (o WhatsApp é um desses apps). |
| `androidVersion` | string ou `null` | Rótulo da versão mínima do Android, ex. `"8.0"`. |
| `androidMinSdk` | number ou `null` | Nível mínimo do SDK do Android, ex. `26`. |
| `targetSdk` | number ou `null` | Nível do SDK alvo, ex. `35`. |
| `size` | sempre `null` | O tamanho do download **não é exposto** pela página da Play Store para requisições anônimas — a loja só o mostra dentro do diálogo Sobre com login. A API retorna `null` em vez de adivinhar. |
| `editorsChoice` | boolean | `true` quando o app tem o selo Editors' Choice. |
| `chartRank` | object ou `null` | Posição no ranking da loja, ex. `{"chart": "top free communication", "rank": "#1"}`. `null` quando o app não está no ranking. |
| `recentChanges` | string | Texto de "Novidades", em texto simples (HTML removido). Vazio quando o app não lista nenhum. |
| `privacyPolicy` | string ou `null` | URL da política de privacidade (quando listada). |
| `screenshots` | array de strings | URLs das imagens de captura de tela. |
| `hasTrailer` | boolean | `true` quando a ficha tem um botão de trailer. |
| `video` | sempre `null` | Nenhuma URL de vídeo do trailer é exposta — a página só traz um botão de Trailer, o vídeo carrega ao clicar. `hasTrailer` é o sinal honesto. |
| `beta` | object | Informações do programa beta (veja abaixo). |

### O que a API antiga não tinha

A playstore-search anterior retornava apenas três campos por app — `package`, `name`, `rating`. Todo o resto da tabela acima é novo: descrição completa, desenvolvedor (nome, id, página, e-mail, site, endereço), instalações (mín. / máx. / resumido), avaliação + avaliações + resenhas + histograma de 1–5★, preço + moeda + grátis/pago, faixa de IAP, contém anúncios, gênero, classificação de conteúdo + descritores, datas de lançamento e atualização, versão, versão do Android / níveis de SDK, capturas de tela, imagem de destaque, Editors' Choice, posição no ranking, novidades, política de privacidade, indicador de trailer e o link para entrar no beta.

## Programa beta

Cada resultado traz um link para entrar no beta:

```json
"beta": {
  "joinUrl": "https://play.google.com/apps/testing/com.whatsapp",
  "available": null,
  "note": "Beta availability is not verifiable without a Google sign-in — open the link to see whether the program is open, full, or unavailable."
}
```

Use `beta.joinUrl` como o botão **Entrar no beta** no seu app ou site — ele abre a página de beta da Google Play para aquele app, onde o usuário faz login e vê se o programa está aberto, cheio ou indisponível.

**Por que `available` é `null`:** a página do beta (`play.google.com/apps/testing/<package>`) redireciona para o login do Google para *todos* os apps — o WhatsApp, que tem beta público, e a Netflix, que não tem, redirecionam de forma idêntica. Não há nenhum sinal no redirecionamento que diga se existe um programa beta, então a API não afirma que existe. Ela entrega o link correto e marca a disponibilidade como desconhecida, com honestidade. Nunca confie numa API que diz que um beta "existe" sem login — ela está adivinhando.

## Paginação

Não há token de página. A pesquisa web da Play não tem paginação estável — a página de pesquisa lista no máximo ~30 apps por consulta, e `maxResults` é limitado a 30. Pedir mais retorna os mesmos 30.

## details=basic

`details=basic` pula a busca da página de detalhes de cada app, então é mais rápido — mas mais simples. A página de pesquisa só renderiza os metadados completos (avaliações, instalações, preço, resumo, selos) para o **primeiro** resultado; os resultados 2+ são linhas compactas com apenas nome, desenvolvedor, avaliação em estrelas e ícone. No modo basic, esses campos voltam `null` em vez de adivinhados. Use `details=full` (o padrão) sempre que precisar de dados completos.

## Erros

| Status | Significado |
|---|---|
| `400` | Falta o `q`, ou valor inválido para `category`, `price`, `sort`, `hl`, `gl`, `maxResults` ou `details`. O corpo diz qual é o problema. |
| `403` | Acesso direto apenas — o endpoint recusa requisições feitas por proxy de servidor ou hospedagem gratuita. |
| `404` | Caminho desconhecido, ou `/app` com um pacote que não existe. |
| `405` | Somente `GET` (e `OPTIONS` para CORS) é suportado. |
| `502` | A Google Play não respondeu. Tente novamente. |

Uma consulta sem resultados retorna `200` com `"success": true` e `"results": []` — não é um erro.

## Notas

- Sem API key, sem login, sem cabeçalho de limite de taxa — use com bom senso.
- `installs`, `ratingsText`, `scoreText`, `priceText`, `released` e `updated` são as strings que a Play mostra, então são texto de exibição; os campos numéricos `Min` / `Max` ao lado deles são os números para ordenar ou comparar.
- `version`, `androidVersion` e `released` são `null` para alguns apps porque a Play simplesmente não os expõe para esses apps — `null` significa "não listado", não "faltando".
- Os resultados vêm da pesquisa da Google Play; o que você recebe depende da consulta, da região (`gl`) e do idioma (`hl`).
- O CORS é aberto (`Access-Control-Allow-Origin: *`), então o endpoint funciona de qualquer site ou app.

## Sobre

Feito por [FAK LABS](https://faizankhichi.me/) — software sério, rodando em produção.
