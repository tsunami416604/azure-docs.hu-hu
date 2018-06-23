---
title: Hívás-válasz - Python gyors üzembe helyezés az Azure kognitív szolgáltatások, a Bing kép Search API |} Microsoft Docs
description: Get információkat és a kód minták segítségével gyorsan használatának megkezdésében a kép Bing keresési API a Microsoft Azure kognitív Services.
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 9/21/2017
ms.author: v-jerkin
ms.openlocfilehash: 3b5d6a961ce4bcde8aaf73f1fbd30689a6c2c2d1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347515"
---
# <a name="call-and-response-your-first-bing-image-search-query-in-python"></a>Hívás-válasz: a Python első Bing kép keresési lekérdezés

A kép Bing keresési API Bing.com/Images hasonló élményt nyújt akkor azáltal, hogy egy felhasználó keresési lekérdezés küldése a Bing, és vissza megfelelő képek listájának lekérése.

Ez a forgatókönyv leírja azokat a kép Bing keresési API hívása és utófeldolgozási az eredményül kapott JSON-objektum egy egyszerű példa. További információkért lásd: [Bing kép keresési dokumentáció](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference).

Futtathatja, ebben a példában Jupyter notebook [MyBinder](https://mybinder.org) az indítási kötő kattintva jelvények: 

[![Kötő](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingImageSearchAPI.ipynb)

## <a name="prerequisites"></a>Előfeltételek

Rendelkeznie kell egy [kognitív szolgáltatások API-fiók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) rendelkező **Bing keresési API-k**. A [ingyenes próbaverzió](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) elegendő-e a gyors üzembe helyezés. Az elérési kulcsot, ha aktiválja az ingyenes próbaverzió, vagy egy fizetős kulcsot használhatja az Azure irányítópultról van szüksége.

## <a name="running-the-walkthrough"></a>A forgatókönyv futtatása
A forgatókönyv folytatásához, állítsa be `subscription_key` az API-kulccsal, a Bing API-szolgáltatás.


```python
subscription_key = None
assert subscription_key
```

Ezt követően ellenőrizze, hogy a `search_url` végpont helyességéről. Csak egy végpont írásának, a Bing keresési API-k használható. Ha engedélyezési hibákat észlel, gondosan ellenőrizze ezt az értéket a Bing keresési végpont az Azure irányítópulton ellen.


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/images/search"
```

Állítsa be `search_term` Kiskutyák képeket keres.


```python
search_term = "puppies"
```

A következő blokkolása használja a `requests` könyvtár hívásához a Bing keresési API-kat, és térjen vissza az eredményeket a JSON-objektumként a Python. Figyelje meg, hogy azt adjon át az API-kulcs használatával a `headers` könyvtár és a keresési kifejezést keresztül a `params` szótárban. A keresés találatainak szűréséhez használt beállítások teljes listájának megtekintéséhez tekintse meg a [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) dokumentációját.


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "license": "public", "imageType": "photo"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

A `search_results` objektum tartalmazza a tényleges képek gazdag metaadatokat, például kapcsolódó elemek mellett. Például a következő kódsort nyerhet a Miniatűr URL-CÍMÉNEK az első 16 eredmények.


```python
thumbnail_urls = [img["thumbnailUrl"] for img in search_results["value"][:16]]
```

Ezt követően is használhatók a `PIL` szalagtárhoz, töltse le a miniatűr képeket és a `matplotlib` könyvtár \times 4$ $4 rácshoz megjelenítéséhez.


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
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Bing kép keresési alkalmazás oktatóanyag](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Lásd még 

[Bing kép keresési áttekintése](../overview.md)  
[Próbálja ki](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
[Egy ingyenes próba hozzáférési kulcs beszerzése](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
[Bing kép keresési API-referencia](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
