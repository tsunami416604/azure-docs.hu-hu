---
title: 'Gyors útmutató: Hajtsa végre a Pythonnal – Bing News Search REST API egy hírkeresés'
titlesuffix: Azure Cognitive Services
description: Ez a rövid útmutató segítségével egy kérelmet küld a Bing News Search REST API-be a Python, és a egy JSON-választ kap.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 1/10/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: f6ae2315c14a92d72d2efa03b96397512120ab06
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55866236"
---
# <a name="quickstart-perform-a-news-search-using-python-and-the-bing-news-search-rest-api"></a>Gyors útmutató: Hajtsa végre a hírkeresési, Python és a Bing News Search REST API használatával

Ez a rövid útmutató segítségével a Bing News Search API az első hívását, és a egy JSON-választ kapnak. Ez az egyszerű JavaScript alkalmazás egy keresési lekérdezést küld az API-t, és feldolgozza az eredményeket. Bár ez az alkalmazás pythonban írt, az API-t-e a webes RESTful szolgáltatás kompatibilis szinte bármelyik programozási nyelvével.

Ez a kódminta-et futtathatja egy Jupyter notebookot [MyBinder](https://mybinder.org) jelvények Binder indításkor kattintva: 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingNewsSearchAPI.ipynb)

Ehhez a mintához forráskódja is elérhető a [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingNewsSearchv7.py).

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

Lásd még: [a Cognitive Services díjszabás – keresési Bing-API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új Python-fájlt a kedvenc integrált Fejlesztőkörnyezetével vagy szerkesztőjével, és importálja a modult a kérelmet. Hozzon létre változókat az előfizetési kulcs, a végpont és a egy keresési kifejezést. A végpont találhatja meg az Azure irányítópultján.

```python
import requests

subscription_key = "your subscription key"
search_term = "Microsoft"
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
```

### <a name="create-parameters-for-the-request"></a>A kérelem paramétereinek létrehozása

1. Az előfizetési kulcs hozzáadása egy új szótár segítségével `"Ocp-Apim-Subscription-Key"` kulcsként. Használja ugyanazt a keresési paraméterek.

    ```python
    headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
    params  = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
    ```

## <a name="send-a-request-and-get-a-response"></a>Kérés küldése és válaszol

1. A kérések library használatával hívja meg a Bing Visual Search API az előfizetési kulcs és az előző lépésben létrehozott szótár objektumok használatával.

    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = response.json()
    ```

2. `search_results` az API-ból a válasz tartalmazza a JSON-objektumként. A cikkek a válaszban szereplő leírásait eléréséhez.
    
    ```python
    descriptions = [article["description"] for article in search_results["value"]]
    ```

## <a name="displaying-the-results"></a>Az eredmények megjelenítése

Ezek a leírások ezután táblaként jeleníthetők meg **félkövérrel** szedett keresési kulcsszóval.

```python
from IPython.display import HTML
rows = "\n".join(["<tr><td>{0}</td></tr>".format(desc) for desc in descriptions])
HTML("<table>"+rows+"</table>")
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
[Egyoldalas webalkalmazás létrehozása](tutorial-bing-news-search-single-page-app.md)
