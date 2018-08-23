---
title: 'Gyors útmutató: Küldési keresési lekérdezések a REST API a Bing Image Search API, a Python használatával'
description: Ez a rövid útmutatóban küldeni keresési lekérdezések a Bing Search API a pythonnal megfelelő rendszerképek listájának beolvasása.
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 9/21/2017
ms.author: v-jerkin
ms.openlocfilehash: bc527ba39b580935f113f56aa63f7bdd283ba304
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2018
ms.locfileid: "41987617"
---
# <a name="quickstart-send-search-queries-using-the-rest-api-and-python"></a>Gyors útmutató: Küldési keresési lekérdezések a REST API-t és a Python használatával

A Bing Image Search API azáltal, hogy egy felhasználó keresési lekérdezést küld a Bing, majd az érintett rendszerképek listájának Bing.com/Images hasonló élményt nyújt.

Ez az útmutató bemutatja egy egyszerű példa, a Bing Image Search API hívása és utófeldolgozása az eredményül kapott JSON-objektum. További információkért lásd: [a Bing Képkeresés a dokumentációs](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference).

Ebben a példában-et futtathatja egy Jupyter notebookot [MyBinder](https://mybinder.org) jelvények Binder indításkor kattintva: 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingImageSearchAPI.ipynb)

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="running-the-walkthrough"></a>A forgatókönyv futtatása
A forgatókönyv folytatásához, állítsa be `subscription_key` az API-kulccsal, a Bing API-szolgáltatás.


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

Használhatjuk ezt követően a `PIL` töltse le a miniatűr képekhez könyvtárat és a `matplotlib` könyvtár \times 4$ $4 rácshoz leképezni.


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
> [Bing – Képkeresés egyoldalas alkalmazás oktatóanyag](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Lásd még 

[Bing – Képkeresés áttekintése](../overview.md)  
[Próbálja ki](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
[Ingyenes próba hozzáférési kulcs lekérése](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
[A Bing Image Search API-referencia](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
