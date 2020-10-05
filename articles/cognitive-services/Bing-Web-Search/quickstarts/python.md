---
title: 'Rövid útmutató: Keresés a Python használatával – Bing Web Search API'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval kéréseket küldhet a Bing Web Search REST API a Python használatával, és JSON-választ kap
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.author: aahi
ms.custom: seodec2018, devx-track-python
ms.openlocfilehash: 1adb273cfedd2342a1429f0d21cd00e9a5e602a8
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "87851171"
---
# <a name="quickstart-use-python-to-call-the-bing-web-search-api"></a>Rövid útmutató: A Bing Web Search API meghívása a Python segítségével  

Ezzel a rövid útmutatóval megteheti az első hívást a Bing Web Search API. Ez a Python-alkalmazás keresési kérelmet küld az API-nak, és megjeleníti a JSON-választ. Bár az alkalmazás Pythonban íródott, az API egy REST-alapú webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel.

Ez a példa Jupyter-notebookként van futtatva a [MyBinderben](https://mybinder.org). A futtatásához válassza az indítási kötés jelvényét:

[![Iratgyűjtő](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingWebSearchAPI.ipynb)

## <a name="prerequisites"></a>Előfeltételek

* [Python 2. x vagy 3. x](https://www.python.org/)

[!INCLUDE [bing-web-search-quickstart-signup](../../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="define-variables"></a>Változók meghatározása

1. Cserélje le a `subscription_key` értékét egy érvényes előfizetői azonosítóra az Azure-fiókjából.

   ```python
   subscription_key = "YOUR_ACCESS_KEY"
   assert subscription_key
   ```

2. Deklarálja a Bing Web Search API végpontját. Használhatja a globális végpontot a következő kódban, vagy használhatja az erőforráshoz tartozó Azure Portalban megjelenő [Egyéni altartomány](../../../cognitive-services/cognitive-services-custom-subdomains.md) -végpontot.

   ```python
   search_url = "https://api.cognitive.microsoft.com/bing/v7.0/search"
   ```

3. Ha szeretné, testreszabhatja a keresési lekérdezést úgy, hogy lecseréli a értéket `search_term` .

   ```python
   search_term = "Azure Cognitive Services"
   ```

## <a name="make-a-request"></a>Kérés indítása

Ez a kód a `requests` könyvtárat használja a Bing Web Search API meghívására és az eredmények JSON-objektumként való visszaküldésére. Az API-kulcs a `headers` szótárban lesz átadva, a keresési kifejezést és a lekérdezés paramétereit pedig a `params` szótárban. 

A beállítások és paraméterek teljes listáját itt tekintheti meg: [Bing Web Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference).

```python
import requests

headers = {"Ocp-Apim-Subscription-Key": subscription_key}
params = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

## <a name="format-and-display-the-response"></a>A válasz formázása és megjelenítése

Az `search_results` objektum tartalmazza a keresési eredményeket, és az ilyen metaadatokat kapcsolódó lekérdezések és lapok. Ez a kód az `IPython.display` kódtár segítségével formázza és jeleníti meg a választ a böngészőjében.

```python
from IPython.display import HTML

rows = "\n".join(["""<tr>
                       <td><a href=\"{0}\">{1}</a></td>
                       <td>{2}</td>
                     </tr>""".format(v["url"], v["name"], v["snippet"])
                  for v in search_results["webPages"]["value"]])
HTML("<table>{0}</table>".format(rows))
```

## <a name="sample-code-on-github"></a>Mintakód a GitHubon

A kód helyi futtatásához tekintse meg a teljes mintát, amely [elérhető a githubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingWebSearchv7.py).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Bing Web Search API egyoldalas alkalmazás oktatóanyaga](../tutorial-bing-web-search-single-page-app.md)

[!INCLUDE [bing-web-search-quickstart-see-also](../../../../includes/bing-web-search-quickstart-see-also.md)]
