---
title: 'Rövid útmutató: Hírkeresés végrehajtása a Pythonnal és a Bing News Search REST API-val'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval kérést küldhet a Bing News Search REST API-nak a Python használatával, és JSON-választ kaphat.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 12/12/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 1c424c75a4df193ec412355607c68abeda0560a5
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75448496"
---
# <a name="quickstart-perform-a-news-search-using-python-and-the-bing-news-search-rest-api"></a>Rövid útmutató: Hírkeresés végrehajtása a Python és a Bing News Search REST API használatával

Ezzel a rövid útmutatóval elsőalkalommal hívhatja meg a Bing News Search API-t, és JSON-választ kaphat. Ez az egyszerű JavaScript-alkalmazás keresési lekérdezést küld az API-nak, és feldolgozza az eredményeket. Bár ez az alkalmazás python nyelven íródott, az API egy RESTful webszolgáltatás kompatibilis a legtöbb programozási nyelvek.

Ezt a kódmintát Jupyter notebookként futtathatja a [MyBinder-en,](https://mybinder.org) ha rákattint az indító iratgyűjtő jelvényre: 

[![Iratgyűjtő](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingNewsSearchAPI.ipynb)

A minta forráskódja a [GitHubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingNewsSearchv7.py)is elérhető.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új Python-fájlt a kedvenc IDE-ben vagy szerkesztőjében, és importálja a kérelemmodult. Hozzon létre változókat az előfizetési kulcshoz, a végponthoz és a keresési kifejezéshez. Használhatja az alábbi globális végpontot, vagy az [egyéni altartomány-végpontot,](../../cognitive-services/cognitive-services-custom-subdomains.md) amely az azure-portálon jelenik meg az erőforráshoz.

```python
import requests

subscription_key = "your subscription key"
search_term = "Microsoft"
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
```

### <a name="create-parameters-for-the-request"></a>Paraméterek létrehozása a kérelemhez

1. Adja hozzá az előfizetési kulcsot `"Ocp-Apim-Subscription-Key"` egy új szótárhoz, kulcsként használva. Tegye ugyanezt a keresési paraméterekért is.

    ```python
    headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
    params  = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
    ```

## <a name="send-a-request-and-get-a-response"></a>Kérés küldése és válasz kérése

1. A kérelmek tárában hívja meg a Bing Visual Search API-t az előfizetési kulcs használatával, és az utolsó lépésben létrehozott szótárobjektumokat.

    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = response.json()
    ```

2. `search_results`az API válaszát JSON-objektumként tartalmazza. A válaszban szereplő cikkek leírásának elérése.
    
    ```python
    descriptions = [article["description"] for article in search_results["value"]]
    ```

## <a name="displaying-the-results"></a>Az eredmények megjelenítése

Ezek a leírások ezután táblaként jeleníthetők meg **félkövérrel** szedett keresési kulcsszóval.

```python
from IPython.display import HTML
rows = "\n".join(["<tr><td>{0}</td></tr>".format(desc)
                  for desc in descriptions])
HTML("<table>"+rows+"</table>")
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas webalkalmazás létrehozása](tutorial-bing-news-search-single-page-app.md)
