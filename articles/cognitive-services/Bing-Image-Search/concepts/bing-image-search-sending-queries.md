---
title: Lekérdezéseket küld a Bing Image Search API |} A Microsoft Docs
description: Ismerje meg a Küldés és a testreszabás, a Bing Image Search API küldött keresési lekérdezések.
services: cognitive-services
author: aahill
manager: cgronlun
ms.assetid: C2862E98-8BCC-423B-9C4A-AC79A287BE38
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: conceptual
ms.date: 8/8/2018
ms.author: aahi
ms.openlocfilehash: bf0db0b6d2aa54a853ba86b570ca05fba902dbc1
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44049558"
---
# <a name="send-queries-to-the-bing-image-search-api"></a>Lekérdezéseket küld a Bing Image Search API

A Bing Image Search API-Bing.com/Images hasonló élményt nyújt. Használhatja egy keresési lekérdezést küld a Bing, majd megfelelő rendszerképek listájának.

## <a name="use-and-suggest-search-terms"></a>Használjon, és javaslatot a keresési feltételek

Miután egy keresési kifejezést adja meg, akkor URL-kódolása az előfizetési időszak előtt állítsa be a [ **q** ](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query) lekérdezési paraméter. Ha például meg *hajózási dinghies*állítsa be `q` való `sailing+dinghies` vagy `sailing%20dinghies`.

Ha az alkalmazás egy keresőmezőt, ahol a keresési feltételek kerülnek, használhatja a [a Bing Autosuggest API](../../bing-autosuggest/get-suggested-search-terms.md) élményének növelése érdekében. Az API-t javasolt keresőkifejezéseket valós időben jelenítheti meg. Az API-t adja vissza a részleges keresési kifejezéseket, és a Cognitive Services alapján javasolt lekérdezési karakterláncokat.

## <a name="pivot-the-query"></a>A lekérdezés forgáspont

Ha a Bing is szegmentálhatja az eredeti keresési lekérdezés által visszaadott [lemezképek](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) az objektum tartalmaz `pivotSuggestions`. Pivot javaslatok, a felhasználónak kötelező keresőkifejezéseket jeleníthető meg. Például, ha az eredeti lekérdezés lett *Microsoft Surface*, a Bing lehet, hogy szegmentálhatja a lekérdezést *Microsoft* és *Surface* , és adja meg a javasolt kimutatás az egyes. Ezek a javaslatok, a felhasználónak kötelező lekérdezési kifejezések jeleníthető meg.

Az alábbi példa bemutatja a pivot javaslatokat *Microsoft Surface*:  

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface&FORM=OIIARP",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions": [...],
    "pivotSuggestions": [{
        "pivot": "microsoft",
        "suggestions": [{
            "text": "Contoso Surface",
            "displayText": "Contoso",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=OtterBox+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Contoso...",
                    "searchLink": "https:\/\/api.cognitive.microsoft.com\/api...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Contoso+Surface..."
            }
        },
        {
            "text": "Adatum Surface",
            "displayText": "Adatum",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Adatum+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Adatum+Surface&pid=Ap..."
            }
        },
        ...
        ]
    },
    {
        "pivot": "surface",
        "suggestions": [{
            "text": "Microsoft Surface4",
            "displayText": "Surface4",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface...",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft..."
            }
        },
        {
            "text": "Microsoft Tablet",
            "displayText": "Tablet",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Tablet&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Microsoft+Tablet..."
            }
        },
        ...
    ],
    "nextOffsetAddCount": 0
}
```

A `pivotSuggestions` mező azoknak a szegmenseknek a listáját tartalmazza, amelyekre az eredeti lekérdezést a rendszer felbontotta. A válasz minden szegmenshez kínál olyan [Query](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query_obj) objektumokat, amelyek a javasolt lekérdezéseket tartalmazzák. A `text` mező tartalmazza a javasolt lekérdezést. A `displayText` mező a kifejezés, amely lecseréli az eredeti lekérdezés a Pivot táblázatot tartalmaz. Ilyen például, a Surface kiadási dátum.

Ha a pivot lekérdezési karakterláncot a felhasználói felülete, használja a `text` és `thumbnail` a pivot megjelenítendő mezők lekérdezési karakterláncokat. Győződjön meg a miniatűr és a szöveg kattintható használatával a `webSearchUrl` URL-cím vagy a `searchLink` URL-CÍMÉT. Használat `webSearchUrl` a felhasználó küldhet a Bing keresési eredmények között. A saját eredmények lapon ad meg, ha `searchLink`.

<!-- Need a sanitized version of the image
The following shows an example of the pivot queries.

![Pivot suggestions](./media/cognitive-services-bing-images-api/bing-image-pivotsuggestion.GIF)
-->

## <a name="expand-the-query"></a>Bontsa ki a lekérdezés

Ha a Bing ki tudja bővíteni a lekérdezést az eredeti keresés szűkítéséhez, az [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) objektumban szerepelni fog a `queryExpansions` mező. Például, ha a lekérdezés a(z) *Microsoft Surface*, lehet, hogy a kibontott lekérdezéseket: 
- Microsoft Surface **Pro 3**.
- Microsoft Surface **RT**.
- Microsoft Surface **Phone**.
- Microsoft Surface **Hub**.

Az alábbi példában a *Microsoft Surface* bővített lekérdezései láthatók.

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface...",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions":  [{
        "text": "Microsoft Surface Pro 3",
        "displayText": "Pro 3",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Pro+3...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Microsoft...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Pro+3..."
        }
    },
    {
        "text": "Microsoft Surface RT",
        "displayText": "RT",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+RT...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+RT..."
        }
    },
    {
        "text": "Microsoft Surface Phone",
        "displayText": "Phone",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Phone",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Phone..."
        }
    }],
    "pivotSuggestions": [...],
    "nextOffsetAddCount": 0
}
```

A `queryExpansions` mező a [Query](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query_obj) objektumok listáját tartalmazza. A `text` mező tartalmazza a kibontott lekérdezést. A `displayText` mező tartalmazza a nyelvi kifejezés. Ha a bővített lekérdezési karakterláncot a felhasználói felülete, használja a `text` és `thumbnail` a bővített lekérdezési karakterláncok megjelenítendő mezőket. Győződjön meg a miniatűr és a szöveg kattintható használatával a `webSearchUrl` URL-cím vagy a `searchLink` URL-CÍMÉT. Használat `webSearchUrl` a felhasználó küldhet a Bing keresési eredmények között. a saját eredmények lapon ad meg, ha `searchLink`.

<!-- Removing until we can replace with a sanitized image.
The following shows an example Bing implementation that uses expanded queries. If the user clicks the Microsoft Surface Pro 3 link, they're taken to the Bing search results page, which shows them images of the Pro 3.

![Query expansion suggestions](./media/cognitive-services-bing-images-api/bing-image-queryexpansion.GIF)
-->


## <a name="throttling-requests"></a>Kérelmek szabályozása

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>További lépések

Ha még nem próbálta a Bing Image Search API előtti, próbálja meg egy [rövid](../quickstarts/csharp.md). Ha összetettebb keres, próbálja meg létrehozni az oktatóanyag egy [egyoldalas webalkalmazást](../tutorial-bing-image-search-single-page-app.md).
