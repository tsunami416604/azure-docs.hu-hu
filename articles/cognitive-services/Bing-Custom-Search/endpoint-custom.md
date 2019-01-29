---
title: Bing Custom Search-végpont
titlesuffix: Azure Cognitive Services
description: A Bing Custom Search API-végpont összefoglalása.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 12/05/2017
ms.author: v-gedod
ms.openlocfilehash: ed420676ddc4c83be93939874f2943126f9209e8
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55176624"
---
# <a name="custom-search"></a>Egyéni keresés
A Bing Custom Search szolgáltatással személyre szabott keresési funkciókat hozhat létre olyan témakörökhöz kapcsolódóan, amelyek jelentőséggel bírnak az Ön számára. A felhasználók az érdeklődési körükhöz igazított keresési eredményeket látnak ahelyett, hogy több oldalnyi irreleváns találatot kellene átnézniük.

## <a name="custom-search-endpoint"></a>Egyéni végpont
Az eredményeket a Bing Custom Search API az első, küldjön egy `GET` kérelem a következő végpont. A fejlécek és URL-paraméterek használatával további specifikációk meghatározása.

Végpont: Értéket ad vissza JSON eredményként, amely a felhasználói bevitel határozzák meg a keresési javaslatok `?q=""`.
```  
 GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search  
```

Ismerteti, hogyan állíthat be egyéni keresés források példákért lásd a [oktatóanyag](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/tutorials/custom-search-web-page). További információk a fejlécek, paraméterek, piaci kódok, válaszobjektumok, hibák stb, tekintse meg a [Bing Custom Search API 7-es verziója](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference) hivatkozás.

## <a name="custom-search-response-json"></a>Egyéni keresési válasz JSON
Egyéni keresési kérelem JSON-objektumként eredményeket ad vissza, lásd: [válaszobjektumok](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#response-objects). 

## <a name="custom-autosuggest"></a>Custom Autosuggest
Az egyéni automatikus kiegészítés API lehetővé teszi egy részleges keresési lekérdezés kifejezés küldeni a Bing, majd az konfigurálható javasolt lekérdezések listája. Az egyéni automatikus kiegészítés adja hozzá az API által visszaadott javaslatokat, és szükség esetén adja meg, hogy tartalmazza a Bing által létrehozott javaslatok.

## <a name="custom-autosuggest-endpoint"></a>Custom Autosuggest végpontja
Egyéni lekérdezési javaslatok kérése, egy GET kérelem küldése:

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions
```  

További információ a vonatkozó egyéni javaslatok meghatározása: [meghatározása egyéni keresési javaslatok](define-custom-suggestions.md).

## <a name="custom-image-search"></a>Egyéni Képkeresés
A Custom-Image Search API lehetővé teszi egy keresési lekérdezést küld a Bing és az egyéni keresési példány a vissza megfelelő rendszerképek listájának beolvasása.

## <a name="custom-image-search-endpoint"></a>Egyéni rendszerkép Search-végpont
A következő URL-címet GET kérést küldhet képek kérhet az egyéni keresési példány:

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/images/search
```

Egy egyéni keresési példány konfigurálásával kapcsolatos további információkért lásd: [konfigurálja az egyéni keresési funkciókat](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/define-your-custom-view).

## <a name="next-steps"></a>További lépések
A **Bing** API-kat támogatja a keresési műveleteket, amelyek a típusuk eredményeket adja vissza. Végpontok keresése az összes eredmény JSON-válasz objektumként adja vissza.  Minden végpontok lekérdezések támogatására, amelyeket egy adott nyelvhez és/vagy a helyet a földrajzi hosszúság, szélesség és keresési radius vissza.

Minden végpont által támogatott paraméterekkel kapcsolatos részletes információkért tekintse meg az egyes referenciaoldalait.
A Custom Search API-val alapszintű kérelmek példákért lásd [Custom Search használatának első lépései](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/)
