---
title: 'Rövid útmutató: Videók keresése a REST API és a Python használatával – Bing videokeresés'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval videokeresési kérelmeket küldhet a Bing Video Search REST API-nak a Python használatával.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.openlocfilehash: fbf20c2d54506b0f314697d6df34f9a430e7c016
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75382684"
---
# <a name="quickstart-search-for-videos-using-the-bing-video-search-rest-api-and-python"></a>Rövid útmutató: Videók keresése a Bing Video Search REST API-val és a Pythonnal

Ezzel a rövid útmutatóval elsőként hívhatja meg a Bing Video Search API-t, és megtekintheti a JSON-válasz keresési eredményét. Ez az egyszerű Python-alkalmazás http-videokeresési lekérdezést küld az API-nak, és megjeleníti a választ. Bár ez az alkalmazás Python nyelven lett íródott, az API egy RESTful-webszolgáltatás, azaz kompatibilis a legtöbb programnyelvvel. A minta forráskódja további hibakezeléssel és kódmegjegyzésekkel együtt elérhető a [GitHubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingVideoSearchv7.py).

Ezt a példát futtathatja Jupyter-notebookként a [MyBinderen](https://mybinder.org), az indítás Binder-jelvényére kattintva. 

[![Iratgyűjtő](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingVideoSearchAPI.ipynb)


## <a name="prerequisites"></a>Előfeltételek

* Python [2.x vagy 3.x](https://python.org)

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Az alkalmazás inicializálása

1. Hozzon létre egy új Python fájlt a kedvenc IDE vagy szerkesztő, és importálja a következő könyvtárak,

    ```python
    import requests
    from IPython.display import HTML
    ```
2.  Hozzon létre változókat az előfizetési kulcshoz, a keresési végponthoz és egy keresési kifejezéshez. `search_url`lehet az alábbi globális végpont, vagy az [egyéni altartomány](../../../cognitive-services/cognitive-services-custom-subdomains.md) végpont jatthatja az erőforrás azure portalján.
    
    ```python
    subscription_key = None
    assert subscription_key
    search_url = "https://api.cognitive.microsoft.com/bing/v7.0/videos/search"
    search_term = "kittens"
    ```

3. Adja hozzá az `Ocp-Apim-Subscription-Key` előfizetési kulcsot egy fejléchez egy új szótár létrehozásával, amely a fejléckarakterláncot társítja a kulcshoz.

    ```python
    headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
    ```

## <a name="send-your-request"></a>Küldje el a kérését

1. Adja hozzá a paramétereket a kéréshez `params`egy szótár nevű szótár létrehozásával. Adja hozzá a `q` keresési kifejezést a paraméterhez, az 5-ös videószámhoz, `free` a visszaküldött videók árához és `short` a videó hosszához.

    ```python
    params  = {"q": search_term, "count":5, "pricing": "free", "videoLength":"short"}
    ```

2. A `requests` Python könyvtára segítségével hívja meg a Bing Video Search API-t. Adja át az API-kulcs és `headers` `params` a keresési paraméterek et a és a szótár használatával.
    
    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = response.json()
    ```

3. A visszaadott videók megtekintéséhez kapjon keresési `search_results` eredményt az objektumról. Szúrja be az `embedHtml` eredmény `IFrame`tulajdonságát egy be.  
    
    ```python
    HTML(search_results["value"][0]["embedHtml"].replace("autoplay=1","autoplay=0"))
    ```


## <a name="json-response"></a>JSON-válasz

A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon: 

```json
{
    "_type": "Videos",
    "instrumentation": {},
    "readLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?q=kittens",
    "webSearchUrl": "https://www.bing.com/videos/search?q=kittens",
    "totalEstimatedMatches": 1000,
    "value": [
        {
            "webSearchUrl": "https://www.bing.com/videos/search?q=kittens&view=...",
            "name": "Top 10 cute kitten videos compilation",
            "description": "HELP HOMELESS ANIMALS AND WIN A PRIZE BY CHOOSING...",
            "thumbnailUrl": "https://tse4.mm.bing.net/th?id=OVP.n1aE_Oikl4MtzBb...",
            "datePublished": "2014-11-12T22:47:36.0000000",
            "publisher": [
                {
                    "name": "Fabrikam"
                }
            ],
            "creator": {
                "name": "Marcus Appel"
            },
            "isAccessibleForFree": true,
            "contentUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "hostPageUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "encodingFormat": "h264",
            "hostPageDisplayUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "width": 480,
            "height": 360,
            "duration": "PT3M52S",
            "motionThumbnailUrl": "https://tse4.mm.bing.net/th?id=OM.j4QyJAENJphdZQ_1501386166&pid=Api",
            "embedHtml": "<iframe width=\"1280\" height=\"720\" src=\"https://www.fabrikam.com/embed/8HVWitAW-Qg?autoplay=1\" frameborder=\"0\" allowfullscreen></iframe>",
            "allowHttpsEmbed": true,
            "viewCount": 7513633,
            "thumbnail": {
                "width": 300,
                "height": 168
            },
            "videoId": "655D98260D012432848F6558260D012432848F",
            "allowMobileEmbed": true,
            "isSuperfresh": false
        },
        . . .
    ],
    "nextOffset": 36,
    "queryExpansions": [
        {
            "text": "Kittens Meowing",
            "displayText": "Meowing",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Kittens+Meowing...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
            "thumbnail": {
                "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Kittens+Meowing&pid..."
            }
        },
        {
            "text": "Funny Kittens",
            "displayText": "Funny",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Funny+Kittens...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
            "thumbnail": {
                "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Funny+Kittens&..."
            }
        },
        . . .
    ],
    "pivotSuggestions": [
        {
            "pivot": "kittens",
            "suggestions": [
                {
                    "text": "Cat",
                    "displayText": "Cat",
                    "webSearchUrl": "https://www.bing.com/videos/search?q=Cat...",
                    "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?...",
                    "thumbnail": {
                        "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Cat&pid=Api..."
                    }
                },
                {
                    "text": "Feral Cat",
                    "displayText": "Feral Cat",
                    "webSearchUrl": "https://www.bing.com/videos/search?q=Feral+Cat...",
                    "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
                    "thumbnail": {
                        "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Feral+Cat&pid=Api&..."
                    }
                }
            ]
        }
    ],
    "relatedSearches": [
        {
            "text": "Kittens Being Born",
            "displayText": "Kittens Being Born",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Kittens+Being+Born...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?...",
            "thumbnail": {
                "thumbnailUrl": "https://tse1.mm.bing.net/th?q=Kittens+Being+Born&pid=..."
            }
        },
        . . .
    ]
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas webalkalmazás készítése](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Lásd még 

 [Mi az a Bing Video Search API?](../overview.md)
