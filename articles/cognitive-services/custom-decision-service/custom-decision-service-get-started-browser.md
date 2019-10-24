---
title: API hívása böngészőből – Custom Decision Service
titlesuffix: Azure Cognitive Services
description: Weblap optimalizálása az API-hívások közvetlenül a böngészőből a Custom Decision Service való beírásával.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: slivkins
ROBOTS: NOINDEX
ms.openlocfilehash: 28ad4681242765bf2da9b1f13dc828e23cce1794
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707266"
---
# <a name="call-api-from-a-browser"></a>API hívása egy böngészőből

Ebből a cikkből megtudhatja, hogy közvetlenül egy böngészőből hívja meg az Azure Custom Decision Service API-kat.

Ügyeljen arra, hogy először [regisztrálja az alkalmazást](custom-decision-service-get-started-register.md).

Lássunk neki! Az alkalmazás előlapjánként van modellezve, amely több cikkre is hivatkozik. A Kezdőlap Custom Decision Service a cikkek sorrendjének megadására használatos. Szúrja be a következő kódot az oldal HTML-fejlécére:

```html
// Define the "callback function" to render UI
<script> function callback(data) { … } </script>

// call Ranking API, after callback() is defined
<script src="https://ds.microsoft.com/api/v2/<appId>/rank/<actionSetId>" async></script>
```

Az `data` argumentum a megjelenítendő URL-címek rangsorát tartalmazza. További információ: Reference [API](custom-decision-service-api-reference.md).

A felhasználó kezeléséhez kattintson a fenti cikkre, hívja meg a következő kódot az előlapon:

```javascript
// call Reward API to report a click
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/' + data.eventId,
    contentType: "application/json" })
```

Itt látható a `callback()` függvény argumentuma. `data` Ebben az oktatóanyagban egy implementációs példa [](custom-decision-service-tutorial-news.md#use-the-apis)is található.

Végül meg kell adnia a műveleti set API-t, amely visszaadja a Custom Decision Service által megfontolandó cikkek (műveletek) listáját. Az API-t RSS-hírcsatornaként implementálhatja, ahogy az itt látható:

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

Itt minden legfelső szintű `<item>` elem egy cikket ír le. A `<link>` kötelező, és Custom Decision Service alapján műveleti azonosítóként használja. Ha `<date>` több mint 15 cikket tartalmaz, akkor a (szabványos RSS-formátumban) legyen megadva. A 15 legutóbb használt cikk. A `<title>` nem kötelező, és szöveggel kapcsolatos szolgáltatások létrehozására szolgál a cikkhez.

## <a name="next-steps"></a>További lépések

* Részletes példa az [oktatóanyagra](custom-decision-service-tutorial-news.md) .
* A megadott funkciókkal kapcsolatos további információkért tekintse meg a hivatkozási [API](custom-decision-service-api-reference.md) -t.
