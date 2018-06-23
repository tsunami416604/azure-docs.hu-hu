---
title: Python gyors üzembe helyezés az Azure kognitív szolgáltatások, a Bing videó keresési API |} Microsoft Docs
description: Get információkat és a kód minták segítségével gyorsan használatának megkezdésében a videó Bing keresési API a Microsoft Azure kognitív Services.
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 9/21/2017
ms.author: v-jerkin
ms.openlocfilehash: ce4356f05e69540bc3bc3241e2ec1751ff7a7276
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347511"
---
# <a name="quickstart-for-bing-video-search-api-with-python"></a>A Bing videó keresési API-t Python gyors üzembe helyezés

Ez a forgatókönyv bemutatja, hogyan a Bing videó keresési API-t használó, Microsoft Azure kognitív szolgáltatások részét. Tekintse meg a [API-referencia](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference) az API-k technikai részleteiért.

Futtathatja, ebben a példában Jupyter notebook [MyBinder](https://mybinder.org) az indítási kötő kattintva jelvények: 

[![Kötő](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingVideoSearchAPI.ipynb)


## <a name="prerequisites"></a>Előfeltételek
Rendelkeznie kell egy [kognitív szolgáltatások API-fiók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) rendelkező **Bing keresési API-k**. A [ingyenes próbaverzió](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) elegendő-e a gyors üzembe helyezés. Az elérési kulcsot, ha aktiválja az ingyenes próbaverzió, vagy egy fizetős kulcsot használhatja az Azure irányítópultról van szüksége.

## <a name="running-the-walkthrough"></a>A forgatókönyv futtatása

Első lépésként állítsa `subscription_key` az API-kulccsal, a Bing API-szolgáltatás.


```python
subscription_key = None
assert subscription_key
```

Ezt követően ellenőrizze, hogy a `search_url` végpont helyességéről. Csak egy végpont írásának, a Bing keresési API-k használható. Ha engedélyezési hibákat észlel, gondosan ellenőrizze ezt az értéket a Bing keresési végpont az Azure irányítópulton ellen.


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/videos/search"
```

Állítsa be `search_term` kismacskák a videók keres


```python
search_term = "kittens"
```

A következő blokkolása használja a `requests` könyvtár hívásához a Bing keresési API-kat, és térjen vissza az eredményeket a JSON-objektumként a Python. Figyelje meg, hogy azt adjon át az API-kulcs használatával a `headers` könyvtár és a keresési kifejezést keresztül a `params` szótárban. A keresés találatainak szűréséhez használt beállítások teljes listájának megtekintéséhez tekintse meg a [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference) dokumentációját.


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "count":5, "pricing": "free", "videoLength":"short"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

A `search_results` objektum tartalmazza a megfelelő videók gazdag metaadatok együtt. Megtekintheti az a videók, használja a `embedHtml` tulajdonság, és helyezze be egy `IFrame`.


```python
from IPython.display import HTML
HTML(search_results["value"][0]["embedHtml"].replace("autoplay=1","autoplay=0"))
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Lapozófájl videók](paging-videos.md)
> [Resizing és a vágás miniatűr képeket](resize-and-crop-thumbnails.md)

## <a name="see-also"></a>Lásd még 

 [A webes videók keresés](search-the-web.md) [kipróbálás](https://azure.microsoft.com/services/cognitive-services/bing-video-search-api/)
