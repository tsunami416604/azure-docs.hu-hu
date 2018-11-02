---
title: A helyi vállalati keresési Bing-API az eredmények szűréséhez használja a földrajzi határokon |} A Microsoft Docs
titleSuffix: Azure Cognitive Services
description: Ez a cikk segítségével megtudhatja, hogyan a helyi vállalati keresési Bing-API az eredmények szűréséhez.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-local-business
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 246b0d3f2edcf941a49e7e57043225ff4c276064
ms.sourcegitcommit: 6678e16c4b273acd3eaf45af310de77090137fa1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2018
ms.locfileid: "50748662"
---
# <a name="use-geographic-boundaries-to-filter-results-from-the-bing-local-business-search-api"></a>A helyi vállalati keresési Bing-API származó eredmények szűréséhez használja a földrajzi korlátok

A helyi vállalati keresési Bing-API lehetővé teszi, hogy állítson be határok az adott földrajzi területen, a keresés szeretné a `localCircularView` vagy `localMapView` lekérdezési paramétereket. Győződjön meg arról, a lekérdezések csak egy paraméter használatáról. 

Ha egy keresési kifejezést tartalmaz egy explicit földrajzi hely, a Bing helyi üzleti API automatikusan, segítségével állítsa be a keresési eredmények határait. Például, ha a keresési kifejezés `sailing in San Diego`, majd `San Diego` lesz használva a helyet, és minden egyéb megadott helyeket a lekérdezési paraméterek vagy a felhasználó fejlécek figyelmen kívül. 

Ha egy földrajzi helye nem észlelhető a keresési kifejezést, és nincs földrajzi hely van megadva, a lekérdezési paraméterek használatával, a helyi vállalati keresési Bing-API megpróbálja meghatározni a hely, ahonnan a kérés `X-Search-ClientIP` vagy `X-Search-Location` fejlécek. Ha sem a fejléc van megadva, az API a kérelem vagy az ügyfél IP-címről helyet határozza meg, vagy GPS koordinálja a mobileszközökhöz.

## <a name="localcircularview"></a>localCircularView

A `localCircularView` paraméter hoz létre a szélességi és hosszúsági koordinátákkal koordináták, a radius által meghatározott készletét körül. kör alakú földrajzi területen. Használja ezt a paramétert, ha a helyi vállalati keresési Bing-API érkező válaszokat csak tartalmazza a kör helyére ellentétben a `localMapView` paraméter, amely valamivel a keresési területen kívül helyek.

Egy kör alakú földrajzi keresés területen megadásához válasszon ki egy szélességi és hosszúsági szolgálhat a körre, és a egy radius méterben közepén. Ez a paraméter majd fűzhető egy lekérdezési karakterláncot, például: `q=Restaurants&localCircularView=47.6421,-122.13715,5000`.

Teljes lekérdezés:

````
https://www.bingapis.com/api/v7/localbusinesses/search?q=restaurant&localCircularView=47.6421,-122.13715,5000&appid=0123456789ABCDEF&mkt=en-us&form=monitr
````

## <a name="localmapview"></a>localMapView

A `localMapView` paraméter adja meg szeretne keresni, egy téglalap alakú földrajzi területen adja meg a Ausztrália és Északnyugat sarkok két készletnyi koordináták segítségével. Ez a paraméter használata esetén a helyi vállalati keresési Bing-API érkező válaszokat tartalmazhatnak helyeket belül, és csak a megadott területen kívül eltérően a `localCircularView` paraméter, amely csak a helyek, a keresési területen belül tartalmazza.

Szeretne megadni egy téglalap alakú keresőmezőt, válassza ki a szélességi és hosszúsági koordinátákkal koordinátái a Ausztrália egyikükön két készletnyi és a határ Északnyugat sarkok. Ügyeljen arra, hogy délkeleti koordinátáit először az alábbi példában látható módon adja meg: `localMapView=47.619987,-122.181671,47.6421,-122.13715`.

Teljes lekérdezés:

````
https://www.bingapis.com/api/v7/localbusinesses/search?q=restaurant&localMapView=47.619987,-122.181671,47.6421,-122.13715&appid=0123456789ABCDEF&mkt=en-us&form=monitr
````

## <a name="next-steps"></a>További lépések
- [Helyi vállalati keresési Java rövid útmutató](quickstarts/local-search-java-quickstart.md)
- [Helyi vállalati keresési C# a rövid útmutató](quickstarts/local-quickstart.md)
- [Helyi vállalati keresési Node-Quickstart](quickstarts/local-search-node-quickstart.md)
- [Helyi üzleti keresési Python a rövid útmutató](quickstarts/local-search-python-quickstart.md)
