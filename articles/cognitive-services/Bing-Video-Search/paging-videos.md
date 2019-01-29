---
title: Oldalakra keresztül a rendelkezésre álló videók – Bing Videókeresés
titlesuffix: Azure Cognitive Services
description: Bemutatja, hogyan lapon végig az összes a videókat a Bing adhatnak vissza.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 26e706b054653b8f0ad1ea14d0a9c2ca97cd227f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55181792"
---
# <a name="paging-videos"></a>Lapozófájl-videók

Amikor meghívja a Video Search API-t, a Bing visszaad egy eredménylistát. A lista a lekérdezéshez kapcsolódó összes találat egy részét tartalmazza. Az első rendelkezésre álló válaszokat becsült teljes száma, a válasz-objektum eléréséhez [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos-totalestimatedmatches) mező.  
  
A következő példa bemutatja a `totalEstimatedMatches` mező, amely egy videó válasz tartalmazza.  
  
```  
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56...",
    "totalEstimatedMatches" : 1000,
    "value" : [...]
}  
```  
  
Oldalon keresztül a rendelkezésre álló videók, használja a [száma](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#count) és [eltolás](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#offset) lekérdezési paramétereket.  
  
A `count` paraméter adja meg a válaszban visszaadott eredmények száma. Az eredmények, amelyek a válaszban vonatkozó kérések maximális számát 105. Az alapértelmezett érték 35. A tényleges szám i lehet kisebb, mint a kért.

A `offset` paraméter adja meg a kihagyandó eredmények száma. A `offset` nulláról induló, és lehet kisebb, mint (`totalEstimatedMatches` - `count`).  
  
Ha meg szeretné jeleníteni a oldalanként 20 videókat, így állíthatja `count` 20-ra és `offset` 0 beolvasni az eredmények első oldala. Minden ezt követő laphoz, akkor növelni `offset` 20 (például 20, 40).  

Az alábbiakban látható egy példa, amely 20, 40 eltolástól kezdve videók kér.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Ha az alapértelmezett `count` érték a megvalósítás esetében működik, csak meg kell adnia a `offset` lekérdezési paraméter.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Általában, ha egyszerre 35 videók lap, így állíthatja be a `offset` lekérdezési paraméter az első kérés a 0-ra, és ezután növelje `offset` minden későbbi kérés a 35 szerint. Azonban néhány, az eredmények a következő válasz többször is előfordulhatnak az előző válasz. Például az első két videók, a válasz lehet ugyanaz, mint az előző válaszban kapott utolsó két videóit.

Az ismétlődések számára, használja a [nextOffset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos-nextoffset) mezőjében a `Videos` objektum.

Például, ha szeretné 30 videók lapon egyszerre, így állíthatja be `count` 30 és `offset` az első kérés a 0. A következő kérés, így állíthatja be a `offset` lekérdezési paraméter, a `nextOffset` értéket.


> [!NOTE]
> Lapozófájl csak videók search (Keresés/videók /), és nem feltárását (/ videók/részletei) vagy a felkapott videók (/ videókat és népszerű) vonatkozik.

> [!NOTE]
> A `TotalEstimatedAnswers` mező elszámolás kérheti le az aktuális lekérdezés keresési eredmények teljes száma.  Ha `count` és `offset` paraméterek, a `TotalEstimatedAnswers` száma változhat. 
