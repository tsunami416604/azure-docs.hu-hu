---
title: A keresési eredmények lapozása – Bing keresési API-k
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogy miként lapozhat a keresési eredmények között a Bing Keresési API-kból.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: aahi
ms.openlocfilehash: ea883bb294a8769b3c9be1e0eafc2e3e7c811b48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73481730"
---
# <a name="how-to-page-through-results-from-the-bing-search-apis"></a>A Bing Keresési API-k találatai között való lapozás

Amikor hívást küld a Bing Web, egyéni, kép, hírek vagy videókeresési API-k, Bing visszaadja egy részhalmaza az összes találat, amely releváns lehet a lekérdezést. A rendelkezésre álló eredmények becsült teljes számának `totalEstimatedMatches` eléréséhez férjen hozzá a válaszobjektum mezőjéhez. 

Példa: 

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    }
}  
```

## <a name="paging-through-search-results"></a>Lapozás a keresési eredmények között

A rendelkezésre álló eredmények `count` lapozásához használja a lekérdezési `offset` paramétereket a kérés küldésekor.  

> [!NOTE]
>
> * A Bing Video, Image és News API-kkal történő`/video/search`lapozás`/news/search`csak az`/image/search`általános videó ( ), hírek ( ) és kép ( ) keresésre vonatkozik. A felkapott témakörök és kategóriák lapozása nem támogatott.  
> * A `TotalEstimatedMatches` mező az aktuális lekérdezés keresési eredményeinek teljes számát becsüli meg. Amikor beállítja `offset` a és a paramétereket, ez a `count` becslés változhat.

| Paraméter | Leírás                                                                                                                                                                |
|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `count`   | Megadja a válaszban visszaadandó eredmények számát. Vegye figyelembe, hogy `count`a alapértelmezett értéke és a legfeljebb kért eredmények száma API-nként változik. Ezeket az értékeket a [Következő lépések](#next-steps)című hivatkozási dokumentációban találja. |
| `offset`  | Megadja a kihagyandó eredmények számát. A `offset` nulla alapú, és kisebbnek`totalEstimatedMatches` - `count`kell lennie, mint ( ).                                           |

Ha például oldalanként 15 eredményt szeretne megjeleníteni, `count` akkor 15, `offset` 0-ra kell állítania az eredmények első oldalát. Minden további API-hívás esetén 15-tel növelné. `offset` A következő példa 15 weblapot kér a 45-ös eltolástól kezdődően.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Ha az alapértelmezett `count` értéket használja, csak `offset` meg kell adnia a lekérdezési paramétert az API-hívásokban.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

A Bing-kép- és videoAPI-k `nextOffset` használatakor az érték kelelkerülheti az ismétlődő keresési eredményeket. Az érték beszerezése a vagy `Images` `Videos` a válaszobjektumokból, és a paraméterekkel való felhasználás a `offset` kérelmekben.  

> [!NOTE]
> A Bing Web Search API olyan keresési eredményeket ad vissza, amelyek weblapokat, képeket, videókat és híreket tartalmazhatnak. Amikor a Bing Web Search API-ból lapozgat a keresési eredmények között, csak [weblapokat](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage)hív meg, más választípusokat, például képeket és híreket nem. Az objektumok `WebPage` keresési eredményei más választípusokban is megjelenhetnek.
>
> Ha a `responseFilter` lekérdezési paramétert szűrőértékek megadása nélkül használja, `offset` ne használja a és a `count` paramétereket. 

## <a name="next-steps"></a>További lépések

* [Mik azok a Bing Web Search API-k?](bing-api-comparison.md)
* [Bing Web Search API 7-es verzió – referencia](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
* [Bing egyéni keresési API v7-es hivatkozása](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
* [Bing News Search API v7-es hivatkozás](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)
* [Bing Video Search API v7-es hivatkozás](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference)
* [Bing Image Search API v7 hivatkozás](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
