---
title: Hogyan oldalon keresztül elérhető rendszerkép |} Microsoft Docs
description: Bemutatja, hogyan összes Bing lépjen vissza a lemezkép lapon.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 3C8423F8-41E0-4F89-86B6-697E840610A7
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: a74ee817e84be5bb563c5fdaf25afc1dc14732e5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346875"
---
# <a name="paging-results"></a>Lapozás eredményei

Hívja a lemezkép keresési API-t, a Bing eredmények listáját adja vissza. A lista része a száma, amelyek kapcsolódnak a lekérdezés eredményeit. Ahhoz, hogy a rendelkezésre álló eredmények becsült száma, a válasz-objektum eléréséhez [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#totalestimatedmatches) mező.  
  
Az alábbi példa azt mutatja meg a `totalEstimatedMatches` képek választ tartalmazó mezőt.  
  
```json
{
    "_type" : "Images",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=73118C8...",
    "totalEstimatedMatches" : 838,
    "value" : [...]  
}  
```  
  
Lapon keresztül elérhető rendszerkép, használja a [száma](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#count) és [eltolás](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#offset) lekérdezési paramétert.  
  
A `count` paraméter határozza meg a válaszban szereplő vissza eredmények száma. Az eredmények, amelyek a válaszban szereplő vonatkozó kérések maximális száma: 150. Az alapértelmezett 35 érték. A tényleges szám kézbesíteni lehet kisebb, mint a kért.

A `offset` paraméter határozza meg a kihagyandó eredmények számát. A `offset` Column, és kisebb, mint (`totalEstimatedMatches` - `count`).  
  
Ha 20 képek laponként megjelenítendő állíthat `count` 20 és `offset` 0 érték megadásával az első oldal az eredmények. A következő 20, 40 eltolástól kezdve képek kérő példáját mutatja be.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Ha az alapértelmezett `count` érték működik, a megvalósítás, csak meg kell adnia a `offset` lekérdezési paraméter.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  
  
Várt, hogy ha 35 képek egyszerre történő lap állíthat a `offset` lekérdezésparaméter 0-ra, az első kérésre, és ezután növelése `offset` minden későbbi kérés 35 által. Előfordulhat azonban, az eredményekről pedig a következő válasz némelyike az előző válaszban azonosak. Például az első két lemezképeket a válaszban szereplő lehet ugyanaz, mint az előző válaszban az utolsó két lemezképet.

Ismétlődő eredmények elkerülése érdekében, használja a [nextOffset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#nextoffset) mezőjét a `Images` objektum. A `nextOffset` mező jelzi, hogy a `offset` a következő kérés esetén használandó. Például, ha azt szeretné, 30 lemezképek lapra egyszerre, akkor állítania `count` 30 és `offset` 0-ra, az első kérelem. A következő kérés állíthat `count` 30 és `offset` az előző válaszban értékének `nextOffset`. Visszafelé lapra, javasoljuk, hogy az előző eltolások Jegyzettömb karbantartásáért, valamint a legutóbbi popping.

> [!NOTE]
> Lapozófájl csak lemezkép search (Keresés/képek /), és nem kép insights vagy trendekkel képek (/ képek/trendek) vonatkozik.