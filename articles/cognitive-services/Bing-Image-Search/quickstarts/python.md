---
title: 'Gyors útmutató: Képkeresés – a Bing Image Search REST API és a Python használatával'
titleSuffix: Azure Cognitive Services
description: Ez a rövid útmutató segítségével kép keresési kérelmeket küldjön a Bing Image Search REST API-be a Python, és JSON-válaszok kap.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 02/06/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 0fa60f8dc7a1bb0f72080e91adb1149c1c4c082d
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56234447"
---
# <a name="quickstart-search-for-images-using-the-bing-image-search-rest-api-and-python"></a>Gyors útmutató: Képkeresés a Bing Image Search REST API és a Python használatával

Ez a rövid útmutató segítségével indítsa el a keresési kérések küldését a Bing Image Search API. A Python-alkalmazás egy keresési lekérdezést küld az API-t, és URL-címét az első képet jeleníti meg az eredményeket. Bár ez az alkalmazás pythonban írt, az API-t az szinte bármelyik programozási nyelvével kompatibilis webes RESTful szolgáltatás.

Ezt a példát futtathatja Jupyter-notebookként a [MyBinderen](https://mybinder.org), az indítás Binder-jelvényére kattintva:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingImageSearchAPI.ipynb)


A minta forráskódja további hibakezeléssel és megjegyzésekkel együtt elérhető a [GitHubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingImageSearchv7.py).


## <a name="prerequisites"></a>Előfeltételek

* [Python 2.x vagy 3.x](https://www.python.org/)
* A [Python-kódtár (PIL) Imaging](https://pillow.readthedocs.io/en/stable/index.html)
* [matplotlib](https://matplotlib.org/) 

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új Python-fájlt a kedvenc integrált Fejlesztőkörnyezetével vagy szerkesztőjével, és a következő modulok importálásához. Hozzon létre egy változót az előfizetési kulcs, a végpont keresése és a keresési kifejezést.

    ```python
    import requests
    import matplotlib.pyplot as plt
    from PIL import Image
    from io import BytesIO
    
    subscription_key = "your-subscription-key"
    search_url = "https://api.cognitive.microsoft.com/bing/v7.0/images/search"
    search_term = "puppies"
    ```

2. Az előfizetési kulcs hozzáadása a `Ocp-Apim-Subscription-Key` fejléc egy szótárban létrehozásával és a kulcs hozzáadása értékként. 

    ```python
    headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
    ```

## <a name="create-and-send-a-search-request"></a>Hozzon létre, és a egy keresési kérelmet küldeni

1. Hozzon létre egy szótárban, a Keresés a kérelem paramétereit. A keresési kifejezést adja hozzá a `q` paraméter. Használja a "nyilvános" a `license` paramétert Képkeresés a nyilvános tartomány. "Fénykép" használata a `imageType` fényképek kereséséhez.

    ```python
    params  = {"q": search_term, "license": "public", "imageType": "photo"}
    ```

2. Használja a `requests` könyvtár a Bing Image Search API meghívásához. A fejléc és paramétereket adhat hozzá a kérelmet, és a válasz egy JSON-objektumként adja vissza. 

    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = response.json()
    ```

## <a name="view-the-response"></a>Tekintse meg a választ

1. Hozzon létre egy új ábrán négy oszlopot, és négy sort a matplotlib kódtár használatával. 

2. Az ábra sorok és oszlopok végigléptetni, és a PIL kódtára `Image.open()` metódus hozzáadása egy kép miniatűr minden szóközt. 

    ```python
    f, axes = plt.subplots(4, 4)
    for i in range(4):
        for j in range(4):
            image_data = requests.get(thumbnail_urls[i+4*j])
            image_data.raise_for_status()
            image = Image.open(BytesIO(image_data.content))        
            axes[i][j].imshow(image)
            axes[i][j].axis("off")
    ```

3. Használat `plt.show()` ábra rajzolhat, és jelenítheti meg a képeket.

## <a name="example-json-response"></a>Példa JSON-válasz

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
* [Díjszabás](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) a Bing Search APIs. 
* [Ingyenes Cognitive Services hozzáférési kulcs beszerzése](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Az Azure Cognitive Services dokumentációja](https://docs.microsoft.com/azure/cognitive-services)
* [Bing Image Search API – referencia](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
