---
title: API hívása egy böngészőből – Custom Decision Service
titlesuffix: Azure Cognitive Services
description: Hogyan azáltal, hogy az API-hívások közvetlenül a böngészőből a Custom Decision Service a weblapon optimalizálása.
services: cognitive-services
author: slivkins
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-decision-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: slivkins
ms.openlocfilehash: 01f75d333e3eea5af35ca57bd44bc76148a481f2
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48867615"
---
# <a name="call-api-from-a-browser"></a>API hívása egy böngészőből

Ez a cikk segít az Azure egyéni döntési szolgáltatás API-hívások közvetlenül böngészőből.

Ügyeljen arra, hogy [regisztrálhatja alkalmazását](custom-decision-service-get-started-register.md), először.

Első lépések. Az alkalmazás egy első oldal, amely több cikk oldalak hivatkozásokat tartalmaz tartalmazóként van modellezve. Az első lapon adja meg az eredménykészlet sorrendjét a cikkek oldalai a Custom Decision Service használja. Az alábbi kód beszúrása az első oldal HTML vezetője:

```html
// Define the "callback function" to render UI
<script> function callback(data) { … } </script>

// call Ranking API, after callback() is defined
<script src="https://ds.microsoft.com/api/v2/<appId>/rank/<actionSetId>" async></script>
```

A `data` argumentum URL-címek a megjelenítésre rangsorolását tartalmazza. További információkért tekintse meg a hivatkozás [API](custom-decision-service-api-reference.md).

Kezeli a legfelső cikk felhasználói elemre kattint, az első oldalon hívja meg a következő kódot:

```javascript
// call Reward API to report a click
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/' + data.eventId,
    contentType: "application/json" })
```

Itt `data` az argumentum, a `callback()` függvény. Megvalósítási példa található ezen [oktatóanyag](custom-decision-service-tutorial-news.md#use-the-apis).

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

Itt minden felső szintű `<item>` elem egy cikk írja le. A `<link>` megadása kötelező, és a Custom Decision Service egy művelet ID használják. Adja meg `<date>` (a szabványos RSS formátumban) Ha több mint 15 cikkeket. A 15 legutóbbi cikkek szolgálnak. A `<title>` nem kötelező, és a cikkhez tartozó szöveg kapcsolatos funkciók létrehozására szolgál.

## <a name="next-steps"></a>További lépések

* Haladjon végig a [oktatóanyag](custom-decision-service-tutorial-news.md) részletesebb példát.
* Tekintse meg a hivatkozás [API](custom-decision-service-api-reference.md) további információ a megadott funkciót.