---
title: 'Rövid útmutató: Bing News Search API, Python'
titlesuffix: Azure Cognitive Services
description: Információk és kódminták segítségével ismerkedhet meg a Bing News Search API használatának első lépéseivel.
services: cognitive-services
author: v-jerkin
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: quickstart
ms.date: 9/21/2017
ms.author: v-jerkin
ms.openlocfilehash: 583b304a742d9abfd799442c9aa2999ad6783a34
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48803545"
---
# <a name="quickstart-for-bing-news-search-api-with-python"></a>Rövid útmutató a Bing News Search API és a Python használatához
Ez az útmutató a Bing News Search API meghívásának és a kapott JSON-objektum utófeldolgozásnak egy egyszerű példáját mutatja be. További információ: [A Bing News Search dokumentációja](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference).  

Ezt a példát futtathatja Jupyter-notebookként a [MyBinderen](https://mybinder.org), az indítás Binder-jelvényére kattintva. 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingNewsSearchAPI.ipynb)

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
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
```

Állítsa be a `search_term` objektumot úgy, hogy a Microsoftról keressen híreket.


```python
search_term = "Microsoft"
```

A következő blokk a Python `requests` kódtárát használja a Bing keresési API-k meghívásához, és JSON-objektumként adja vissza az eredményeket. Figyelje meg, hogy az API-kulcs átadása a `headers` szótárban történik, a keresési kifejezés pedig a `params` szótárban. A keresési eredmények szűrésénél alkalmazható összes lehetőség teljes listáját a [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference) dokumentációjában találja.


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

A `search_results` objektum tartalmazza a releváns híreket és részletes metaadatokat. Az alábbi kódsor például a cikkek leírásait nyeri ki.


```python
descriptions = [article["description"] for article in search_results["value"]]
```

Ezek a leírások ezután táblaként jeleníthetők meg **félkövérrel** szedett keresési kulcsszóval.


```python
from IPython.display import HTML
rows = "\n".join(["<tr><td>{0}</td></tr>".format(desc) for desc in descriptions])
HTML("<table>"+rows+"</table>")
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Hírek lapozása](paging-news.md)
> [Díszítő megjelölések használata szövegkiemeléshez](hit-highlighting.md)

## <a name="see-also"></a>Lásd még 

 [Hírek keresése az interneten](search-the-web.md)  
 [Kipróbálás](https://azure.microsoft.com/services/cognitive-services/bing-news-search-api/)
