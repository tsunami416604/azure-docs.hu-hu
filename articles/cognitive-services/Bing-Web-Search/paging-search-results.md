---
title: Az oldal keresési eredményeinek megjelenítése – Bing Search API-k
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan lapozhat a Bing Search API-k keresési eredményei között.
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
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73481730"
---
# <a name="how-to-page-through-results-from-the-bing-search-apis"></a>Az Bing Search API-k eredményeinek megjelenítése

A Bing web, Custom, képek, hírek vagy Video Search API-k hívásának elküldésekor a Bing a lekérdezéshez kapcsolódó összes eredmény részhalmazát adja vissza. A rendelkezésre álló találatok becsült teljes számának beszerzéséhez nyissa meg a válasz objektum `totalEstimatedMatches` mezőjét. 

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

A rendelkezésre álló találatok között a `count` és `offset` lekérdezési paramétereket használhatja a kérelem elküldésekor.  

> [!NOTE]
>
> * A Bing video, a képek és a News API-k lapozása csak az általános videó (`/video/search`), a hírek (`/news/search`) és a képek (`/image/search`) keresésekre vonatkozik. A lapozási témakörök és kategóriák nem támogatottak.  
> * A `TotalEstimatedMatches` mező az aktuális lekérdezés keresési eredményeinek teljes számát adja meg. A `count` és `offset` paraméterek beállításakor ez a becslés változhat.

| Paraméter | Leírás                                                                                                                                                                |
|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `count`   | Megadja a válaszban visszaadni kívánt eredmények számát. Vegye figyelembe, hogy a `count`alapértelmezett értéke, valamint a kérelmek maximális száma az API-tól függően változhat. Ezeket az értékeket a [következő lépések](#next-steps)szakaszban található hivatkozási dokumentációban találja. |
| `offset`  | A kihagyni kívánt eredmények számát adja meg. A `offset` nulla-alapú, és kisebbnek kell lennie, mint (`totalEstimatedMatches` - `count`).                                           |

Ha például 15 találat/oldal megjelenítését szeretné megjeleníteni, akkor az eredmények első oldalának beolvasásához állítsa a `count` értéket 15-re, és `offset` 0-ra. Minden további API-hívás esetén a `offset` 15-re növeli. Az alábbi példa 15 weblapot kér le a 45 eltolásnál kezdődően.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Ha az alapértelmezett `count` értéket használja, csak a `offset` lekérdezési paramétert kell megadnia az API-hívásokban.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

A Bing-rendszerkép és a videó API-k használatakor a `nextOffset` érték használatával elkerülhetők az ismétlődő keresési eredmények. Szerezze be az értéket a `Images` vagy `Videos` válasz objektumokból, és használja azt a `offset` paraméterrel rendelkező kérésekben.  

> [!NOTE]
> A Bing Web Search API olyan keresési eredményeket ad vissza, amelyek weblapokat, képeket, videókat és híreket is tartalmazhatnak. Ha a Bing Web Search API a keresési eredmények között lapoz át, [akkor csak a weblapok](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage)lapozása történik meg, más típusú válaszok, például képek vagy hírek. `WebPage` objektumok keresési eredményei tartalmazhatnak olyan eredményeket is, amelyek más válasz típusokban is megjelennek.
>
> Ha a `responseFilter` lekérdezési paramétert nem a szűrő értékének megadása nélkül használja, ne használja a `count` és a `offset` paramétereket. 

## <a name="next-steps"></a>További lépések

* [Mik a Bing Web Search API-k?](bing-api-comparison.md)
* [Bing Web Search API 7-es verzió – referencia](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
* [Bing Custom Search API v7-dokumentáció](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
* [Bing News Search API v7-dokumentáció](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)
* [Bing Video Search API v7-dokumentáció](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference)
* [Bing Image Search API v7-dokumentáció](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
