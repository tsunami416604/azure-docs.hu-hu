---
title: API-referencia – Custom Decision Service
titlesuffix: Azure Cognitive Services
description: A Custom Decision Service teljes körű API útmutatót.
services: cognitive-services
author: slivkins
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-decision-service
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: slivkins
ms.openlocfilehash: 3d9b87241946a04ae71fabde9958b24ad626c0db
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46364024"
---
# <a name="api"></a>API

Azure Custom Decision Service biztosít két API-k, amelyek az egyes döntési nevezzük: a [rangsorolási API](#ranking-api) műveletek rangsorolását bemeneti és a [ellenszolgáltatás API](#reward-api) a kimenetben a fejében. Ezenkívül rendelkeznek egy [művelet beállítása API](#action-set-api-customer-provided) , adja meg az Azure Custom Decision Service a végrehajtandó műveleteket. Ez a cikk ismerteti ezen három API-k. Egy tipikus forgatókönyv szolgál alábbi megjelenítése, ha a Custom Decision Service optimalizálja a cikkek rangsorolását.

## <a name="ranking-api"></a>API rangsorolása

Az ennek a területnek API-t használja egy standard [JSNOP](https://en.wikipedia.org/wiki/JSONP)-stílus kommunikációs mintát optimalizálja a késést, és figyelmen kívül hagyása az [azonoseredet-](https://en.wikipedia.org/wiki/Same-origin_policy). Az utóbbi megakadályozza, hogy a JavaScript kívül az oldal forrás adatainak beolvasása.

Ez a kódrészlet beszúrása a (ahol egy személyre szabott cikkek listája megjelenik) első oldal HTML vezetője:

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
> A visszahívási függvény a ranglistán megjelenő API-hívás előtt definiálni kell.

> [!TIP]
> Javíthatja a késés, a rangsorolás API elérhetővé HTTP helyett HTTPS, mint a `http://ds.microsoft.com/api/v2/<appId>/rank/*`.
> Azonban HTTPS-végpontokat kell használni, ha az első oldal kiszolgált HTTPS-en keresztül.

Paraméterek nem használhatók, ha a HTTP-válasz, a rangsorolás API egy JSNOP-formátumú karakterláncot:

```json
callback({
   "ranking":[{"id":"url1"}, {"id":"url2"}, {"id":"url3"}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "actionSets":[{"id":"<A1>","lastRefresh":"2017-04-29T22:34:25.3401438Z"},
                 {"id":"<A2>","lastRefresh":"2017-04-30T22:34:25.3401438Z"}]});
```

A böngésző végrehajtja ezt a karakterláncot, a hívást a `callback()` függvény.

Az előző példában a visszahívási függvény paraméterének a következő mintát követik:

- `ranking` Itt megjelenő URL-címek rangsorolását.
- `eventId` belső használatára szolgál a Custom Decision Service a ennek a területnek a megfelelő kattintással megfelelően.
- `appId` lehetővé teszi a visszahívási függvény, amely a Custom Decision Service a azonos weblap futó több alkalmazás között.
- `actionSets` minden egyes művelethez állítsa be az ranglistán megjelenő API-hívás, valamint a legutóbbi sikeres frissítés történő küldés időbélyegzője (UTC) használt sorolja fel. A Custom Decision Service a művelet set hírcsatornák rendszeres időközönként frissíti. Például ha a műveleti csoportok némelyike nem aktuális, a visszahívási függvény előfordulhat, hogy kell visszatér az alapértelmezés szerint a rangsorolás.

> [!IMPORTANT]
> A megadott művelet készletek feldolgozása, és esetleg törli a cikksorozat alapértelmezés szerint a rangsorolás kialakításához. Alapértelmezés szerint a rangsorolás majd átrendezésekor és a HTTP-válaszban visszaadott. Alapértelmezés szerint a rangsorolás itt van definiálva:
>
> - Minden művelet csoporton belül a cikkeket is törli 15 legutóbbi cikkek (Ha a több mint 15 adódik vissza).
> - Ha több művelet felelnek meg van adva, egyesítés ugyanabban a sorrendben, ahogy az API-hívás. A cikkek eredeti sorrendjét a rendszer megőrzi minden művelet csoporton belül. Ismétlődő értéke a korábbi példányok törlődnek.
> - Az első `n` cikkek őrzi meg az egyesített listájából cikkeket, ahol `n=20` alapértelmezés szerint.

### <a name="ranking-api-with-parameters"></a>Paraméterekkel rendelkező API rangsorolása

A prioritás API lehetővé teszi, hogy ezeket a paramétereket:

- `details=1` és `details=2` szúr be a további részleteket kapcsolatban szereplő minden egyes cikk `ranking`.
- `limit=<n>` Alapértelmezés szerint a rangsorolás cikkek maximális számát adja meg. `n` közé kell esnie `2` és `30` (vagy más csonkolva, `2` vagy `30`, illetve).
- `dnt=1` letiltja a felhasználói cookie-kat.

Paraméterek kombinálható is a standard szintű, a lekérdezési karakterlánc szintaxisát, például `details=2&dnt=1`.

> [!IMPORTANT]
> Az alapértelmezett beállítás, az Európai kell `dnt=1` mindaddig, amíg az ügyfél elfogadja a cookie-k szalagcím. Az alapértelmezett beállítás a websites kiskorúak célzó is kell lennie. További információkért lásd: a [használati feltételeket tartalmazó fájl](https://www.microsoft.com/cognitive-services/en-us/legal/CognitiveServicesTerms20160804).

A `details=1` elem beszúrása a cikkhez tartozó `guid`, ha ez a művelet beállítása API által kiszolgált. A HTTP-válasz:

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

A `details=2` elem hozzáadása a további részletek, amelyek a Custom Decision Service a cikkek keresőmotor-Optimalizálást metaadatcímkékről előfordulhat, hogy kinyerése [featurization kód](https://github.com/Microsoft/mwt-ds/tree/master/Crawl):

- `title` a `<meta property="og:title" content="..." />` vagy `<meta property="twitter:title" content="..." />` vagy `<title>...</title>`
- `description` a `<meta property="og:description" ... />` vagy `<meta property="twitter:description" content="..." />` vagy `<meta property="description" content="..." />`
- `image` A `<meta property="og:image" content="..." />`
- `ds_id` A `<meta name=”microsoft:ds_id” content="..." />`

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

Egyéni döntési szolgáltatás használja csak a felső tárolóhely kattint. Minden egyes kattintson egy ellenszolgáltatás 1 kerül értelmezésre. Egy-két kattintással hiánya egy ellenszolgáltatás 0 kerül értelmezésre. Kattintással a megfelelő módosítják a rangsort egyező által előállított eseményazonosítók használatával a [rangsorolási API](#ranking-api) hívja. Ha szükséges, esemény azonosítók átadhatók munkamenet cookie-k használatával.

Elemre kattint a felső tárhely kezelése érdekében helyezze ezt a kódot az első oldalon:

```javascript
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/' + data.eventId,
    contentType: "application/json" })
```

Itt `data` az argumentum, a `callback()` működik, az előzőekben leírtak szerint. Használatával `data` lehetőségre a kezelő kódot igényel néhány ellátás. A jelen példa [oktatóanyag](custom-decision-service-tutorial-news.md#use-the-apis).

Csak tesztelés esetén, a ellenszolgáltatás API nem hívható meg keresztül [cURL](https://en.wikipedia.org/wiki/CURL):

```sh
curl -v https://ds.microsoft.com/api/v2/<appId>/reward/<eventId> -X POST -d 1 -H "Content-Type: application/json"
```

A várt jár, a 200 (OK) HTTP-választ. Láthatja a naplózott eseményhez 1 a ellenszolgáltatás (Ha egy Azure storage-fiókkulcs lett megadva a portálon).

## <a name="action-set-api-customer-provided"></a>A művelet beállítása API (vásárló által biztosított)

Magas szinten a művelet beállítása API (műveletek) cikkek listáját adja vissza. Minden egyes cikk URL-címe és (opcionálisan) a cikk címe és a közzététel dátuma szerint van megadva. Megadhatja, hogy több művelet beállítja a portálon. Egy másik művelet beállítása API használata, az egyes művelet, különböző URL-ként.

Minden művelet beállítása API két módon hajtható végre: az RSS-hírcsatorna vagy Atom-hírcsatorna formájában. Akár egy kell a szabványoknak és a egy megfelelő XML-t adja vissza. Az RSS Íme egy példa:

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

Minden felső szintű `<item>` elem leírja egy műveletet:

- `<link>` kötelező, és szolgál egy művelet.
- `<date>` rendszer figyelmen kívül hagyja, ha legfeljebb 15 elemek; Ellenkező esetben azt megadása kötelező.
  - Ha több mint 15 elemek, 15 legutóbbi azokat használják.
  - Kell lennie az RSS- vagy Atom, szabványos formátumban jelölik:
    - [RFC 822](https://tools.ietf.org/html/rfc822) RSS: például `"Fri, 28 Apr 2017 18:02:06 GMT"`
    - [RFC 3339](https://tools.ietf.org/html/rfc3339) az Atom: például `"2016-12-19T16:39:57-08:00"`
- `<title>` nem kötelező, és az funkciók, amelyek ismertetik a cikk létrehozására szolgál.
- `<guid>` nem kötelező, és a rendszer a visszahívási függvény segítségével átadott (Ha a `?details` paraméter meg van adva a ranglistán megjelenő API-hívással).

Belüli más elemeket egy `<item>` figyelmen kívül hagyja.

Az Atom-hírcsatorna verzióját használja az XML ugyanazt a szintaxist és a konvenciók.

> [!TIP]
> Ha a rendszer a saját cikk azonosítók, azokat is át lehet adni által a visszahívási függvény használatával `<guid>`.