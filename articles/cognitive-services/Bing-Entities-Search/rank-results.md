---
title: Prioritás használatával válaszok megjelenítendő |} Microsoft Docs
description: Bemutatja, hogyan prioritás használatával megjelenítheti a kérdésekre adott válaszokat, hogy az entitás Bing keresési API adja vissza.
services: cognitive-services
author: v-jerkin
manager: ehansen
ms.assetid: BBF87972-B6C3-4910-BB52-DE90893F6C71
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 12/12/2017
ms.author: v-jerkin
ms.openlocfilehash: 53354c0f78419a37e8896bb4d00e0d7aebf32203
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37059991"
---
# <a name="using-ranking-to-display-results"></a>Eredmények megjelenítéséhez használja a prioritása  

Minden entitás keresési válasz tartalmazza a [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse) fogadja a hívást, hasonló egy, a Bing webes keresés választ, amely meghatározza, hogyan meg kell jelenítenie a keresési eredmények között. A rangsorolási válasz sarkpontot mainline, az eredmények és az oldalsó sáv tartalom felsorolását tartalmazza. A sarkpontot eredmény fontos vagy a jól láthatóan elhelyezett eredménye, és először üzenetnek kell megjelennie. Ha nem jelenik meg a fennmaradó eredményezi egy hagyományos mainline és az oldalsó sáv formátumát, mint az oldalsó sáv mainline tartalom magasabb látható kell megadnia. 
  
Az egyes csoportok a [elemek](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items) tömb azonosítja a megfelelő szerepelnie kell a tartalmát. Minden elem azonosításához az eredmény választ belül két lehetőséget biztosít.  
  
-   `answerType` és `resultIndex` – a `answerType` mező azonosítja a válasz (entitás vagy a hely) és `resultIndex` belül a válasz (például egy entitás) eredményeként azonosítja. Az index értéke nulla alapú.  
  
-   `value` – A `value` mezőben egy megfelelő választ vagy a válasz belül eredményeként az azonosítója. A válasz vagy az eredményeket az azonosítója, de nem mindkettőt tartalmaz.  
  
A azonosítójával meg kell egyeznie a rangsorolási-Azonosítójával az eredmények közül választ vagy azonosítójú. Ha egy válasz objektum tartalmaz egy `id` mezőben, a válasz eredmények együttes megjelenítése. Például ha a `Entities` objektum tartalmazza a `id` mezőbe az entitások cikkeket együttes megjelenítése. Ha a `Entities` objektum nem tartalmazza a `id` mezőben, majd minden egyes entitás tartalmaz egy `id` mező, és a rangsorolási válasz keveri az entitásokat a helyek eredmények.  
  
Használja a `answerType` és `resultIndex` egy kétlépéses folyamat. Először is használhat `answerType` azonosítására a választ, amely tartalmazza az eredmények megjelenítéséhez. Akkor használja `resultIndex` az adott válasz eredmények megjelenítéséhez eredmény index. (A `answerType` mezőjének neve a [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse) objektum.) Ha a válasz eredmények együttes megjelenítése elvárt befejezési rangsorolási válasz elem nem tartalmazza a a `resultIndex` mező.

## <a name="ranking-response-example"></a>Prioritás válasz – példa

A következő példáját mutatja be [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse).
  
```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "Jimi Hendrix"
  },
  "entities": { ... },
  "rankingResponse": {
    "sidebar": {
      "items": [
        {
          "answerType": "Entities",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.0"
          }
        },
        {
          "answerType": "Entities",
          "resultIndex": 1,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.1"
          }
        }
      ]
    }
  }
}
```

Ez rangsorolási a válasz alapján, az oldalsávon jeleníti meg, a két entitás eredmények Jimi Hendrix kapcsolódik.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Bing entitás keresési oktatóanyag](tutorial-bing-entities-search-single-page-app.md)
