---
title: Oldalakra keresztül a rendelkezésre álló videók – Bing Videókeresés
titlesuffix: Azure Cognitive Services
description: Útmutató a videókat a Bing Videókeresési API által visszaadott összes lapon.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: 0af36fa68b2d801eed52e6f081b040fb56929c91
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58101312"
---
# <a name="paging-through-video-search-results"></a>Videó keresési eredmények lapozása

A Bing Videókeresési API talált a lekérdezés összes keresési eredmény egy részhalmazát adja vissza. Ezeket az eredményeket a későbbi API-hívás átlapozva által lekérése, és az alkalmazásban megjeleníteni azokat.

> [!NOTE]
> Lapozófájl csak videók search (Keresés/videók /), és nem feltárását (/ videók/részletei) vagy a felkapott videók (/ videókat és népszerű) vonatkozik.

## <a name="total-estimated-matches"></a>Becsült teljes egyezés

Becsült száma található keresési eredmények lekéréséhez használja a [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos-totalestimatedmatches) mezőt a JSON-válaszban.   
  
```json  
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56...",
    "totalEstimatedMatches" : 1000,
    "value" : [...]
}  
```  
  
## <a name="paging-through-videos"></a>Videók lapozása

Oldalon keresztül a rendelkezésre álló videók, használja a [száma](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#count) és [eltolás](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#offset) lekérdezési paramétert a kérés küldésekor.  
  

|Paraméter  |Leírás  |
|---------|---------|
|`count`     | A válaszban visszaadott eredmények számát adja meg. Az eredmények, amelyek a válaszban vonatkozó kérések maximális száma pedig a 100. Az alapértelmezett érték 10. A tényleges szám i lehet kisebb, mint a kért.        |
|`offset`     | Megadja a kihagyandó eredmények száma. A `offset` nulláról induló, és lehet kisebb, mint (`totalEstimatedMatches` - `count`).          |

Például, ha meg szeretné jeleníteni a oldalanként 20 cikkeket, így állíthatja be `count` 20-ra és `offset` 0 beolvasni az eredmények első oldala. Minden ezt követő laphoz, akkor növelni `offset` 20 (például 20, 40).  
  
Az alábbi példa lekéri 20 videók, 40 eltolástól kezdve.  
  
```cURL  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Ha az alapértelmezett érték a [száma](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#count), csak meg kell adnia a `offset` lekérdezési paraméter, a következő példához hasonlóan.  
  
```cURL  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Ha oldalon keresztül 35 videók egyszerre, így állíthatja be a `offset` lekérdezési paraméter az első kérés a 0-ra, és ezután növelje `offset` minden későbbi kérés a 35 szerint. Azonban néhány eredmények a következő válasz tartalmazhat ismétlődő videó eredményeit az előző válaszban kapott. Ha például az első két videók, a válasz lehet ugyanaz, mint az előző válaszban kapott utolsó két videóit.

Az ismétlődések számára, használja a [nextOffset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos-nextoffset) mezőjében a `Videos` objektum.

Például, ha szeretné egyszerre 30 videók lapon, beállíthat `count` 30 és `offset` az első kérés a 0. A következő kérés, így állíthatja be a `offset` lekérdezési paraméter, a `nextOffset` értéket.

> [!NOTE]
> A `TotalEstimatedAnswers` mező elszámolás kérheti le az aktuális lekérdezés keresési eredmények teljes száma.  Ha `count` és `offset` paraméterek, a `TotalEstimatedAnswers` száma változhat. 
  
## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Videó elemzések lekérése](video-insights.md)
