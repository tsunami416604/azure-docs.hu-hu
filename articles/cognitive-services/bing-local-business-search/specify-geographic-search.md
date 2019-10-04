---
title: Földrajzi határok használata a Bing helyi üzleti keresési API eredményeinek szűréséhez
titleSuffix: Azure Cognitive Services
description: Ebből a cikkből megtudhatja, hogyan szűrheti a keresési eredményeket a Bing helyi üzleti keresési API-ból.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: 213457bc583494bbe039269b96b25990f7d0a961
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69906247"
---
# <a name="use-geographic-boundaries-to-filter-results-from-the-bing-local-business-search-api"></a>Földrajzi határok használata a Bing helyi üzleti keresési API eredményeinek szűréséhez

A Bing helyi üzleti keresési API lehetővé teszi, hogy a vagy `localCircularView` `localMapView` a lekérdezési paraméterek használatával a keresett földrajzi terület határain belül legyenek meghatározva. Ügyeljen arra, hogy csak egy paramétert használjon a lekérdezésekben. 

Ha a keresési kifejezés explicit földrajzi helyet tartalmaz, a Bing local Business API automatikusan azt fogja használni a keresési eredmények határainak megadásához. Ha például a keresési kifejezés `sailing in San Diego`, akkor `San Diego` a rendszer a lekérdezési paraméterekben és a felhasználói fejlécekben a hely és a többi megadott hely között fogja figyelmen kívül hagyni. 

Ha a keresési kifejezés nem észleli a földrajzi helyet, és nincs megadva földrajzi hely a lekérdezési paraméterek használatával, a Bing local Business Search API megkísérli meghatározni a kérés `X-Search-ClientIP` vagy `X-Search-Location` a fejlécek helyét. Ha egyik fejléc sincs megadva, az API meghatározza a kérelem ügyfél-IP-címének helyét, vagy a mobileszközök GPS-koordinátáit.

## <a name="localcircularview"></a>localCircularView

A `localCircularView` (z) paraméter egy kör alakú földrajzi területet hoz létre szélességi/hosszúsági koordináták köré, amelyet egy sugár határoz meg. Ha ezt a paramétert használja, a Bing helyi üzleti keresési API-tól érkező válaszok csak az ezen a körben `localMapView` belüli helyekre fognak kiterjedni, a paramétertől eltérően, amely a keresési területen kívüli helyet is tartalmazhat.

Körkörös földrajzi keresési terület megadásához válasszon egy szélességi és hosszúsági fokot, amely a kör középpontjának és egy méteres sugárnak a kiszolgálása. Ezt a paramétert ezután egy lekérdezési karakterlánchoz lehet hozzáfűzni, például `q=Restaurants&localCircularView=47.6421,-122.13715,5000`:.

Lekérdezés befejezése:

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search?q=restaurant&localCircularView=47.6421,-122.13715,5000&appid=0123456789ABCDEF&mkt=en-us&form=monitr
```

## <a name="localmapview"></a>localMapView

A `localMapView` paraméter egy téglalap alakú földrajzi területet határoz meg a kereséshez, amely két koordináta használatával határozza meg a Délkelet-és északnyugati sarkokat. Ha ezt a paramétert használja, a Bing helyi üzleti keresési API-tól érkező válaszok tartalmazhatnak a megadott területen kívüli, `localCircularView` de a paraméterrel nem rendelkező helyeket, amelyek csak a keresési területen belül találhatók meg.

Téglalap alakú keresési terület megadásához válasszon ki két szélességi/hosszúsági koordinátákat a határ délkeleti és északnyugati sarkába. Először a Délkelet-koordinátákat kell megadnia, ahogy az az alábbi `localMapView=47.619987,-122.181671,47.6421,-122.13715`példában is látható:.

Lekérdezés befejezése:

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search?q=restaurant&localMapView=47.619987,-122.181671,47.6421,-122.13715&appid=0123456789ABCDEF&mkt=en-us&form=monitr
```

## <a name="next-steps"></a>További lépések
- [Helyi üzleti keresés – Java rövid útmutató](quickstarts/local-search-java-quickstart.md)
- [Helyi üzleti keresés C# – rövid útmutató](quickstarts/local-quickstart.md)
- [Helyi üzleti keresési csomópont rövid útmutatója](quickstarts/local-search-node-quickstart.md)
- [Helyi üzleti keresés – Python rövid útmutató](quickstarts/local-search-python-quickstart.md)
