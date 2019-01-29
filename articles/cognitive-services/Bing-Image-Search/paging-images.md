---
title: A lemezképek, a Bing Image Search API által visszaadott keresztül lap
titleSuffix: Azure Cognitive Services
description: Váltás a lemezképek, a Bing Image Search API által visszaadott különböző oldalain.
services: cognitive-services
author: swhite-msft
manager: cgonlun
ms.assetid: 3C8423F8-41E0-4F89-86B6-697E840610A7
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: e9eb67cff82ce8c52684d523be7c662c5a2cbb40
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55160322"
---
# <a name="page-through-the-images-results"></a>A lemezképek eredmények lap

A képkeresési API hívásakor a Bing eredmények listáját adja vissza. A lista a lekérdezéshez kapcsolódó összes találat egy részét tartalmazza. Az első rendelkezésre álló válaszokat becsült teljes száma, a válasz-objektum eléréséhez [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#totalestimatedmatches) mező.  

A következő példa bemutatja a `totalEstimatedMatches` lemezképek választ tartalmazó mezőt.  

```json
{
    "_type" : "Images",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=73118C8...",
    "totalEstimatedMatches" : 838,
    "value" : [...]  
}  
```  

A lapon az elérhető rendszerképek keresztül, használja a [száma](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#count) és [eltolás](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#offset) lekérdezési paramétereket.  

A `count` paraméter adja meg a válaszban visszaadott eredmények száma. Az eredmények, amelyek a válaszban vonatkozó kérések maximális számát 150. Az alapértelmezett érték 35. A tényleges szám i lehet kisebb, mint a kért.

A `offset` paraméter adja meg a kihagyandó eredmények száma. A `offset` nulláról induló, és lehet kisebb, mint (`totalEstimatedMatches` - `count`).  

Ha meg szeretné jeleníteni a 20 rendszerképek egy-egy lapon, akkor állítania `count` 20-ra és `offset` 0 beolvasni az eredmények első oldala. Az alábbiakban látható egy példa, amely 20, 40 eltolástól kezdve lemezképet kér.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Ha az alapértelmezett `count` érték a megvalósítás esetében működik, csak meg kell adnia a `offset` lekérdezési paraméter.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Így várhatóan egyszerre 35 lemezképek lapon, ha szeretné beállítani a `offset` lekérdezési paraméter az első kérés a 0-ra, és ezután növelje `offset` minden későbbi kérés a 35 szerint. Azonban néhány, az eredmények a következő válasz többször is előfordulhatnak az előző válasz. Például az első két lemezképek, a válasz lehet ugyanaz, mint az előző válaszban kapott utolsó két képeit.

Az ismétlődések számára, használja a [nextOffset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#nextoffset) mezőjében a `Images` objektum. A `nextOffset` mező jelzi, hogy a `offset` , a következő kérésnél használja. Például, ha azt szeretné, 30 lemezképek lapra egyszerre, így állíthatja be `count` 30 és `offset` az első kérés a 0. A következő kérés, így állíthatja `count` 30 és `offset` az előző válaszban kapott értékéhez `nextOffset`. A visszafelé lapon, javasoljuk, hogy az előző eltolások Jegyzettömb karbantartásáért, valamint a legutóbbi popping.

> [!NOTE]
> Lapozófájl a csak lemezkép search (Keresés/képek /), és nem hasznos képadatok vagy a felkapott képek (/ képek/népszerű) vonatkozik.

> [!NOTE]
> A `TotalEstimatedAnswers` mező elszámolás kérheti le az aktuális lekérdezés keresési eredmények teljes száma.  Ha `count` és `offset` paraméterek, a `TotalEstimatedAnswers` száma változhat. 
