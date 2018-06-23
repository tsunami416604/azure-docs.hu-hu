---
title: Egy alkalmazás - Azure kognitív szolgáltatások API hívása |} Microsoft Docs
description: Hogyan lásson Azure egyéni döntési szolgáltatással, ha az alkalmazással hívja az API-kat.
services: cognitive-services
author: slivkins
manager: slivkins
ms.service: cognitive-services
ms.topic: article
ms.date: 05/10/2018
ms.author: slivkins
ms.reviewer: marcozo, alekh
ms.openlocfilehash: 2d02b0deaaa701dd0b4818638827c04e2c946558
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349746"
---
# <a name="call-api-from-an-app"></a>API hívása egy alkalmazásból

Győződjön meg az Azure egyéni döntési Service API-k hívásainak alkalmazással. Ez a cikk azt ismerteti, hogyan néhány alapvető beállítások használatába.

Ügyeljen arra, hogy [az alkalmazás regisztrálása](custom-decision-service-get-started-register.md), első.

Nincsenek két elvégezte a smartphone alkalmazás egyéni döntési Service API-hívásokban: a tartalom és a jelentés egy ellenszolgáltatás a ellenszolgáltatás API hívása rangsorolt listáját beszerzése a prioritás API hívása. Az alábbiakban a minta-hívások [cURL](https://en.wikipedia.org/wiki/CURL).

További információkért lásd: a hivatkozási [API](custom-decision-service-api-reference.md).

Első lépésként a prioritás API-hívás. A fájl létrehozása `<request.json>`, amely rendelkezik a művelet be azonosítóját. Ezt az Azonosítót a megfelelő RSS nevét, vagy Atom hírcsatornát, hogy a portál meg:

```json
{"decisions":
     [{ "actionSets":[{"id":{"id":"<actionSetId>"}}] }]}
```

Sok művelet beállítása az alábbiak szerint adható meg:

```json
{"decisions":
    [{ "actionSets":[{"id":{"id":"<actionSetId1>"}},
                     {"id":{"id":"<actionSetId2>"}}] }]}
```

A JSON-fájlt a rendszer elküldi a rangsorolási kérelem részeként:

```shell
curl -d @<request.json> -X POST https://ds.microsoft.com/api/v2/<appId>/rank --header "Content-Type: application/json"
```

Itt `<appId>` a nevét, az alkalmazás regisztrálva lesz a portálon. A tartalomelem, amely meg tudja jeleníteni az alkalmazás egy rendezett sorozata kell kapnia. A visszaadandó minta néz ki:

```json
[{ "ranking":[{"id":"actionId3"}, {"id":"actionId1"}, {"id":"actionId2"}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "rewardAction":"actionId3",
   "actionSets":[{"id":"<actionSetId1>","lastRefresh":"2017-04-29T22:34:25.3401438Z"},
                 {"id":"<actionSetId2>","lastRefresh":"2017-04-30T22:34:25.3401438Z"}]}]
```

A visszatérési első része rendelkezik rendezett műveletek, a műveleti azonosítók megadott listáját. Egy cikk a Műveletazonosító egy URL-címet. A teljes kérelem is rendelkezik egy egyedi `<eventId>`, a rendszer által létrehozott.

Később, megadhatja, ha a elemre kattint, az első tartalomelem ezt az eseményt, amely a megfigyelt `<actionId3>`. Ezen majd jelentheti a ellenszolgáltatás `<eventId>` egyéni döntési szolgáltatásnak a ellenszolgáltatás API-n keresztül, egy másik kérelem, mint:

```shell
curl -v https://ds.microsoft.com/api/v2/<appId>/reward/<eventId> -X POST
```

Végül meg kell adnia a művelet beállítása API, amely akkor, ha egyéni döntési szolgáltatás (műveletek) cikkek listáját adja vissza. Az API RSS-hírcsatorna, mint ahogy az itt látható:

```xml
<rss version="2.0">
<channel>
   <item>
      <title><![CDATA[title (possibly with url) ]]></title>
      <link>url</link>
      <pubDate>Thu, 27 Apr 2017 16:30:52 GMT</pubDate>
    </item>
   <item>
       ....
   </item>
</channel>
</rss>
```

Itt, minden egyes legfelső szintű `<item>` elem egy cikk ismerteti. A `<link>` kötelező, és egy művelet ID egyéni döntési szolgáltatás által használt. Adja meg `<date>` (a szabványos RSS formátumban), ha több mint 15 cikkek megismerte. A 15 legutóbbi cikkek használják. A `<title>` nem kötelező, és a szöveg funkcióiról a cikk létrehozására szolgál.

## <a name="next-steps"></a>További lépések

* Munka – egy [oktatóanyag](custom-decision-service-tutorial-news.md) részletesebb például.
* Tekintse át a hivatkozás [API](custom-decision-service-api-reference.md) további információt a megadott funkciót.