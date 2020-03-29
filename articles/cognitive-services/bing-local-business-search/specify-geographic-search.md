---
title: Földrajzi határok használata az eredmények szűréséhez a Bing helyi vállalati keresés API-ból
titleSuffix: Azure Cognitive Services
description: Ebből a cikkből megtudhatja, hogyan szűrheti a keresési eredményeket a Bing Helyi vállalati keresési API-ból.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: 213457bc583494bbe039269b96b25990f7d0a961
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "69906247"
---
# <a name="use-geographic-boundaries-to-filter-results-from-the-bing-local-business-search-api"></a>Földrajzi határok használata az eredmények szűréséhez a Bing helyi vállalati keresés API-ból

A Bing Helyi üzleti keresés API lehetővé teszi, hogy a kívánt földrajzi terület `localCircularView` `localMapView` határait a vagy a lekérdezési paraméterek használatával állítsa be. Ügyeljen arra, hogy csak egy paramétert használjon a lekérdezésekben. 

Ha egy keresési kifejezés explicit földrajzi helyet tartalmaz, a Bing helyi üzleti API automatikusan használja azt a keresési eredmények határainak beállításához. Ha például a keresett `sailing in San Diego`kifejezés `San Diego` a , akkor a rendszer helyként fogja használni, és a lekérdezési paraméterekben vagy a felhasználói fejlécekben megadott bármely más helyet figyelmen kívül hagyja a rendszer. 

Ha a keresési kifejezésnem észlel földrajzi helyet, és nincs megadva földrajzi hely a lekérdezési paraméterek használatával, a Bing helyi `X-Search-ClientIP` `X-Search-Location` üzleti keresési API megpróbálja meghatározni a helyét a kérelemből vagy a fejlécekből. Ha egyik fejléc sincs megadva, az API a kérelem ügyfél IP-címéből, vagy a mobileszközök GPS-koordinátáiból határozza meg a helyet.

## <a name="localcircularview"></a>localCircularView

A `localCircularView` paraméter kör alakú földrajzi területet hoz létre egy sugár által meghatározott szélességi/hosszúsági koordináták köré. Ha ezt a paramétert használja, a Bing Helyi üzleti keresés API `localMapView` válaszai csak a körön belüli helyeket tartalmazzák, ellentétben azzal a paraméterrel, amely a keresési területen kívül rekedhet.

Kör alakú földrajzi keresési terület megadásához válasszon egy szélességi és hosszúsági fokot, amely a kör középpontjaként szolgál, és egy sugarat méterben. Ez a paraméter ezután hozzáfűzhető egy `q=Restaurants&localCircularView=47.6421,-122.13715,5000`lekérdezési karakterlánchoz, például: . .

Teljes lekérdezés:

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search?q=restaurant&localCircularView=47.6421,-122.13715,5000&appid=0123456789ABCDEF&mkt=en-us&form=monitr
```

## <a name="localmapview"></a>localMapView nézet

A `localMapView` paraméter egy téglalap alakú földrajzi területet ad meg a kereséshez, két koordináta-készlet használatával a délkeleti és északnyugati sarkok megadásához. Ha ezt a paramétert használja, a Bing Helyi üzleti keresés API-ból érkező válaszok `localCircularView` tartalmazhatnak a megadott területen belüli és azon kívüli helyeket, ellentétben a paraméterrel, amely csak a keresési területen belüli helyeket tartalmazza.

Téglalap alakú keresési terület megadásához válasszon két szélességi/hosszúsági koordinátát, amely a határ délkeleti és északnyugati sarkaiként szolgál. Ügyeljen arra, hogy először a délkeleti koordinátákat határozza meg, ahogy a következő példában: `localMapView=47.619987,-122.181671,47.6421,-122.13715`.

Teljes lekérdezés:

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search?q=restaurant&localMapView=47.619987,-122.181671,47.6421,-122.13715&appid=0123456789ABCDEF&mkt=en-us&form=monitr
```

## <a name="next-steps"></a>További lépések
- [Helyi üzleti keresés Java rövid útmutató](quickstarts/local-search-java-quickstart.md)
- [Helyi üzleti keresés C# Rövid útmutató](quickstarts/local-quickstart.md)
- [Rövid útmutató a helyi üzleti keresőcsomópontról](quickstarts/local-search-node-quickstart.md)
- [Helyi üzleti keresés Python rövid útmutató](quickstarts/local-search-python-quickstart.md)
