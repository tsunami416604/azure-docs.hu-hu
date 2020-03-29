---
title: Automatikus keresési kifejezések – Bing Web Search API
titleSuffix: Azure Cognitive Services
description: Párosítsa a Bing Web Search API-t a Bing Automatikus javaslat API-val, hogy a felhasználók számára továbbfejlesztett keresési élményt nyújtson.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/03/2019
ms.author: aahi
ms.openlocfilehash: 0fb62966c78eb19c1daf9294efba786a267ae200
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "66384862"
---
# <a name="autosuggest-bing-search-terms-in-your-application"></a>Bing keresési kifejezések automatikus javaslata az alkalmazásban

Ha biztosít egy olyan keresőmezőt, ahol a felhasználók megadhatják a keresőkifejezést, a [Bing Autosuggest API](../bing-autosuggest/get-suggested-search-terms.md) használatával kényelmesebbé teheti a felhasználói élményt. Az API javasolt lekérdezési sztringeket ad vissza a részleges keresőkifejezések alapján, miközben a felhasználó gépel.

Miután a felhasználó beírt egy keresési kifejezést, azt url-címmel kell kódolni a [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query) query paraméter beállítása előtt. Ha például a felhasználó a *sailing dinghies* (kis vitorlások) kifejezésre keres, állítsa a `q` beállítást `sailing+dinghies` vagy `sailing%20dinghies` értékre.

Ha a lekérdezési kifejezés helyesírási hibát tartalmaz, a keresési válasz [egy QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#querycontext) objektumot is tartalmaz. Az objektum az eredeti és a javított helyesírást is mutatja, amelyet a Bing a keresés során használt.

```json
"queryContext": {
    "originalQuery": "sialing dingy for sale",
    "alteredQuery": "sailing dinghy for sale",
    "alterationOverrideQuery": "+sialing +dingy for sale"
}
```

Ezen információk segítségével tudathatja a felhasználóval, hogy módosította a lekérdezési karakterláncot a keresési eredmények megjelenítésekor.

![Példa lekérdezési környezet felhasználói felületére](./media/cognitive-services-bing-web-api/bing-query-context.PNG)  

## <a name="next-steps"></a>További lépések  

* Tekintse át a [Bing Web Search API-válaszok mintáját.](search-responses.md)  

## <a name="see-also"></a>Lásd még  

* [Bing Web Search API-hivatkozás](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
