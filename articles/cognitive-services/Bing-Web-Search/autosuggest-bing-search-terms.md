---
title: Automatikus kiegészítési Bing keresési kifejezések
titleSuffix: Microsoft Cognitive Services
description: Párosítsa a Bing Web Search API a Bing Autosuggest API olyan továbbfejlesztett keresési élményt biztosít.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 8/13/2018
ms.author: erhopf
ms.openlocfilehash: df8a57b3136abfacce971f4d01ccb2296dfa784c
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42889453"
---
# <a name="autosuggest-bing-search-terms"></a>Automatikus kiegészítési Bing keresési kifejezések

Ha biztosít egy olyan keresőmezőt, ahol a felhasználók megadhatják a keresőkifejezést, a [Bing Autosuggest API](../bing-autosuggest/get-suggested-search-terms.md) használatával kényelmesebbé teheti a felhasználói élményt. Az API javasolt lekérdezési sztringeket ad vissza a részleges keresőkifejezések alapján, miközben a felhasználó gépel.

Miután a felhasználó megadja egy keresési kifejezést, URL-kódolású előtt kell lennie a [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#query) lekérdezési paraméter értéke. Ha például a felhasználó a *sailing dinghies* (kis vitorlások) kifejezésre keres, állítsa a `q` beállítást `sailing+dinghies` vagy `sailing%20dinghies` értékre.

Ha a lekérdezési kifejezés egy gépelési hibát tartalmaz, a keresési válasz tartalmazza a [QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#querycontext) objektum. Az objektum az eredeti helyesírás- és a javított helyesírás-ellenőrzés, hogy a keresési Bing használt jeleníti meg.

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
