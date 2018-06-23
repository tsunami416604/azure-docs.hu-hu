---
title: 'Bing egyéni keresés: Lapon keresztül elérhető weblapok |} Microsoft Docs'
description: Bemutatja, hogyan lapon összes Bing lépjen vissza a weblapokat.
services: cognitive-services
author: brapel
manager: ehansen
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: f2f545a5a9195fc65515ea716f277723600cbb78
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346891"
---
# <a name="paging-webpages"></a>Lapozófájl szolgáltatásának konfigurálása 

Az egyéni keresési API-t hívja, a Bing eredmények listáját adja vissza. A lista, amely fontos lehet a lekérdezési eredmények száma részhalmaza. Ahhoz, hogy a rendelkezésre álló eredmények becsült száma, a válasz-objektum eléréséhez [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#totalestimatedmatches) mező.  
  
Az alábbi példa azt mutatja meg a `totalEstimatedMatches` mező, amely a Webes válasz tartalmazza.  
  
```  
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    }
}  
```  
  
Lapon keresztül a rendelkezésre álló weblapjait, használja a [száma](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#count) és [eltolás](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#offset) lekérdezési paramétert.  
  
A `count` paraméter határozza meg a válaszban szereplő vissza eredmények száma. Az eredmények, amelyek a válaszban szereplő vonatkozó kérések maximális száma érték az 50. Az alapértelmezett érték 10. A tényleges szám kézbesíteni lehet kisebb, mint a kért.

A `offset` paraméter határozza meg a kihagyandó eredmények számát. A `offset` Column, és kisebb, mint (`totalEstimatedMatches` - `count`).  
  
Ha 15 weblapok laponként megjelenítendő állíthat `count` 15 és `offset` 0 érték megadásával az első oldal az eredmények. Az összes azt követő lapon, akkor növelnie `offset` 15 (például 15, 30).  
  
A következő 45 eltolástól kezdve 15 weblapok kérő példáját mutatja be.  
  
```  
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: <subscription ID>
Host: api.cognitive.microsoft.com  
```  

Ha az alapértelmezett `count` érték működik, a megvalósítás, csak meg kell adnia a `offset` lekérdezési paraméter.  
  
```  
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: <subscription ID>  
Host: api.cognitive.microsoft.com  
```  

