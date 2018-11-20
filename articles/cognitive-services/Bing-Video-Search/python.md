---
title: 'Rövid útmutató: Bing Video Search, Python'
titlesuffix: Azure Cognitive Services
description: Információk és kódminták segítségével ismerkedhet meg a Bing Video Search API használatának első lépéseivel.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: quickstart
ms.date: 9/21/2017
ms.author: aahi
ms.openlocfilehash: ccc27481289ffc686e3e480685ba421c762e3718
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2018
ms.locfileid: "52161079"
---
# <a name="quickstart-bing-video-search-api-with-python"></a>Rövid útmutató: Bing Video Search API Python használatával

Ez az útmutató bemutatja, hogyan használható a Microsoft Cognitive Services részét képező Bing Video Search API az Azure-on. Az API-k technikai részleteit az [API-referencia](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference) című részben tekintheti meg.

Ezt a példát futtathatja Jupyter-notebookként a [MyBinderen](https://mybinder.org), az indítás Binder-jelvényére kattintva: 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingVideoSearchAPI.ipynb)


## <a name="prerequisites"></a>Előfeltételek
Egy **Bing Search API-kat** tartalmazó [Cognitive Services API-fiókkal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) kell rendelkeznie. Az [ingyenes próbaverzió](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) elegendő ehhez a rövid útmutatóhoz. Szüksége lesz az ingyenes próbaverzió aktiválásakor kapott hozzáférési kulcsra, vagy beszerezhet egy fizetős előfizetői azonosítót az Azure-irányítópultról.

## <a name="running-the-walkthrough"></a>Az útmutató futtatása

Először állítsa a `subscription_key` értékét a Bing API-szolgáltatáshoz kapott API-kulcsára.


```python
subscription_key = None
assert subscription_key
```

Ellenőrizze, hogy helyes-e a `search_url` végpont. A jelen dokumentum írásakor a rendszer csak egy végpontot használ a Bing keresési API-khoz. Ha hitelesítési hibákba ütközik, ellenőrizze újra ezt az értéket az Azure-irányítópulton lévő Bing-keresési végponttal összevetve.


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/videos/search"
```

A `search_term` értékének beállítása cicás videók kereséséhez


```python
search_term = "kittens"
```

A következő blokk a Python `requests` kódtárát használja a Bing keresési API-k meghívásához, és JSON-objektumként adja vissza az eredményeket. Figyelje meg, hogy az API-kulcs átadása a `headers` szótárban történik, a keresési kifejezés pedig a `params` szótárban. A keresési eredmények szűrésénél alkalmazható összes lehetőség teljes listáját a [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference) dokumentációjában találja.


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "count":5, "pricing": "free", "videoLength":"short"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

A `search_results` objektum tartalmazza a releváns videókat és részletes metaadatokat. Az egyik videó megtekintéséhez használja az `embedHtml` tulajdonságát, és illessze be egy `IFrame`-be.


```python
from IPython.display import HTML
HTML(search_results["value"][0]["embedHtml"].replace("autoplay=1","autoplay=0"))
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Videók lapozása](paging-videos.md)
> [Miniatűrképek átméretezése és levágása](resize-and-crop-thumbnails.md)

## <a name="see-also"></a>Lásd még 

 [Videók keresése az interneten](search-the-web.md) [Kipróbálás](https://azure.microsoft.com/services/cognitive-services/bing-video-search-api/)
