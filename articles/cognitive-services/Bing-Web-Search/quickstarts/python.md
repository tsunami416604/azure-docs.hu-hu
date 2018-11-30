---
title: 'Rövid útmutató: Keresés a Python használatával – Bing Web Search API'
titleSuffix: Azure Cognitive Services
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhatja létre első Bing Web Search API-hívását a Python használatával, majd hogyan fogadhatja a JSON-választ.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: quickstart
ms.date: 8/16/2018
ms.author: aahi
ms.openlocfilehash: 0f6f3991e01e4eb6919d958002ef6230a2570dbe
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52309454"
---
# <a name="quickstart-use-python-to-call-the-bing-web-search-api"></a>Rövid útmutató: A Bing Web Search API meghívása a Python segítségével  
A [Cognitive Services hozzáférési kulcsát](https://azure.microsoft.com/try/cognitive-services/) a **Keresés** területen kérheti le.  Lásd még: [a Cognitive Services díjszabás – keresési Bing-API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

Ebből a rövid útmutatóból megtudhatja, hogyan hozhatja létre 10 perc alatt az első Bing Web Search API-hívását, majd hogyan fogadhatja a JSON-választ.  

[!INCLUDE [bing-web-search-quickstart-signup](../../../../includes/bing-web-search-quickstart-signup.md)]

Lásd még: [a Cognitive Services díjszabás – keresési Bing-API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

Ez a példa Jupyter-notebookként van futtatva a [MyBinderben](https://mybinder.org). Kattintson a Binder indítása jelvényre:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingWebSearchAPI.ipynb)

## <a name="define-variables"></a>Változók meghatározása

Cserélje le a `subscription_key` értékét egy érvényes előfizetői azonosítóra az Azure-fiókjából.

```python
subscription_key = "YOUR_ACCESS_KEY"
assert subscription_key
```

Deklarálja a Bing Web Search API végpontját. Ha hitelesítési hibákba ütközik, ellenőrizze újra ezt az értéket az Azure-irányítópulton lévő Bing-keresési végponttal összevetve.

```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/search"
```

Nyugodtan testreszabhatja a keresési lekérdezést a `search_term` értékének lecserélésével.

```python
search_term = "Azure Cognitive Services"
```

## <a name="make-a-request"></a>Kérés indítása

Ez a blokk a `requests` kódtárat használja a Bing Web Search API meghívásához, és JSON-objektumként adja vissza az eredményeket. Az API-kulcs a `headers` szótárban lesz átadva, a keresési kifejezést és a lekérdezés paramétereit pedig a `params` szótárban. A beállítások és paraméterek teljes listáját a [Bing Web Search API 7-es verziójának](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference) dokumentációjában találja meg.

```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "textDecorations":True, "textFormat":"HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

## <a name="format-and-display-the-response"></a>A válasz formázása és megjelenítése

A `search_results` objektum tartalmazza a keresési eredményeket és a metaadatokat, például a kapcsolódó lekérdezéseket és lapokat. Ez a kód az `IPython.display` kódtár segítségével formázza és jeleníti meg a választ a böngészőjében.

```python
from IPython.display import HTML

rows = "\n".join(["""<tr>
                       <td><a href=\"{0}\">{1}</a></td>
                       <td>{2}</td>
                     </tr>""".format(v["url"],v["name"],v["snippet"]) \
                  for v in search_results["webPages"]["value"]])
HTML("<table>{0}</table>".format(rows))
```

## <a name="sample-code-on-github"></a>Mintakód a GitHubon

Ha szeretné ezt a kódot helyben futtatni, a teljes [mintát megtalálja a GitHubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingWebSearchv7.js).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas alkalmazás-oktatóanyag a Bing Web Search használatához](../tutorial-bing-web-search-single-page-app.md)

[!INCLUDE [bing-web-search-quickstart-see-also](../../../../includes/bing-web-search-quickstart-see-also.md)]
