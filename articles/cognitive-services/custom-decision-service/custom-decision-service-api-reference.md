---
title: API-hivatkozás – Custom Decision Service
titlesuffix: Azure Cognitive Services
description: A Custom Decision Service teljes API-útmutatója.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: slivkins
ROBOTS: NOINDEX
ms.openlocfilehash: 4f263e3b57103174f0084ab3d25430d8c47359fd
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707299"
---
# <a name="api"></a>API

Az Azure Custom Decision Service két olyan API-t biztosít, amelyek mindegyike meghívja az egyes döntéseket: a [rangsorolási API](#ranking-api) -t, amely a műveletek rangsorolását és a [jutalmazási API](#reward-api) -t adja meg a jutalmak Emellett a [műveleti csoport API](#action-set-api-customer-provided) -t is megadhatja az Azure Custom Decision Service műveletekhez. Ez a cikk a három API-t ismerteti. Az alábbi tipikus forgatókönyvek azt mutatják be, hogy mikor Custom Decision Service optimalizálja a cikkek rangsorolását.

## <a name="ranking-api"></a>Rangsorolási API

A rangsorolási API szabványos [jsnop támogatással](https://en.wikipedia.org/wiki/JSONP)-stílusú kommunikációs mintát használ a késés optimalizálása és az [azonos eredetű házirend](https://en.wikipedia.org/wiki/Same-origin_policy)megkerülése érdekében. Az utóbbi tiltja, hogy a JavaScript az oldal forrásán kívülről beolvassa az adatok beolvasását.

Szúrja be ezt a kódrészletet az oldal HTML-fejlécére (ahol a cikkek személyre szabott listája megjelenik):

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
> A visszahívási függvényt a rangsor API meghívása előtt kell meghatározni.

> [!TIP]
> A késés javítása érdekében a rangsorolási API HTTPS helyett HTTP-n keresztül érhető el, `https://ds.microsoft.com/api/v2/<appId>/rank/*`a következő módon:.
> Egy HTTPS-végpontot azonban akkor kell használni, ha az előlapot HTTPS-kapcsolaton keresztül kézbesítik.

Ha nem használja a paramétereket, a rangsorolási API HTTP-válasza egy JSNOP támogatással-formázott karakterlánc:

```json
callback({
   "ranking":[{"id":"url1"}, {"id":"url2"}, {"id":"url3"}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "actionSets":[{"id":"<A1>","lastRefresh":"2017-04-29T22:34:25.3401438Z"},
                 {"id":"<A2>","lastRefresh":"2017-04-30T22:34:25.3401438Z"}]});
```

A böngésző utána a `callback()` függvény meghívásaként értelmezve végrehajtja ezt a karakterláncot.

Az előző példában szereplő visszahívási függvény paraméterének a következő sémája van:

- `ranking`a megjelenítendő URL-címek rangsorát adja meg.
- `eventId`belső használatban van Custom Decision Service, hogy az megfeleljen a megfelelő kattintásoknak.
- `appId`lehetővé teszi, hogy a visszahívási függvény megkülönböztetse az ugyanazon a weblapon futó Custom Decision Service több alkalmazását.
- `actionSets`felsorolja a rangsorolási API-hívásban használt összes műveletet, valamint az utolsó sikeres frissítés UTC-időbélyegét. Custom Decision Service rendszeresen frissíti a műveleti készlet hírcsatornáit. Ha például a műveleti készletek némelyike nem aktuális, előfordulhat, hogy a visszahívási függvénynek vissza kell térnie az alapértelmezett rangsorhoz.

> [!IMPORTANT]
> A megadott műveleti készletek feldolgozása és feltehetően metszi a cikkek alapértelmezett rangsorolását. Az alapértelmezett rangsorolás ekkor átrendezve lesz, és a HTTP-válaszban visszakerül. Az alapértelmezett rangsor itt van definiálva:
>
> - Az egyes műveleti készleteken belül a cikkek a 15 legutóbbi cikkre lesznek metszve (ha több, mint 15).
> - Ha több Művelettípus van megadva, a rendszer az API-hívásban megegyező sorrendben egyesíti őket. A cikkek eredeti sorrendje megmarad az egyes műveleti készleteken belül. A rendszer eltávolítja a duplikált elemeket a korábbi másolatok javára.
> - Az első `n` cikkeket a cikkek összevont listájából kell tárolni, ahol `n=20` alapértelmezés szerint.

### <a name="ranking-api-with-parameters"></a>Rangsor API paraméterekkel

A rangsor API a következő paramétereket teszi lehetővé:

- `details=1`és `details=2` további részleteket szúr be a `ranking`témakörben felsorolt cikkekhez.
- `limit=<n>`meghatározza a cikkek maximális számát az alapértelmezett rangsorban. `n`és `2` `2` `30`között kell lennie (vagy más, vagy). `30`
- `dnt=1`letiltja a felhasználói cookie-kat.

A paraméterek kombinálhatók a standard, a lekérdezési karakterlánc szintaxisában, `details=2&dnt=1`például:.

> [!IMPORTANT]
> Az Európai alapértelmezett beállításnak addig kell `dnt=1` lennie, amíg az ügyfél el nem fogadja a cookie-szalagcímet. Azt is meg kell adni, hogy az alapértelmezett beállítás a kiskorúakat célzó webhelyekhez. További információ: használati [feltételek](https://www.microsoft.com/cognitive-services/en-us/legal/CognitiveServicesTerms20160804).

Az `details=1` elem beszúrja az egyes `guid`cikkeket, ha azokat a Action set API szolgáltatja. A HTTP-válasz:

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

Az `details=2` elem további részleteket tartalmaz, amelyeket Custom Decision Service a SEO MetaTags [featurization](https://github.com/Microsoft/mwt-ds/tree/master/Crawl)-kódjából kinyert cikkekből:

- `title``<meta property="og:title" content="..." />` vagy vagy`<meta property="twitter:title" content="..." />``<title>...</title>`
- `description``<meta property="og:description" ... />` vagy vagy`<meta property="twitter:description" content="..." />``<meta property="description" content="..." />`
- `image`a`<meta property="og:image" content="..." />`
- `ds_id`a`<meta name=”microsoft:ds_id” content="..." />`

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

## <a name="reward-api"></a>Jutalom API

Custom Decision Service a csak a felső tárolóhelyen található kattintásokat használja. A rendszer az egyes kattintásokat 1 jutalomként értelmezi. A kattintások hiánya 0 jutalomként lesz értelmezve. A kattintások a [rangsor API](#ranking-api) -hívás által generált eseményazonosító használatával egyeznek meg a megfelelő rangsorokkal. Ha szükséges, az eseményazonosító a munkamenet-cookie-kon keresztül adható át.

Ha egy kattintást szeretne kezelni a felső tárolóhelyen, helyezze ezt a kódot az oldalára:

```javascript
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/' + data.eventId,
    contentType: "application/json" })
```

Itt `data` látható a `callback()` függvény argumentuma a korábban leírtak szerint. A `data` alkalmazásban kattintson a kód kezelése lehetőségre. Ebben az oktatóanyagban egy példa [](custom-decision-service-tutorial-news.md#use-the-apis)látható.

Csak tesztelés esetén a jutalmazási API a [curl](https://en.wikipedia.org/wiki/CURL)használatával hívható meg:

```sh
curl -v https://ds.microsoft.com/api/v2/<appId>/reward/<eventId> -X POST -d 1 -H "Content-Type: application/json"
```

A várt hatás a 200-es HTTP-válasz (OK). Az eseményhez tartozó 1 jutalmat a naplóban tekintheti meg (ha az Azure Storage-fiók kulcsát a portálon adták meg).

## <a name="action-set-api-customer-provided"></a>Műveleti set API (ügyfél által biztosított)

A műveleti készlet API magas szinten a cikkek listáját (műveletek) adja vissza. Minden cikket a saját URL-címe és (opcionálisan) a cikk címe és a közzététel dátuma határoz meg. A portálon több műveleti halmaz is megadható. Egy másik műveleti set API-t kell használni az egyes műveleti készletekhez, külön URL-címként.

Az egyes műveleti set API-k kétféleképpen valósíthatók meg: RSS-hírcsatorna vagy Atom-hírcsatorna. Az egyiknek meg kell felelnie a standardnak, és megfelelő XML-t kell visszaadnia. Az RSS esetében Íme egy példa:

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

Minden legfelső szintű `<item>` elem egy műveletet ír le:

- `<link>`kötelező, és műveleti AZONOSÍTÓként van használatban.
- `<date>`figyelmen kívül hagyja a rendszer, ha a érték kisebb vagy egyenlő, mint 15 elem; Ellenkező esetben kötelező megadni.
  - Ha több mint 15 elemet használ, a rendszer a 15 legutóbbiat használja.
  - Az RSS-hez vagy az atomhoz szabványos formátumban kell megadni:
    - [RFC 822](https://tools.ietf.org/html/rfc822) RSS-hez: például`"Fri, 28 Apr 2017 18:02:06 GMT"`
    - [RFC 3339](https://tools.ietf.org/html/rfc3339) for Atom: például`"2016-12-19T16:39:57-08:00"`
- `<title>`a nem kötelező, és a cikkben ismertetett funkciók létrehozásához használatos.
- `<guid>`a nem kötelező, és a rendszeren keresztül továbbítja a visszahívási függvényt (ha a `?details` paraméter a rangsor API-hívásban van megadva).

A rendszer figyelmen kívül `<item>` hagyja az egy elemen belüli egyéb elemeket.

Az Atom-hírcsatorna verziója ugyanazokat az XML-szintaxist és konvenciókat használja.

> [!TIP]
> Ha a rendszer saját cikk-azonosítókat használ, akkor a használatával `<guid>`a visszahívási függvénynek adhatók át.
