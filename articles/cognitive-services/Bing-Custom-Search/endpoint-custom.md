---
title: Egyéni keresési végpont |} Microsoft Docs
description: Az egyéni keresési API-végpont összegzését.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 12/05/2017
ms.author: v-gedod
ms.openlocfilehash: 8d9851f3687a783f52a80a8dffcf2580d4710551
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347867"
---
# <a name="custom-search"></a>Egyéni keresés
Bing egyéni keresés szabott keresési lép témakörök, amelyek az Ön számára legfontosabb létrehozását teszi lehetővé. A tartalom, így ahelyett hogy érdeklő igazított találatok jelennek meg a felhasználók lapra a keresési eredmények, amelyek tartalma nem számít.

## <a name="custom-search-endpoint"></a>Egyéni keresési végpont
Egyéni Bing keresési API használatával eredményekhez juthat, küldjön egy `GET` kérelem a következő végponthoz. A fejlécek és URL-cím paraméterek segítségével további specifikációk meghatározása.

Végpont: Értéket ad vissza keresési javaslatok JSON találatok között, amely kapcsolódik a határozzák meg a felhasználói bevitel `?q=""`.
```  
 GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search  
```

Azt ismertetik, hogyan állíthat be egyéni keresési források példákért lásd a [oktatóanyag](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/tutorials/custom-search-web-page). A fejlécek, a paraméterek, a piaci kódok, a válasz objektumok, a hibák, vonatkozó további információért stb, tekintse meg a [Bing egyéni Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference) hivatkozás.

## <a name="custom-search-response-json"></a>Egyéni keresési válasz JSON
Egyéni keresési kérelem JSON-objektumként eredményeket ad vissza, lásd: [válasz objektumok](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#response-objects). 

## <a name="custom-autosuggest"></a>Egyéni automatikus kiegészítési
Az egyéni automatikus kiegészítési API lehetővé teszi egy részleges keresési lekérdezés kifejezés küldeni a Bing, és vissza konfigurálható javasolt lekérdezések listáját. Az automatikus kiegészítési egyéni az API által visszaadott javaslatokat tehet, és opcionálisan adja meg, hogy a Bing által generált javasol.

## <a name="custom-autosuggest-endpoint"></a>Egyéni automatikus kiegészítési végpont
Egyéni lekérdezési javaslatok kérelmezéséhez GET kérelem küldése:

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions
```  

További információ az egyéni javaslatok meghatározása: [határozza meg az egyéni keresési javaslatok](define-custom-suggestions.md).

## <a name="custom-image-search"></a>Egyéni lemezkép keresése
Az egyéni lemezképet Search API lehetővé teszi a keresési lekérdezés küldése a Bing, és az egyéni keresés példányból vissza megfelelő képek listájának lekérése.

## <a name="custom-image-search-endpoint"></a>Kép: egyéni keresési végpont
Lemezképek kérhet az egyéni keresési példányát, GET kérés küldése a következő URL-címe:

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/images/search
```

Egy egyéni keresési példányát konfigurálásával kapcsolatos további információkért lásd: [konfigurálja az egyéni keresési élményt biztosít](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/define-your-custom-view).

## <a name="next-steps"></a>További lépések
A **Bing** API-k támogatják a keresési eredményeket a alapján műveleteket. Az összes keresési végpontok JSON-válasz objektumként adja vissza az eredményeket.  Minden végpontok egy adott nyelvhez és/vagy a hely által a földrajzi hosszúság értéke, a szélességi és a keresési radius visszaadó lekérdezések támogatja.

A végpontok által támogatott paraméterekkel kapcsolatos részletes információkért lásd: az egyes referencia oldalak.
Alapszintű kérelmek használatával az egyéni keresési API-t, tekintse meg a [egyéni keresés gyors-indítása](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/)