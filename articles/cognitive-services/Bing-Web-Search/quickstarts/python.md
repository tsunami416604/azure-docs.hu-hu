---
title: Hívás-válasz - Python gyors üzembe helyezés az Azure kognitív szolgáltatások, a Bing webes keresés API |} Microsoft Docs
description: Get információkat és a kód minták segítségével gyorsan használatának megkezdésében a webes Bing keresési API a Microsoft Azure kognitív Services.
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 9/18/2017
ms.author: v-jerkin
ms.openlocfilehash: 8d4df9db60c7a74a5b9e53d4622528c0054b4f19
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347575"
---
# <a name="call-and-response-your-first-bing-web-search-query-in-python"></a>Hívás-válasz: a Python első Bing keresést a lekérdezés

A webes Bing keresési API vissza a találatok között, amely meghatározza a Bing kapcsolódik a lekérdezés a felhasználó Bing.com/Search hasonló élményt nyújt. Az eredmények lehet, hogy tartalmazzák a weblapok, képek, videók, híreket és entitások, kapcsolódó keresési lekérdezések, helyesen adta-e javításokat, időzónák, egység átalakítás, fordítások és számítások együtt. A kapott eredmények típusú azok relevanciájának és a réteg a Bing keresési API-k riasztásról alapulnak.

Tekintse meg a [API-referencia](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference) az API-k technikai részleteiért.

Futtathatja, ebben a példában Jupyter notebook [MyBinder](https://mybinder.org) az indítási kötő kattintva jelvények: 

[![Kötő](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingWebSearchAPI.ipynb)


## <a name="prerequisites"></a>Előfeltételek
Rendelkeznie kell egy [kognitív szolgáltatások API-fiók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) rendelkező **Bing keresési API-k**. A [ingyenes próbaverzió](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) elegendő-e a gyors üzembe helyezés. Az elérési kulcsot, ha aktiválja az ingyenes próbaverzió, vagy egy fizetős kulcsot használhatja az Azure irányítópultról van szüksége.

## <a name="running-the-walkthrough"></a>A forgatókönyv futtatása

Állítsa be `subscription_key` az API-kulccsal, a Bing API-szolgáltatás.


```python
subscription_key = None
assert subscription_key
```

Ezt követően ellenőrizze, hogy a `search_url` végpont helyességéről. Csak egy végpont írásának, a Bing keresési API-k használható. Ha engedélyezési hibákat észlel, gondosan ellenőrizze ezt az értéket a Bing keresési végpont az Azure irányítópulton ellen.


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/search"
```

Állítsa be `search_term` lekérdezéshez Bing Microsoft kognitív számára.


```python
search_term = "Microsoft Cognitive Services"
```

A következő blokkolása használja a `requests` könyvtár hívásához a Bing keresési API-kat, és térjen vissza az eredményeket a JSON-objektumként a Python. Figyelje meg, hogy azt adjon át az API-kulcs használatával a `headers` könyvtár és a keresési kifejezést keresztül a `params` szótárban. A keresés találatainak szűréséhez használt beállítások teljes listájának megtekintéséhez tekintse meg a [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference) dokumentációját.


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "textDecorations":True, "textFormat":"HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

A `search_results` objektum tartalmazza a keresési eredmények gazdag metaadatokat, például kapcsolódó lekérdezések és a lapok együtt. Az alábbi kódsorokat a lekérdezés által visszaadott felső oldalak formátumban.


```python
from IPython.display import HTML

rows = "\n".join(["""<tr>
                       <td><a href=\"{0}\">{1}</a></td>
                       <td>{2}</td>
                     </tr>""".format(v["url"],v["name"],v["snippet"]) \
                  for v in search_results["webPages"]["value"]])
HTML("<table>{0}</table>".format(rows))
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Bing webes keresés alkalmazás oktatóanyag](../tutorial-bing-web-search-single-page-app.md)

## <a name="see-also"></a>Lásd még 

[Bing webes keresés – áttekintés](../overview.md)  
[Próbálja ki](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/)  
[Egy ingyenes próba hozzáférési kulcs lekérése](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)
[Bing webes keresési API-referencia](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)
