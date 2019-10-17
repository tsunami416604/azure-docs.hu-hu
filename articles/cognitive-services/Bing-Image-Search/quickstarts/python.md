---
title: 'Gyors útmutató: képek keresése – Bing Image Search REST API és a Python'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval képkeresési kérelmeket küldhet a Bing Image Search REST API a Python használatával, és JSON-válaszokat kaphat.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 08/26/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 8dc7bc36b3d4b172521b0fbbf9aa09cf4d1a9b29
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390120"
---
# <a name="quickstart-search-for-images-using-the-bing-image-search-rest-api-and-python"></a>Gyors útmutató: rendszerképek keresése a Bing Image Search REST API és a Python használatával

Ezzel a rövid útmutatóval megkezdheti a keresési kérések küldését a Bing Image Search API. Ez a Python-alkalmazás keresési lekérdezést küld az API-nak, és megjeleníti az eredményekben szereplő első rendszerkép URL-címét. Habár ez az alkalmazás Pythonban íródott, az API egy REST-alapú webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel.

Ezt a példát futtathatja Jupyter-notebookként a [MyBinderen](https://mybinder.org), az indítás Binder-jelvényére kattintva.

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingImageSearchAPI.ipynb)


A minta forráskódja további hibakezeléssel és megjegyzésekkel együtt elérhető a [GitHubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingImageSearchv7.py).


## <a name="prerequisites"></a>Előfeltételek

* [Python 2. x vagy 3. x](https://www.python.org/)
* A [Python Imaging Library (PIL)](https://pillow.readthedocs.io/en/stable/index.html)
* [matplotlib](https://matplotlib.org/) 

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új Python-fájlt a kedvenc IDE vagy szerkesztőben, és importálja a következő modulokat. Hozzon létre egy változót az előfizetési kulcshoz, a keresési végponthoz és a keresési kifejezéshez.

    ```python
    import requests
    import matplotlib.pyplot as plt
    from PIL import Image
    from io import BytesIO
    
    subscription_key = "your-subscription-key"
    search_url = "https://api.cognitive.microsoft.com/bing/v7.0/images/search"
    search_term = "puppies"
    ```

2. Adja hozzá az előfizetési kulcsot a `Ocp-Apim-Subscription-Key` fejléchez egy szótár létrehozásával, és adja hozzá a kulcsot értékként. 

    ```python
    headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
    ```

## <a name="create-and-send-a-search-request"></a>Keresési kérelem létrehozása és küldése

1. Hozzon létre egy szótárt a keresési kérelem paramétereinek. Adja hozzá a keresési kifejezést a `q` paraméterhez. A nyilvános tartományban található lemezképek kereséséhez használja a "Public" értéket a `license` paraméterhez. A `imageType` "Photo" kifejezéssel csak a fényképek keresése.

    ```python
    params  = {"q": search_term, "license": "public", "imageType": "photo"}
    ```

2. A Bing Image Search API meghívásához használja a `requests` függvénytárat. Adja hozzá a fejlécet és a paramétereket a kérelemhez, és adja vissza a választ JSON-objektumként. A válasz `thumbnailUrl` mezőjéből több miniatűr lemezkép URL-címének beolvasása.

    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = response.json()
    thumbnail_urls = [img["thumbnailUrl"] for img in search_results["value"][:16]]
    ```

## <a name="view-the-response"></a>Válasz megtekintése

1. Hozzon létre egy új ábrát négy oszloppal és négy sorral a matplotlib könyvtár használatával. 

2. Ismételje meg az ábra sorait és oszlopait, és a PIL Library `Image.open()` metódusával adja hozzá a kép miniatűrjét az egyes területekhez. 

3. A `plt.show()` használatával rajzolja meg az ábrát, és jelenítse meg a képeket.

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

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Egyoldalas alkalmazás-oktatóanyag a Bing Image Search használatához](../tutorial-bing-image-search-single-page-app.md)

* [Mi a Bing Image Search API?](../overview.md)  
* A Bing Search API-k [díjszabása](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) . 
* [Ingyenes Cognitive Services hozzáférési kulcs beszerzése](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Az Azure Cognitive Services dokumentációja](https://docs.microsoft.com/azure/cognitive-services)
* [Bing Image Search API – referenciaanyag](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
