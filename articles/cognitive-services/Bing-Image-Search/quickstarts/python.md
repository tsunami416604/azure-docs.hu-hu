---
title: 'Gyors útmutató: Küldési keresési lekérdezések a Bing Image Search API és a Python használatával'
description: Ez a rövid útmutatóban küldeni keresési lekérdezések a Bing Search API a pythonnal rendszerképek listájának beolvasása.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 8/20/2018
ms.author: aahi
ms.openlocfilehash: 42b9af2d6c7223dc3f5d269dd2003f1c99d8c7da
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45578327"
---
# <a name="quickstart-send-search-queries-using-the-rest-api-and-python"></a>Gyors útmutató: Küldési keresési lekérdezések a REST API-t és a Python használatával

Ez a rövid útmutató segítségével a Bing Image Search API az első hívását, és a egy JSON-választ kapnak. Az egyszerű Python-alkalmazás egy keresési lekérdezést küld az API-t, és megjeleníti a nyers eredményeken.

Bár ez az alkalmazás pythonban írt, az API-t az szinte bármelyik programozási nyelvével kompatibilis webes RESTful szolgáltatás.

Ebben a példában-et futtathatja egy Jupyter notebookot [MyBinder](https://mybinder.org) jelvények Binder indításkor kattintva: 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingImageSearchAPI.ipynb)


Ezenkívül az ehhez a mintához forráskódja elérhető [a Githubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingImageSearchv7.py).

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="running-the-quickstart"></a>Ez a rövid útmutató fut

Első lépésként állítsa `subscription_key` egy érvényes előfizetési kulcs, a Bing API-szolgáltatás.

```python
subscription_key = None
assert subscription_key
```

Ezután ellenőrizze, hogy a `search_url` végpont helyességéről. Csak egy végpont használható írásának pillanatában a Bing keresési API-k. Ha hitelesítési hibát tapasztal, ellenőrizze ezt az értéket az Azure irányítópultján a Bing search végponthoz.


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/images/search"
```

Állítsa be `search_term` való Kiskutyák-rendszerképeket.


```python
search_term = "puppies"
```

A következő letiltása használja a `requests` hívja a Bing Search API-k és az eredményeket JSON-objektumként Python-erőforrástárat. Figyelje meg, hogy adjuk át az API-kulcs használatával a `headers` keresztül távú szótár, és a Keresés a `params` szótárban. Tekintse meg a keresési eredmények szűrésére szolgáló beállítások teljes listáját, tekintse meg a [REST API-val](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) dokumentációját.


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "license": "public", "imageType": "photo"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

A `search_results` objektum tartalmazza a tényleges rendszerképekre, valamint gazdag metaadatokat, például kapcsolódó elemek. Ha például a következő kódsort nyerhet ki a Miniatűr URL-CÍMEK esetében az első 16 eredményeket.


```python
thumbnail_urls = [img["thumbnailUrl"] for img in search_results["value"][:16]]
```

Ezután a `PIL` töltse le a miniatűr képekhez könyvtárat és a `matplotlib` könyvtár \times 4$ $4 rácshoz leképezni.


```python
%matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

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

## <a name="sample-json-response"></a>Példa JSON-válasz

A Bing Image Search API érkező válaszokat a rendszer JSON formátumban adja vissza. Ez a minta-válasz a rendszer csonkolta egyetlen eredmény megjelenítéséhez.

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
    }
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Bing – Képkeresés egyoldalas alkalmazás oktatóanyag](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Lásd még 

* [Mi az a Bing Képkeresés?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Próbálja ki az online interaktív bemutatót](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Ingyenesen a Cognitive Services hozzáférési kulcs lekérése](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Az Azure Cognitive Services – dokumentáció](https://docs.microsoft.com/azure/cognitive-services)
* [A Bing Image Search API-referencia](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)