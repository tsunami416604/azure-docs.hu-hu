---
title: Python gyors üzembe helyezés az Azure kognitív szolgáltatások, a Bing hírek keresési API |} Microsoft Docs
description: Get információkat és a kód minták segítségével gyorsan Ismerkedés a Bing hírek Search API használatával a Microsoft Azure kognitív Services.
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 9/21/2017
ms.author: v-jerkin
ms.openlocfilehash: 0fde478b650513aa1527c1d47f5b453ba094506c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347519"
---
# <a name="quickstart-for-bing-news-search-api-with-python"></a>A Bing hírek keresési API-t Python gyors üzembe helyezés
Ez a forgatókönyv leírja azokat a Bing hírek keresési API-JÁNAK hívása és utófeldolgozási az eredményül kapott JSON-objektum egy egyszerű példa. További információkért lásd: [Bing új keresés dokumentáció](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference).  

Futtathatja, ebben a példában Jupyter notebook [MyBinder](https://mybinder.org) az indítási kötő kattintva jelvények: 

[![Kötő](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingNewsSearchAPI.ipynb)

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
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
```

Állítsa be `search_term` kapcsolatos Microsoft írt hírcikkeket keres.


```python
search_term = "Microsoft"
```

A következő blokkolása használja a `requests` könyvtár hívásához a Bing keresési API-kat, és térjen vissza az eredményeket a JSON-objektumként a Python. Figyelje meg, hogy azt adjon át az API-kulcs használatával a `headers` könyvtár és a keresési kifejezést keresztül a `params` szótárban. A keresés találatainak szűréséhez használt beállítások teljes listájának megtekintéséhez tekintse meg a [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference) dokumentációját.


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

A `search_results` objektum tartalmazza a megfelelő új cikkek gazdag metaadatok együtt. Például a következő kódsort kibontja a cikkek leírását.


```python
descriptions = [article["description"] for article in search_results["value"]]
```

A leírások majd megjeleníthetők táblázatként kiemelt keresési kulcsszóval **félkövér**.


```python
from IPython.display import HTML
rows = "\n".join(["<tr><td>{0}</td></tr>".format(desc) for desc in descriptions])
HTML("<table>"+rows+"</table>")
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Lapozófájl hírek](paging-news.md)
> [szöveg kiemeléséhez decoration jelölők használatával](hit-highlighting.md)

## <a name="see-also"></a>Lásd még 

 [A webes hírek a keresés](search-the-web.md)  
 [Próbálja ki](https://azure.microsoft.com/services/cognitive-services/bing-news-search-api/)
