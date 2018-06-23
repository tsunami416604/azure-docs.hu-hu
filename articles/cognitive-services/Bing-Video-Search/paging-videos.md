---
title: Hogyan oldalon keresztül a rendelkezésre álló videók |} Microsoft Docs
description: Bemutatja, hogyan összes a Bing visszaadó videók lapon.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 910A485F-BCF3-42B9-958D-DD48BDEDA965
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 00476825eb3fc1008c3f2172b591d8b7a2f35884
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346906"
---
# <a name="paging-videos"></a>Lapozófájl-videók

A videó keresési API-t hívja, a Bing eredmények listáját adja vissza. A lista része a száma, amelyek kapcsolódnak a lekérdezés eredményeit. Ahhoz, hogy a rendelkezésre álló eredmények becsült száma, a válasz-objektum eléréséhez [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos-totalestimatedmatches) mező.  
  
Az alábbi példa azt mutatja meg a `totalEstimatedMatches` egy videó választ tartalmazó mezőt.  
  
```  
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56...",
    "totalEstimatedMatches" : 1000,
    "value" : [...]
}  
```  
  
Lapon keresztül a rendelkezésre álló videók, használja a [száma](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#count) és [eltolás](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#offset) lekérdezési paramétert.  
  
A `count` paraméter határozza meg a válaszban szereplő vissza eredmények száma. Az eredmények, amelyek a válaszban szereplő vonatkozó kérések maximális száma: 105. Az alapértelmezett 35 érték. A tényleges szám kézbesíteni lehet kisebb, mint a kért.

A `offset` paraméter határozza meg a kihagyandó eredmények számát. A `offset` Column, és kisebb, mint (`totalEstimatedMatches` - `count`).  
  
Ha 20 videók laponként megjelenítendő állíthat `count` 20 és `offset` 0 érték megadásával az első oldal az eredmények. Az összes azt követő lapon, akkor növelnie `offset` 20 (például 20, 40).  

A következő 20, 40 eltolástól kezdve videók kérő példáját mutatja be.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Ha az alapértelmezett `count` érték működik, a megvalósítás, csak meg kell adnia a `offset` lekérdezési paraméter.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Általában, ha egyszerre 35 videók, lapon, akkor kell állítania a `offset` lekérdezésparaméter 0-ra, az első kérésre, és ezután növelése `offset` minden későbbi kérés 35 által. Előfordulhat azonban, az eredményekről pedig a következő válasz némelyike az előző válaszban azonosak. Például a válaszban szereplő első két videók lehet ugyanaz, mint az előző válaszban az utolsó két videók.

Ismétlődő eredmények elkerülése érdekében, használja a [nextOffset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos-nextoffset) mezőjét a `Videos` objektum.

Például ha egyszerre 30 videók lapon, akkor állítania `count` 30 és `offset` 0-ra, az első kérelem. A következő kérés állíthat a `offset` lekérdezési paramétert a `nextOffset` érték.


> [!NOTE]
> Lapozófájl csak videók search (Keresés/videók /), nem pedig videó insights (részletek/videók /) vagy (/ videók/trendek) trendekkel videók vonatkozik.