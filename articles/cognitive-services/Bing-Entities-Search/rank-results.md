---
title: Használatával a ranglistán megjelenő válaszok – Bing Entity Search megjelenítése
titlesuffix: Azure Cognitive Services
description: Bemutatja, hogyan ranglistán megjelenő használatával megjelenítheti a Bing Entity Search API adja vissza a válaszokat.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: aahi
ms.openlocfilehash: f0cae32acf2db62a5d3c060ea944b1131252beda
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55195919"
---
# <a name="using-ranking-to-display-results"></a>Eredmények megjelenítése a ranglistán megjelenő használatával  

Minden entitás keresési válasz tartalmazza a [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse) választ, hasonlóan egy, a Bing webes keresés választ, amely megadja, hogyan kell a keresési eredmények megjelenítéséhez. Ennek a területnek válasz pole által, az eredmények és az oldalsáv tartalom csoportosítja. Pole eredménye a leginkább fontos vagy neves eredménye, és először üzenetnek kell megjelennie. Ha nem jelennek meg a fennmaradó eredményez a hagyományos által és az oldalsáv formátumát, meg kell adnia által tartalom magasabb látható-e, mint az oldalsáv tartalmat. 
  
Minden egyes csoporton belül a [elemek](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items) tömb azonosítja a rendelést, szerepelnie kell a tartalmat. Minden elem azonosítására, az eredmény belül választ kétféle módszert biztosít.  
  
-   `answerType` és `resultIndex` – a `answerType` mező azonosítja a választ (entitás vagy a hely) és `resultIndex` azonosítja egy eredményt a válasz (például egy entitás) belül. Az index nulla alapú.  
  
-   `value` – A `value` mező, amely a válasz vagy válasz belül eredményt azonosítója megegyezik-Azonosítót tartalmaz. Vagy a választ, vagy az eredmények tartalmazzák az azonosítója, de nem mindkettőt.  
  
Az azonosító használatával megköveteli, hogy egyezik meg ennek a területnek Azonosítóval azonosítójú, válasz vagy az eredmények közül. Ha egy válasz objektum tartalmaz egy `id` mezőben együtt a válasz eredmények megjelenítéséhez. Például ha a `Entities` objektum tartalmazza a `id` mezőt, és megjelenítheti az összes entitások cikk együtt. Ha a `Entities` objektum nem tartalmaz a `id` mezőben, majd minden egyes entitás tartalmaz egy `id` mező, és a rangsorolás válasz eredményét az entitásokat a helyek eredményekkel.  
  
Használatával a `answerType` és `resultIndex` egy kétlépéses folyamat. Első lépésként használhat `answerType` azonosíthatja a választ, amely tartalmazza az eredmények megjelenítéséhez. Ezután használhatja `resultIndex` index adott válasz találatok megjelenítéséhez eredmény eléréséhez. (A `answerType` a neve, a mező a [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse) objektum.) Ön kellene együtt a válasz eredményeket megjeleníteni, ha a rangsorolás válasz elem nem tartalmazza a `resultIndex` mező.

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
> [A Bing Entity Search-oktatóanyag](tutorial-bing-entities-search-single-page-app.md)
