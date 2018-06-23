---
title: Webes keresés végpont |} Microsoft Docs
description: A webes keresés API-végpont összegzését.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 11/28/2017
ms.author: v-gedod
ms.openlocfilehash: 72fbe1a0eb4379ad032e649f7299e37a0214adfb
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346975"
---
# <a name="web-search-endpoint"></a>Webes keresés végpont
A **webes keresés API** adja vissza a weblapokat, híreket, képek, videók, és [entitások](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Entitások egy személy, hely vagy a témakör összefoglaló információkat tartalmaznak.
## <a name="endpoint"></a>Végpont
Ahhoz, hogy a webes keresési eredmények között, a Bing API-val, küldjön egy `GET` kérelem a következő végponthoz. További specifikációk meghatározása a fejlécek és URL-cím paramétereket.

**Végpont**: beolvasása webes eredmények, amelyek fontos a felhasználó által definiált lekérdezés keresése `?q=""`.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```
Végpont: Fejlécek, a paraméterek, vonatkozó további információért piaci kódokat, válasz objektumok, hibák, stb., tekintse meg a [Bing Web API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference) hivatkozás.

## <a name="response-json"></a>Válasz JSON
Az egy webes keresés irányuló kérelemre adott válasz JSON-objektumként minden eredmény tartalmazza. Az eredmény elemzése szükséges eljárások, amelyek kezelik a minden típusú elemek. Tekintse meg a [oktatóanyag](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app) és [forráskód](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app-source) példákat.

## <a name="next-steps"></a>További lépések
A **Bing** API-k támogatják a keresési eredményeket a alapján műveleteket. Az összes keresési végpontok JSON-válasz objektumként adja vissza az eredményeket.  Minden végpontok egy adott nyelvhez és/vagy a hely által a földrajzi hosszúság értéke, a szélességi és a keresési radius visszaadó lekérdezések támogatja.

A végpontok által támogatott paraméterekkel kapcsolatos részletes információkért lásd: az egyes referencia oldalak.
A webes keresés API alapvető kérelmek, tekintse meg a [keresése a webes Quick elindul](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/search-the-web).