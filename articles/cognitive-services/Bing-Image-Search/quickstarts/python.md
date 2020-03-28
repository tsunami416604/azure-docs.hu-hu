---
title: 'Rövid útmutató: Képek keresése REST API és Python – Bing képkeresés'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval képkeresési kérelmeket küldhet a Bing Image Search REST API-nak a Python használatával, és JSON-válaszokat kaphat.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 12/06/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: f3d00688feb0f9d42e80cdbb51753483e53c388d
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930729"
---
# <a name="quickstart-search-for-images-using-the-bing-image-search-rest-api-and-python"></a>Rövid útmutató: Képek keresése a Bing Image Search REST API-val és a Pythonnal

Ezzel a rövid útmutatóval elindíthatja a keresési kérelmek küldését a Bing Image Search API-nak. Ez a Python-alkalmazás keresési lekérdezést küld az API-nak, és megjeleníti az első kép URL-címét az eredményekben. Bár ez az alkalmazás python nyelven íródott, az API egy RESTful webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel.

Ezt a példát futtathatja Jupyter-notebookként a [MyBinderen](https://mybinder.org), az indítás Binder-jelvényére kattintva.

[![Iratgyűjtő](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingImageSearchAPI.ipynb)


A minta forráskódja további hibakezeléssel és megjegyzésekkel együtt elérhető a [GitHubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingImageSearchv7.py).


## <a name="prerequisites"></a>Előfeltételek

* [Python 2.x vagy 3.x](https://www.python.org/)
* A [Python Képalkotó Könyvtár (PIL)](https://pillow.readthedocs.io/en/stable/index.html)
* [matplotlib között](https://matplotlib.org/) 

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új Python-fájlt a kedvenc IDE-ben vagy szerkesztőjében, és importálja a következő modulokat. Hozzon létre egy változót az előfizetési kulcshoz, a keresési végponthoz és a keresési kifejezéshez. `search_url`lehet az alábbi globális végpont, vagy az [egyéni altartomány](../../../cognitive-services/cognitive-services-custom-subdomains.md) végpont jatthatja az erőforrás azure portalján.

    ```python
    import requests
    import matplotlib.pyplot as plt
    from PIL import Image
    from io import BytesIO
    
    subscription_key = "your-subscription-key"
    search_url = "https://api.cognitive.microsoft.com/bing/v7.0/images/search"
    search_term = "puppies"
    ```

2. Adja hozzá az `Ocp-Apim-Subscription-Key` előfizetési kulcsot a fejléchez egy szótár létrehozásával és a kulcs értékként való hozzáadásával. 

    ```python
    headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
    ```

## <a name="create-and-send-a-search-request"></a>Keresési kérelem létrehozása és küldése

1. Hozzon létre egy szótárat a keresési kérelem paramétereihez. Adja hozzá a `q` keresési kifejezést a paraméterhez. Használja a "nyilvános" a `license` paraméter képek keresése a nyilvános tartományban. Használja a "fénykép" a `imageType` keresni csak a képeket.

    ```python
    params  = {"q": search_term, "license": "public", "imageType": "photo"}
    ```

2. A `requests` tár segítségével hívja meg a Bing Image Search API-t. Adja hozzá a fejlécet és a paramétereket a kérelemhez, és adja vissza a választ JSON-objektumként. Az URL-címek lenyomása több miniatűr `thumbnailUrl` képre a válasz mezőjéből.

    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = response.json()
    thumbnail_urls = [img["thumbnailUrl"] for img in search_results["value"][:16]]
    ```

## <a name="view-the-response"></a>A válasz megtekintése

1. Hozzon létre egy új alakzatot négy oszlopból és négy sorral a matplotlib könyvtár használatával. 

2. Végighaladjon az alakzat sorai és oszlopai között, `Image.open()` és a PIL-könyvtár módszerével adjon hozzá képbélyegképet az egyes szóközökhöz. 

3. Segítségével `plt.show()` rajzolja meg az ábrát, és jelenítse meg a képeket.

    ```python
    f, axes = plt.subplots(4, 4)
    for i in range(4):
        for j in range(4):
            image_data = requests.get(thumbnail_urls[i+4*j])
            image_data.raise_for_status()
            image = Image.open(BytesIO(image_data.content))        
            axes[i][j].imshow(image)
            axes[i][j].axis("off")
    plt.show()
    ```


## <a name="example-json-response"></a>Példa JSON-válaszra

A Bing Image Search API válaszai JSON formátumban érkeznek vissza. A mintaválasz egyetlen eredményre van csonkolva.

```json
{
"_type":"Images",
"instrumentation":{
    "_type":"ResponseInstrumentation"
},
"readLink":"images\/search?q=tropical ocean",
"webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=tropical ocean&FORM=OIIARP",
"totalEstimatedMatches":842,
"nextOffset":47,
"value":[
    {
        "webSearchUrl":"https:\/\/www.bing.com\/images\/search?view=detailv2&FORM=OIIRPO&q=tropical+ocean&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&simid=608027248313960152",
        "name":"My Life in the Ocean | The greatest WordPress.com site in ...",
        "thumbnailUrl":"https:\/\/tse3.mm.bing.net\/th?id=OIP.fmwSKKmKpmZtJiBDps1kLAHaEo&pid=Api",
        "datePublished":"2017-11-03T08:51:00.0000000Z",
        "contentUrl":"https:\/\/mylifeintheocean.files.wordpress.com\/2012\/11\/tropical-ocean-wallpaper-1920x12003.jpg",
        "hostPageUrl":"https:\/\/mylifeintheocean.wordpress.com\/",
        "contentSize":"897388 B",
        "encodingFormat":"jpeg",
        "hostPageDisplayUrl":"https:\/\/mylifeintheocean.wordpress.com",
        "width":1920,
        "height":1200,
        "thumbnail":{
        "width":474,
        "height":296
        },
        "imageInsightsToken":"ccid_fmwSKKmK*mid_8607ACDACB243BDEA7E1EF78127DA931E680E3A5*simid_608027248313960152*thid_OIP.fmwSKKmKpmZtJiBDps1kLAHaEo",
        "insightsMetadata":{
        "recipeSourcesCount":0,
        "bestRepresentativeQuery":{
            "text":"Tropical Beaches Desktop Wallpaper",
            "displayText":"Tropical Beaches Desktop Wallpaper",
            "webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=Tropical+Beaches+Desktop+Wallpaper&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&FORM=IDBQDM"
        },
        "pagesIncludingCount":115,
        "availableSizesCount":44
        },
        "imageId":"8607ACDACB243BDEA7E1EF78127DA931E680E3A5",
        "accentColor":"0050B2"
    }]
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas alkalmazás-oktatóanyag a Bing Image Search használatához](../tutorial-bing-image-search-single-page-app.md)

* [Mi az a Bing Image Search API?](../overview.md)  
* A Bing Search API-k [díjszabási részletei.](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) 
* [Ingyenes Cognitive Services hozzáférési kulcs beszerzése](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Az Azure Cognitive Services dokumentációja](https://docs.microsoft.com/azure/cognitive-services)
* [Bing Image Search API – referencia](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
