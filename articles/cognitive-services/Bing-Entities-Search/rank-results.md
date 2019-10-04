---
title: Válaszok megjelenítése a rangsor használatával – Bing Entity Search
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan használható a rangsor a Bing Entity Search API által visszaadott válaszok megjelenítéséhez.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 110cef117683b20170649a231226c8193496edf3
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423920"
---
# <a name="using-ranking-to-display-entity-search-results"></a>Rangsorolás használata az entitások keresési eredményeinek megjelenítéséhez  

Minden entitás keresési válasza tartalmaz egy [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse) választ, amely meghatározza, hogyan kell megjeleníteni a Bing Entity Search API által visszaadott keresési eredményeket. A rangsorolási válaszok a pole, a fővonal és a Sidebar tartalmát eredményezik. A pole eredmény a legfontosabb vagy legjelentősebb eredmény, és elsőként kell megjelennie. Ha nem jeleníti meg a fennmaradó eredményeket egy hagyományos fővonal-és oldalsáv-formátumban, meg kell adnia a fővonali tartalmat, mint az oldalsáv tartalma. 
  
Az Items Array az [](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items) egyes csoportokon belül azonosítja azt a sorrendet, amelyben a tartalomnak szerepelnie kell. Mindegyik elem két módszert biztosít az eredmény azonosítására a válaszon belül.  
 

|Mező | Leírás  |
|---------|---------|
|`answerType` és `resultIndex` | `answerType`azonosítja a választ (vagy az entitást vagy a `resultIndex` helyet), és azonosítja a válaszon belüli eredményt (például egy entitást). Az index 0 karakternél kezdődik.|
|`value`    | `value`Egy olyan azonosítót tartalmaz, amely a válasz vagy eredmény AZONOSÍTÓjának felel meg a válaszon belül. Vagy a válasz vagy az eredmény tartalmazza az azonosítót, de nem mindkettőt. |
  
A `answerType` és`resultIndex` a két lépésből álló folyamat. Először a használatával `answerType` azonosíthatja a megjelenítendő eredményeket tartalmazó választ. Ezután a `resultIndex` paranccsal indexelheti a válasz eredményét, hogy megjelenjen az eredmény. (Az `answerType` érték a mező neve a [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse) objektumban.) Ha azt szeretné, hogy a válasz összes eredménye együtt jelenjen meg, a rangsorolási válasz elem nem tartalmazza `resultIndex` a mezőt.

Az azonosító használatához meg kell egyeznie a rangsor azonosítójának és a válasz AZONOSÍTÓjának, vagy az egyik eredményének. Ha egy válasz objektum tartalmaz egy `id` mezőt, a válasz összes eredményét együtt jeleníti meg. Ha például az `Entities` objektum tartalmazza a `id` mezőt, az összes entitást együtt jeleníti meg. Ha az `Entities` objektum nem tartalmazza a `id` mezőt, akkor minden entitás tartalmaz egy `id` mezőt, és a rangsorolási válasz összekeveri az entitásokat a helyek eredményeivel.  
  
## <a name="ranking-response-example"></a>Rangsorolási válasz példája

A következő példa egy [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse)mutat be.
  
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

Ezen rangsorolási válasz alapján az oldalsáv megjeleníti a Jimi Hendrix-hez kapcsolódó két entitás eredményét.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas webalkalmazás létrehozása](tutorial-bing-entities-search-single-page-app.md)
