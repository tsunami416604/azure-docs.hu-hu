---
title: A böngésző - Azure kognitív szolgáltatások API hívása |} Microsoft Docs
description: Ismerkedés az Azure egyéni döntési szolgáltatáshoz egy weblap optimalizálni API-hívások közvetlenül a böngészőből módjáról.
services: cognitive-services
author: slivkins
manager: slivkins
ms.service: cognitive-services
ms.topic: article
ms.date: 05/09/2018
ms.author: slivkins,marcozo,alekh
ms.openlocfilehash: 10236c9d8f70d9b90a896464b4f86a847ee904c2
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349115"
---
# <a name="call-api-from-a-browser"></a>API hívása egy böngészőből

Ez a cikk segít az Azure egyéni döntési Service API-k meghíváshoz közvetlenül a böngészőből.

Ügyeljen arra, hogy [az alkalmazás regisztrálása](custom-decision-service-get-started-register.md), első.

Lássunk neki. Az alkalmazás első lap, és hivatkozik, és több lapokra rendelkezőként van modellezve. Az első lap egyéni döntési szolgáltatás segítségével adja meg a cikk lapok sorrendje. Az alábbi kód beszúrása az első oldal HTML vezetője:

```html
// Define the "callback function" to render UI
<script> function callback(data) { … } </script>

// call Ranking API, after callback() is defined
<script src="https://ds.microsoft.com/api/v2/<appId>/rank/<actionSetId>" async></script>
```

A `data` argumentum tartalmazza a megjeleníteni kívánt URL-címeket prioritását. További információkért lásd: a hivatkozási [API](custom-decision-service-api-reference.md).

Kezeli a felhasználó elemre kattint, a legfelső cikk, hívja meg a következő kódot az első oldalon:

```javascript
// call Reward API to report a click
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/' + data.eventId,
    contentType: "application/json" })
```

Itt `data` a argumentuma a `callback()` függvény. Megvalósítási példa található ezen [oktatóanyag](custom-decision-service-tutorial-news.md#use-the-apis).

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

Itt, minden egyes legfelső szintű `<item>` elem egy cikk ismerteti. A `<link>` kötelező, és egy művelet ID egyéni döntési szolgáltatás által használt. Adja meg `<date>` (a szabványos RSS formátumban) Ha több mint 15 cikkeket. A 15 legutóbbi cikkek használják. A `<title>` nem kötelező, és a szöveg funkcióiról a cikk létrehozására szolgál.

## <a name="next-steps"></a>További lépések

* Munka – egy [oktatóanyag](custom-decision-service-tutorial-news.md) részletesebb például.
* Tekintse át a hivatkozás [API](custom-decision-service-api-reference.md) további információt a megadott funkciót.