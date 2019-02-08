---
title: Automatikus kiegészítés keresőkifejezéseket – a Bing Web Search API
titleSuffix: Azure Cognitive Services
description: Párosítsa a Bing Web Search API a Bing Autosuggest API olyan továbbfejlesztett keresési élményt biztosít.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 8/13/2018
ms.author: aahi
ms.openlocfilehash: 540c8c19d1c5ab371588b8a2092b72187382488f
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55874362"
---
# <a name="autosuggest-bing-search-terms-in-your-application"></a>Automatikus kiegészítési Bing keresési kifejezéseket az alkalmazásban

Ha biztosít egy olyan keresőmezőt, ahol a felhasználók megadhatják a keresőkifejezést, a [Bing Autosuggest API](../bing-autosuggest/get-suggested-search-terms.md) használatával kényelmesebbé teheti a felhasználói élményt. Az API javasolt lekérdezési sztringeket ad vissza a részleges keresőkifejezések alapján, miközben a felhasználó gépel.

Miután a felhasználó megadja egy keresési kifejezést, URL-kódolású előtt kell lennie a [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#query) lekérdezési paraméter értéke. Ha például a felhasználó a *sailing dinghies* (kis vitorlások) kifejezésre keres, állítsa a `q` beállítást `sailing+dinghies` vagy `sailing%20dinghies` értékre.

Ha a lekérdezési kifejezés egy gépelési hibát tartalmaz, a keresési válasz tartalmazza a [QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#querycontext) objektum. Az objektum az eredeti és a javított helyesírást is mutatja, amelyet a Bing a keresés során használt.

```json
"queryContext": {
    "originalQuery": "sialing dingy for sale",
    "alteredQuery": "sailing dinghy for sale",
    "alterationOverrideQuery": "+sialing +dingy for sale"
}
```

Ezen információk használatával lehetővé teszik a felhasználó ismeri, a lekérdezési karakterlánc módosítani, ha a keresési eredmények megjelenítéséhez.

![Lekérdezési környezet UX példa](./media/cognitive-services-bing-web-api/bing-query-context.PNG)  

## <a name="next-steps"></a>További lépések  

* Felülvizsgálat minta [Bing Web Search API-válaszok](search-responses.md).  

## <a name="see-also"></a>Lásd még  

* [A Bing Web Search API-referencia](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)
