---
title: API - Azure kognitív szolgáltatások |} Microsoft Docs
description: A teljes és felhasználóbarát API útmutató Azure egyéni döntési szolgáltatás, a felhő alapú alkalmazásprogramozási Felületet, amely az révén élesíti környezetfüggő döntéshozatali.
services: cognitive-services
author: slivkins
manager: slivkins
ms.service: cognitive-services
ms.topic: article
ms.date: 05/11/2018
ms.author: slivkins
ms.reviewer: marcozo, alekh
ms.openlocfilehash: 403b17e33394016a07a7b33ba1bcbfe6afdcc05b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349251"
---
# <a name="api"></a>API

Azure egyéni döntési szolgáltatás biztosít két API-k, amelyek az egyes döntési nevezzük: a [prioritás API](#ranking-api) műveletek prioritása a felhasználótól, és a [ellenszolgáltatás API](#reward-api) kimeneti a ellenszolgáltatás. Emellett biztosítanak egy [művelet beállítása API](#action-set-api-customer-provided) Azure egyéni döntési Service műveletek megadásához. Ez a cikk ismerteti ezen három API-k. A jellemző forgatókönyv segítségével alatt megjelenítése, ha egyéni döntési szolgáltatás optimalizálja a cikkek rangsorolási.

## <a name="ranking-api"></a>API prioritása

A rangsorolási API által használt szabványos [JSONP](https://en.wikipedia.org/wiki/JSONP)-stílusú kommunikáció mintát optimalizálhatja a késés és a bypass a [azonos eredetű házirend](https://en.wikipedia.org/wiki/Same-origin_policy). Az utóbbi tiltja, hogy a JavaScript a lap származási kívül az adatok beolvasása.

Ez részlet beilleszteni a HTML head az első oldal (ahol a cikkek személyre szabott listáját akkor jelennek meg):

```html
// define the "callback function" to render UI
<script> function callback(data) { … } </script>
// "data" provides the ranking of URLs, see example below.

// call to Ranking API
<script src="https://ds.microsoft.com/api/v2/<appId>/rank/<actionSetId>?<parameters>" async></script>
// action set id is the name of the corresponding RSS/Atom feed.

// same call with multiple action sets:
// <script src="https://ds.microsoft.com/api/v2/<appId>/rank/<A1>/<A2>/.../<An>?<parameters>" async></script>
```

> [!IMPORTANT]
> A visszahívási függvény a prioritás API hívása előtt kell definiálni.

> [!TIP]
> Késés javítása érdekében a prioritás API fel van fedve HTTP helyett HTTPS-en keresztül és a `http://ds.microsoft.com/api/v2/<appId>/rank/*`.
> Azonban HTTPS-végpontnak kell használható, ha az első oldal kiszolgált HTTPS használatával.

Paraméterek nem használják, a prioritás API HTTP-válasz esetén a JSONP-formátumú karakterláncot:

```json
callback({
   "ranking":[{"id":"url1"}, {"id":"url2"}, {"id":"url3"}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "actionSets":[{"id":"<A1>","lastRefresh":"2017-04-29T22:34:25.3401438Z"},
                 {"id":"<A2>","lastRefresh":"2017-04-30T22:34:25.3401438Z"}]});
```

A böngésző végrehajtja ezt a karakterláncot, hívása a `callback()` függvény.

A paraméter az előző példában a visszahívási függvény a következő sémával rendelkezik:

- `ranking` az URL-címek megjelenítendő rangsorolási biztosít.
- `eventId` belsőleg egyéni döntési szolgáltatás ezt a megfelelő kattintással rangsorolási kereséséhez.
- `appId` lehetővé teszi, hogy a visszahívási függvény segítségével különböztetheti meg egymástól a azonos weblapon futó egyéni döntési szolgáltatás több alkalmazás között.
- `actionSets` a prioritás API-hívás, a legutóbbi sikeres frissítés UTC időbélyegzője együtt használt minden egyes művelethez sorolja fel. Egyéni döntési szolgáltatás rendszeresen frissülnek a művelet set hírcsatornák. Például ha a művelet beállítja némelyike nem aktuális, a visszahívási függvény előfordulhat, hogy kell térhet vissza az alapértelmezett rangsorolási.

> [!IMPORTANT]
> A megadott művelet készletek feldolgozási, és esetleg törli a cikkek alapértelmezett rangsorolási kialakításához. Az alapértelmezett rangsorolási majd átrendezésekor és a HTTP-válasz. Az alapértelmezett rangsorolási itt van definiálva:
>
> - Egyes művelet készleten belül a cikkek vannak lerövidítette a 15 legutóbbi cikkekhez (Ha több mint 15 visszaadott).
> - Ha több művelet beállítása meg van adva, egyesítés ugyanabban a sorrendben, ahogy az API-hívás. A cikkek eredeti rendelési megmarad, egyes művelet készleten belül. Ismétlődések eltávolítja a korábbi másolatok helyett.
> - Az első `n` cikkek tartják egyesített közül cikkeket, ahol `n=20` alapértelmezés szerint.

### <a name="ranking-api-with-parameters"></a>A paraméterekkel rangsoroló API

A prioritás API lehetővé teszi, hogy ezeket a paramétereket:

- `details=1` és `details=2` minden cikkben szereplő további részleteit beszúrása `ranking`.
- `limit=<n>` Adja meg az alapértelmezett rangsorolási cikkek maximális számát. `n` közé kell esnie `2` és `30` (vagy ellenkező esetben a függvény egésszé csonkítja való `2` vagy `30`, illetve).
- `dnt=1` letiltja a felhasználói cookie-kat.

Paraméterek kombinálható szabvány a lekérdezési karakterlánc-formátum, például `details=2&dnt=1`.

> [!IMPORTANT]
> Az alapértelmezett beállítás, az Európai kell `dnt=1` mindaddig, amíg az ügyfél elfogadja a cookie-k szalagcím. Alapértelmezés szerint kiskorúak célzó webhelyek kell tenni. További információkért lásd: a [használati feltételek](https://www.microsoft.com/cognitive-services/en-us/legal/CognitiveServicesTerms20160804).

A `details=1` elem beszúrása minden cikknek `guid`, ha a művelet be API által kiszolgált. A HTTP-válasz:

```json
callback({
   "ranking":[{"id":"url1","details":[{"guid":"123"}]},
              {"id":"url2","details":[{"guid":"456"}]},
              {"id":"url3","details":[{"guid":"789"}]}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "actionSets":[{"id":"<A1>","lastRefresh":"timeStamp1"},
                 {"id":"<A2>","lastRefresh":"timeStamp2"}]});
```

A `details=2` elem hozzáadja az egyéni döntési szolgáltatás előfordulhat, hogy kinyerése cikkek Keresőmotor metaadatcímkékről részleteket [featurization kód](https://github.com/Microsoft/mwt-ds/tree/master/Crawl):

- `title` a `<meta property="og:title" content="..." />` vagy `<meta property="twitter:title" content="..." />` vagy `<title>...</title>`
- `description` a `<meta property="og:description" ... />` vagy `<meta property="twitter:description" content="..." />` vagy `<meta property="description" content="..." />`
- `image` a `<meta property="og:image" content="..." />`
- `ds_id` a `<meta name=”microsoft:ds_id” content="..." />`

A HTTP-válasz:

```json
callback({
   "ranking":[{"id":"url1","details":<details>},
              {"id":"url2","details":<details>},
              {"id":"url3","details":<details>}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "actionSets":[{"id":"<A1>","lastRefresh":"timeStamp1"},
                 {"id":"<A2>","lastRefresh":"timeStamp2"}]});
```

A `<details>` elem:

```json
[{"guid":"123"}, {"description":"some text", "ds_id":"234", "image":"ImageUrl1", "title":"some text"}]
```

## <a name="reward-api"></a>Ellenszolgáltatás API

Egyéni döntési szolgáltatás által használt csak az első tárhely kattint. Minden egyes kattintson egy ellenszolgáltatás 1 kerül értelmezésre. Egy kattintással hiánya egy ellenszolgáltatás 0 kerül értelmezésre. Kattintással összeveti a megfelelő sorba rendezés által előállított eseményazonosítók használatával a [prioritás API](#ranking-api) hívható meg. Ha szükséges, esemény azonosítók átadhatók munkamenet cookie-k használatával.

Elemre kattint, a felső tárolóhely kezelésére, az első oldala helyezhető el ezt a kódot:

```javascript
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/' + data.eventId,
    contentType: "application/json" })
```

Itt `data` a argumentuma a `callback()` működik, az előzőekben leírt. Használatával `data` lehetőségre a kód kezelése szükséges néhány eljárni. Ez a példa [oktatóanyag](custom-decision-service-tutorial-news.md#use-the-apis).

Csak tesztelési célokat szolgál a ellenszolgáltatás API használatával hívható [cURL](https://en.wikipedia.org/wiki/CURL):

```sh
curl -v https://ds.microsoft.com/api/v2/<appId>/reward/<eventId> -X POST -d 1 -H "Content-Type: application/json"
```

A várt hatással egy HTTP-válasz 200-as (OK). (Ha az Azure storage-fiók kulcs lett megadva, a portál) 1 a fejében, ezt az eseményt a naplóban tekintheti meg.

## <a name="action-set-api-customer-provided"></a>A művelet be API (az ügyfél a megadott)

Magas szinten a művelet be API (műveletek) cikkek listáját adja vissza. Minden cikk az URL-CÍMÉT és (opcionálisan) a cikk címe és a kiadvány dátum szerint van megadva. Megadhatja, hogy több művelet beállítja a portálon. Egy másik művelet beállítása API használható minden művelet be van állítva, a különböző URL-címként.

Minden művelet beállítása API kétféle módon valósítható: RSS-hírcsatorna, vagy egy Atom-hírcsatorna. Lehet, hogy egy kell a szabványoknak és a megfelelő XML-dokumentumot. RSS Íme egy példa:

```xml
<rss version="2.0">
<channel>
   <item>
      <title><![CDATA[title (possibly with url) ]]></title>
      <link>url</link>
      <guid>guid (could be a your internal database id)</guid>
      <pubDate>date</pubDate>
    </item>
   <item>
       ....
   </item>
</channel>
</rss>
```

Minden egyes legfelső szintű `<item>` elem leírja egy műveletet:

- `<link>` kötelező pedig a rendszer egy művelet.
- `<date>` Ha nagyobb, mint 15 elemek; figyelmen kívül hagyva Ellenkező esetben is kötelező.
  - Ha több mint 15 elem, a 15 legutóbbi használja a rendszer.
  - Kell lennie az RSS- vagy Atom, szabványos formátumban osztályban:
    - [RFC 822](https://tools.ietf.org/html/rfc822) RSS: például `"Fri, 28 Apr 2017 18:02:06 GMT"`
    - [RFC 3339](https://tools.ietf.org/html/rfc3339) az Atom: például `"2016-12-19T16:39:57-08:00"`
- `<title>` nem kötelező, és a szolgáltatások, a cikk leíró előállítására szolgál.
- `<guid>` nem kötelező, és a rendszer a visszahívási függvénynek átadott (Ha a `?details` paraméter meg van adva a prioritás API-hívásban).

Egyéb elemek egy `<item>` figyelmen kívül lesznek hagyva.

Az Atom hírcsatornát verzióját használja, az XML ugyanaz a szintaxis és a egyezmények.

> [!TIP]
> Ha a rendszer a saját cikk azonosítók használ, akkor átadhatók által a visszahívási függvénynek használatával `<guid>`.