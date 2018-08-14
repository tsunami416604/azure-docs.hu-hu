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
ms.openlocfilehash: d74f59ffcf095e639686a3ada3b09dac988fc544
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2018
ms.locfileid: "40082609"
---
# <a name="sending-queries-to-the-bing-image-search-api"></a>Lekérdezéseket küld a Bing Image Search API

A Bing Image Search API azáltal, hogy egy felhasználó keresési lekérdezést küld a Bing, majd az érintett rendszerképek listájának Bing.com/Images hasonló élményt nyújt.

## <a name="using-and-suggesting-search-terms"></a>Használatával, és a keresési kifejezéseket javasol.

Miután egy keresési kifejezést adja meg, akkor az előfizetési időszak beállítása előtt URL-kódolása a [ **q** ](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query) lekérdezési paraméter. Például, ha a felhasználó *hajózási dinghies*állítsa be `q` való `sailing+dinghies` vagy `sailing%20dinghies`.

Ha az alkalmazás egy keresőmezőt, ahol a keresési feltételek kerülnek, használhatja a [a Bing Autosuggest API](../../bing-autosuggest/get-suggested-search-terms.md) élményének növelése érdekében javasolt keresési kifejezéseket, valós idejű megjelenítésével. Az API-t adja vissza a javasolt lekérdezési karakterláncok részleges keresési kifejezéseket, és az Azure cognitive services alapján.

## <a name="pivoting-the-query"></a>A lekérdezés szegmentálása

Ha a Bing is szegmentálhatja az eredeti keresési lekérdezés által visszaadott [lemezképek](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) objektumot fog tartalmazni `pivotSuggestions` , a felhasználónak kötelező keresési kifejezéseket is megjeleníthetők. Például, ha az eredeti lekérdezés lett *Microsoft Surface*, a Bing lehet, hogy szegmentálhatja a lekérdezést *Microsoft* és *Surface* , és adja meg a javasolt kimutatás az egyes. Ezeket az opcionális lekérdezési feltételek a felhasználó számára, jeleníthető meg.

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

A `pivotSuggestions` mező azoknak a szegmenseknek a listáját tartalmazza, amelyekre az eredeti lekérdezést a rendszer felbontotta. A válasz minden szegmenshez kínál olyan [Query](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query_obj) objektumokat, amelyek a javasolt lekérdezéseket tartalmazzák. A `text` mező a javasolt lekérdezést, a `displayText` mező pedig az eredeti lekérdezésben a szegmenst lecserélő kifejezést tartalmazza. Például: Release Date of Surface (Surface megjelenési dátuma).

A `text` és a `thumbnail` mezőben megjelenítheti a szegmentált lekérdezési sztringeket arra az esetre, ha a felhasználó valójában az egyik szegmentált lekérdezési sztringet keresné. A miniatűrt és a szöveget a `webSearchUrl` URL vagy a `searchLink` URL használatával kattinthatóvá teheti. Akkor használja a `webSearchUrl` lehetőséget, ha a felhasználót a Bing keresési találatait szeretné küldeni, a `searchLink` lehetőséget pedig akkor, ha saját eredményoldalt használ.

<!-- Need a sanitized version of the image
The following shows an example of the pivot queries.

![Pivot suggestions](./media/cognitive-services-bing-images-api/bing-image-pivotsuggestion.GIF)
-->

## <a name="expanding-the-query"></a>A lekérdezés bővítése

Ha a Bing ki tudja bővíteni a lekérdezést az eredeti keresés szűkítéséhez, az [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) objektumban szerepelni fog a `queryExpansions` mező. Ha például a lekérdezés a *Microsoft Surface* volt, a bővített lekérdezések a következők lehetnek: Microsoft Surface **Pro 3**, Microsoft Surface **RT**, Microsoft Surface **Phone** és Microsoft Surface **Hub**.

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

A `queryExpansions` mező a [Query](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query_obj) objektumok listáját tartalmazza. A `text` mezőben a bővített lekérdezés, a `displayText` mezőben pedig a bővítési kifejezés található. A `text` és a `thumbnail` mezőben megjelenítheti a bővített lekérdezési sztringeket arra az esetre, ha a felhasználó valójában az egyik bővített lekérdezési sztringet keresné. A miniatűrt és a szöveget a `webSearchUrl` URL vagy a `searchLink` URL használatával kattinthatóvá teheti. Akkor használja a `webSearchUrl` lehetőséget, ha a felhasználót a Bing keresési találatait szeretné küldeni, a `searchLink` lehetőséget pedig akkor, ha saját eredményoldalt használ.

<!-- Removing until we can replace with a sanitized image.
The following shows an example Bing implementation that uses expanded queries. If the user clicks the Microsoft Surface Pro 3 link, they're taken to the Bing search results page, which shows them images of the Pro 3.

![Query expansion suggestions](./media/cognitive-services-bing-images-api/bing-image-queryexpansion.GIF)
-->


## <a name="throttling-requests"></a>Kérelmek szabályozása

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>További lépések

Ha még nem próbálta a Bing Image Search API előtti, próbálja meg egy [rövid](../quickstarts/csharp.md). Ha összetettebb keres, próbálja meg a következő oktatóanyagban létrehozása egy [egyoldalas webalkalmazást](../tutorial-bing-image-search-single-page-app.md).
