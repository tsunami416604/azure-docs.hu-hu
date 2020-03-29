---
title: A válaszok megjelenítéséhez a rangsorolás használata - Bing entitáskeresés
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan használhatja a rangsorolást a Bing Entity Search API által visszaadott válaszok megjelenítéséhez.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 110cef117683b20170649a231226c8193496edf3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "68423920"
---
# <a name="using-ranking-to-display-entity-search-results"></a>Rangsorolás használata az entitás keresési eredményeinek megjelenítéséhez  

Minden entitáskeresési válasz tartalmaz egy [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse) választ, amely meghatározza, hogyan kell megjelenítenie a Bing Entity Search API által visszaadott keresési eredményeket. A rangsorolási válasz az eredményeket pólus, fővonal és oldalsáv tartalomba csoportosítja. A pólus eredménye a legfontosabb vagy kiemelkedő eredmény, és először meg kell jelennie. Ha a fennmaradó eredményeket nem hagyományos fővonali és oldalsávformátumban jeleníti meg, akkor a fővonal tartalmát jobban át kell adnia, mint az oldalsáv tartalmát. 
  
Az [Elemek](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items) tömb minden csoporton belül azonosítja azt a sorrendet, amelyben a tartalomnak meg kell jelennie. Minden elem két lehetőséget kínál az eredmény azonosítására a válaszon belül.  
 

|Mező | Leírás  |
|---------|---------|
|`answerType` és `resultIndex` | `answerType`azonosítja a választ (entitás vagy `resultIndex` hely), és azonosítja az eredmény az adott válaszban (például egy entitás). Az index 0-nál kezdődik.|
|`value`    | `value`Olyan azonosítót tartalmaz, amely megegyezik a válasz vagy a válaszon belüli eredmény azonosítójával. A válasz vagy az eredmények tartalmazzák az azonosítót, de mindkettőt nem. |
  
A `answerType` és `resultIndex` egy kétlépéses folyamat használata. Először `answerType` azonosítsa a megjelenítendő eredményeket tartalmazó választ. Ezután `resultIndex` indexelheti a válasz eredményeit, hogy az eredmény megjelenjen. (Az `answerType` érték a [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse) objektum mezőjének neve.) Ha a válasz összes eredményét együtt kell megjelenítenie, a rangsorolási `resultIndex` válasz elem nem tartalmazza a mezőt.

Az azonosító használatához a rangsorolási azonosítót egyeztetnie kell a válasz azonosítójával vagy annak egyik eredményével. Ha egy válaszobjektum `id` mezőt tartalmaz, jelenítse meg a válasz összes eredményét együtt. Ha például `Entities` az objektum `id` tartalmazza a mezőt, jelenítse meg az összes entitáscikket együtt. Ha `Entities` az objektum nem `id` tartalmazza a mezőt, akkor minden entitás tartalmaz egy mezőt, `id` és a rangsorolási válasz összekeveri az entitásokat a Helyek eredményekkel.  
  
## <a name="ranking-response-example"></a>Példa rangsorolásra adott válaszra

Az alábbiakban egy példa [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse).
  
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

A rangsorolási válasz alapján az oldalsáv a Jimi Hendrixhez kapcsolódó két entitás-eredményt jeleníti meg.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas webalkalmazás létrehozása](tutorial-bing-entities-search-single-page-app.md)
