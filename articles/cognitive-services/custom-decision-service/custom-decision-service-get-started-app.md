---
title: API hívása egy alkalmazásból – Custom Decision Service
titlesuffix: Azure Cognitive Services
description: Tudnivalók a Custom Decision Service API-k hívása egy alkalmazással.
services: cognitive-services
author: slivkins
manager: cgronlun
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: slivkins
ms.openlocfilehash: fed45ec10eca26c58bc3b3c70991c1f7f468e393
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55219413"
---
# <a name="call-api-from-an-app"></a>API hívása egy alkalmazásból

Győződjön meg az Azure egyéni döntési szolgáltatás API-hívásait alkalmazással. Ez a cikk bemutatja, hogyan kezdheti el néhány alapvető beállításokkal.

Ügyeljen arra, hogy [regisztrálhatja alkalmazását](custom-decision-service-get-started-register.md), először.

Nincsenek a két API-hívást hajt végre az alkalmazással a Custom Decision Service: egy a ranglistán megjelenő API meghívásához a tartalom és a egy ellenszolgáltatás jelentést a ellenszolgáltatás API hívása rangsorolt listájának beszerzése érdekében. Az alábbiakban a minta meghívja a [cURL](https://en.wikipedia.org/wiki/CURL).

További információkért tekintse meg a hivatkozás [API](custom-decision-service-api-reference.md).

Indítsa el a ranglistán megjelenő API-hívás. A fájl létrehozásához `<request.json>`, amely rendelkezik a beállított művelet azonosítója. Ezt az Azonosítót a megfelelő RSS nevét, vagy Atom-hírcsatorna megadott a portálon:

```json
{"decisions":
     [{ "actionSets":[{"id":{"id":"<actionSetId>"}}] }]}
```

Számos művelet beállítja a következő adható meg:

```json
{"decisions":
    [{ "actionSets":[{"id":{"id":"<actionSetId1>"}},
                     {"id":{"id":"<actionSetId2>"}}] }]}
```

A JSON-fájlt a rendszer elküldi a rangsorolás kérelem részeként:

```shell
curl -d @<request.json> -X POST https://ds.microsoft.com/api/v2/<appId>/rank --header "Content-Type: application/json"
```

Itt `<appId>` az alkalmazás nevét a portál regisztrált. Tartalom cikkek, amelyek az alkalmazás leképezhetők egy rendezett készlet kell kapnia. A minta visszatérési hasonlóan néz ki:

```json
[{ "ranking":[{"id":"actionId3"}, {"id":"actionId1"}, {"id":"actionId2"}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "rewardAction":"actionId3",
   "actionSets":[{"id":"<actionSetId1>","lastRefresh":"2017-04-29T22:34:25.3401438Z"},
                 {"id":"<actionSetId2>","lastRefresh":"2017-04-30T22:34:25.3401438Z"}]}]
```

A visszaadandó első része rendelkezik rendezett műveletek által a műveleti azonosítók megadott listáját. A művelet azonosítója egy cikken, az URL. A teljes kérést is rendelkezik egy egyedi `<eventId>`, a rendszer hozta létre.

Később, megadhatja, ha megfigyelte elemre kattint, ez az esemény, amely az első tartalomelem `<actionId3>`. Egy ellenszolgáltatás majd jelentheti a `<eventId>` a Custom Decision Service a ellenszolgáltatás API-n keresztül, egy másik kérelem például:

```shell
curl -v https://ds.microsoft.com/api/v2/<appId>/reward/<eventId> -X POST
```

Végül meg kell adnia a művelet beállítása API, amely szerint a Custom Decision Service a cikkeket (műveletek) listáját adja vissza. Az API egy RSS-hírcsatornát, mint ahogy az itt látható:

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

Itt minden felső szintű `<item>` elem egy cikk írja le. A `<link>` megadása kötelező, és a Custom Decision Service egy művelet ID használják. Adja meg `<date>` (a szabványos RSS formátumban) több mint 15 cikkeket, ha. A 15 legutóbbi cikkek szolgálnak. A `<title>` nem kötelező, és a cikkhez tartozó szöveg kapcsolatos funkciók létrehozására szolgál.

## <a name="next-steps"></a>További lépések

* Haladjon végig a [oktatóanyag](custom-decision-service-tutorial-news.md) részletesebb példát.
* Tekintse meg a hivatkozás [API](custom-decision-service-api-reference.md) további információ a megadott funkciót.
