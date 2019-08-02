---
title: Az elérhető videók oldala – Bing Video Search
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan lapozhat a Bing Video Search API által visszaadott videókon.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: c3570d8772734595c6707ca8103006867a8eb47a
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68500709"
---
# <a name="paging-through-video-search-results"></a>Lapozás a videó keresési eredményei között

A Bing Video Search API a lekérdezéshez talált összes keresési eredmény egy részhalmazát adja vissza. Ezeknek az eredményeknek a lapozásával az API további hívásait követve beolvashatja és megjelenítheti azokat az alkalmazásban.

> [!NOTE]
> A lapozás csak a videók keresésére (/videos/Search) vonatkozik, és nem a videó-felismerési (/videos/details) és a trendek (/videos/trending) videókra.

## <a name="total-estimated-matches"></a>Becsült egyezések összesen

A talált keresési eredmények becsült számának beszerzéséhez használja a JSON-válasz [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos-totalestimatedmatches) mezőjét.   
  
```json  
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56...",
    "totalEstimatedMatches" : 1000,
    "value" : [...]
}  
```  
  
## <a name="paging-through-videos"></a>Lapozás videókon keresztül

A rendelkezésre álló videókon keresztüli lapon a kérés küldésekor használja a [Count](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#count) és az [eltolás](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#offset) lekérdezési paramétereket.  
  

|Paraméter  |Leírás  |
|---------|---------|
|`count`     | Megadja a válaszban visszaadni kívánt eredmények számát. A válaszban megadható eredmények maximális száma 100. Az alapértelmezett érték 10. A ténylegesen leszállított szám a kértnél kevesebb lehet.        |
|`offset`     | A kihagyni kívánt eredmények számát adja meg. A `offset` nulla-alapú, és kisebbnek kell lennie`totalEstimatedMatches`, mint ( - `count`).          |

Ha például 20 cikket szeretne megjeleníteni oldalanként, akkor az eredmények első oldalának beszerzéséhez 20 `count` és `offset` 0 értéket kell beállítania. Minden további lap esetében 20 (például 20 `offset` , 40) értékkel növelhető.  
  
Az alábbi példa 20 videót kér le, a 40 eltolástól kezdve.  
  
```cURL  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Ha a [Count](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#count)alapértelmezett értékét használja, csak a `offset` lekérdezési paramétert kell megadnia, ahogy az az alábbi példában is látható.  
  
```cURL  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Ha egyszerre 35-videókon keresztül lapozunk, a `offset` lekérdezési paramétert 0-ra kell állítania az első kérelemnél, majd a 35-es növekményt `offset` minden további kérelem esetében. A következő válasz egyes eredményei azonban tartalmazhatnak az előző válaszból származó, duplikált videós eredményeket. Előfordulhat például, hogy a válasz első két videója ugyanaz, mint az előző válasz utolsó két videója.

Az ismétlődő eredmények eltávolításához használja az [](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos-nextoffset) `Videos` objektum nextOffset mezőjét.

Ha például 30 videót szeretne egyszerre használni, beállíthatja, `count` hogy a 30 és `offset` a 0 értéket adja meg az első kérelemben. A következő kérelemben a `offset` lekérdezési paraméter `nextOffset` értékét állítsa be a értékre.

> [!NOTE]
> A `TotalEstimatedAnswers` mező az aktuális lekérdezéshez lekérhető keresési eredmények teljes számát adja meg.  A beállítás és `count` `offset` a paraméterek beállításakor `TotalEstimatedAnswers` a szám változhat. 
  
## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Videó-felismerések beolvasása](video-insights.md)
