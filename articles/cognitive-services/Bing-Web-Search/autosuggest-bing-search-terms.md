---
title: Keresési feltételek automatikus ajánlása – Bing Web Search API
titleSuffix: Azure Cognitive Services
description: A Bing Web Search API és a Bing Autosuggest API párosításával biztosíthatja a felhasználók számára a továbbfejlesztett keresési élményt.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/03/2019
ms.author: aahi
ms.openlocfilehash: 0fb62966c78eb19c1daf9294efba786a267ae200
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "66384862"
---
# <a name="autosuggest-bing-search-terms-in-your-application"></a>Bing-keresési kifejezések automatikus kiegészítése az alkalmazásban

Ha biztosít egy olyan keresőmezőt, ahol a felhasználók megadhatják a keresőkifejezést, a [Bing Autosuggest API](../bing-autosuggest/get-suggested-search-terms.md) használatával kényelmesebbé teheti a felhasználói élményt. Az API javasolt lekérdezési sztringeket ad vissza a részleges keresőkifejezések alapján, miközben a felhasználó gépel.

Miután a felhasználó beírja a keresési kifejezést, a [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query) lekérdezési paraméter beállítása előtt kódolni kell az URL-címet. Ha például a felhasználó a *sailing dinghies* (kis vitorlások) kifejezésre keres, állítsa a `q` beállítást `sailing+dinghies` vagy `sailing%20dinghies` értékre.

Ha a lekérdezési kifejezés helyesírási hibát tartalmaz, a keresési válasz egy [QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#querycontext) objektumot tartalmaz. Az objektum az eredeti és a javított helyesírást is mutatja, amelyet a Bing a keresés során használt.

```json
"queryContext": {
    "originalQuery": "sialing dingy for sale",
    "alteredQuery": "sailing dinghy for sale",
    "alterationOverrideQuery": "+sialing +dingy for sale"
}
```

Ezekkel az adatokkal engedélyezheti, hogy a felhasználó tudja, hogy módosította a lekérdezési karakterláncot a keresési eredmények megjelenítésekor.

![Példa a lekérdezés kontextusára – UX](./media/cognitive-services-bing-web-api/bing-query-context.PNG)  

## <a name="next-steps"></a>További lépések  

* Tekintse át a minta [Bing Web Search API válaszokat](search-responses.md).  

## <a name="see-also"></a>Lásd még  

* [Bing Web Search API referenciája](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
