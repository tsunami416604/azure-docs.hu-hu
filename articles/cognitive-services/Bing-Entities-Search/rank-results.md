---
title: Használatával a ranglistán megjelenő válaszok – Bing Entity Search megjelenítése
titlesuffix: Azure Cognitive Services
description: Ismerje meg, hogyan ranglistán megjelenő használatával megjelenítheti a Bing Entity Search API adja vissza a válaszokat.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 56215bfa17343576b6bebec3a5dc5076ac56073c
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55754132"
---
# <a name="using-ranking-to-display-entity-search-results"></a>Használatával a ranglistán megjelenő entitás keresési eredmények megjelenítése  

Minden entitás keresési válasz tartalmazza a [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse) választ, amely meghatározza, hogy meg kell jelenítenie a Bing Entity Search API által visszaadott találatok. Ennek a területnek válasz pole által, az eredmények és az oldalsáv tartalom csoportosítja. Pole eredménye a leginkább fontos vagy neves eredménye, és először üzenetnek kell megjelennie. Ha nem jelennek meg a fennmaradó eredményez a hagyományos által és az oldalsáv formátumát, meg kell adnia által tartalom magasabb látható-e, mint az oldalsáv tartalmat. 
  
Minden egyes csoporton belül a [elemek](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items) tömb azonosítja a rendelést, szerepelnie kell a tartalmat. Minden elem azonosítására, az eredmény belül választ kétféle módszert biztosít.  
 

|Mező | Leírás  |
|---------|---------|
|`answerType` és `resultIndex` | `answerType` a válasz (entitás vagy helyet) azonosítja és `resultIndex` azonosítja az adott válasz (például egy entitás) belül eredményt. Az index kezdődik, 0.|
|`value`    | `value` Egy Azonosítót, amely megfelel a válasz vagy válasz belül eredményt Azonosítóját tartalmazza. Vagy a választ, vagy az eredmények tartalmazzák az azonosítója, de nem mindkettőt. |
  
Használatával a `answerType` és `resultIndex` egy kétlépéses folyamat. Először `answerType` azonosíthatja a választ, amely tartalmazza az eredmények megjelenítéséhez. Ezután `resultIndex` index adott válasz találatok megjelenítéséhez eredmény eléréséhez. (A `answerType` a neve, a mező a [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse) objektum.) Ön kellene együtt a válasz eredményeket megjeleníteni, ha a rangsorolás válasz elem nem tartalmazza a `resultIndex` mező.

Az azonosító használatával megköveteli, hogy egyezik meg ennek a területnek Azonosítóval azonosítójú, válasz vagy az eredmények közül. Ha egy válasz objektum tartalmaz egy `id` mezőben együtt a válasz eredmények megjelenítéséhez. Például ha a `Entities` objektum tartalmazza a `id` mezőt, és megjelenítheti az összes entitások cikk együtt. Ha a `Entities` objektum nem tartalmaz a `id` mezőben, majd minden egyes entitás tartalmaz egy `id` mező, és a rangsorolás válasz eredményét az entitásokat a helyek eredményekkel.  
  
## <a name="ranking-response-example"></a>Példa a válasz rangsorolása

Az alábbi példán látható [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse).
  
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

Ez ennek a területnek a válasz alapján, az oldalsáv jeleníti meg, a két entitás eredmények Jimi Hendrix kapcsolatos.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas webalkalmazás létrehozása](tutorial-bing-entities-search-single-page-app.md)
