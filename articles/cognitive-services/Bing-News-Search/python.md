---
title: 'Gyors útmutató: Hírek keresése a Pythonban és a Bing News Search REST API'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval kérést küldhet a Bing News Search REST API a Python használatával, és JSON-választ kap.
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
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75448496"
---
# <a name="quickstart-perform-a-news-search-using-python-and-the-bing-news-search-rest-api"></a>Gyors útmutató: Hírek keresése a Python és a Bing News Search használatával REST API

Ezzel a rövid útmutatóval megteheti az első hívását a Bing News Search API, és JSON-választ kap. Ez az egyszerű JavaScript-alkalmazás keresési lekérdezést küld az API-nak, és feldolgozza az eredményeket. Habár ez az alkalmazás Pythonban íródott, az API egy REST-alapú webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel.

Ezt a mintakód Jupyter jegyzetfüzetként is futtathatja a [MyBinder](https://mybinder.org) a következő hivatkozásra kattintva:. 

[![Iratgyűjtő](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingNewsSearchAPI.ipynb)

A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingNewsSearchv7.py)is elérhető.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új Python-fájlt a kedvenc IDE vagy szerkesztőben, és importálja a kérelem modulját. Hozzon létre változókat az előfizetési kulcshoz, a végponthoz és a keresési kifejezéshez. Használhatja az alábbi globális végpontot, vagy az erőforráshoz tartozó Azure Portalban megjelenő [Egyéni altartomány](../../cognitive-services/cognitive-services-custom-subdomains.md) végpontot.

```python
import requests

subscription_key = "your subscription key"
search_term = "Microsoft"
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
```

### <a name="create-parameters-for-the-request"></a>Paraméterek létrehozása a kérelemhez

1. Adja hozzá az előfizetési kulcsot egy új szótárhoz `"Ocp-Apim-Subscription-Key"` , a kulcsként használva. Tegye meg ugyanezt a keresési paramétereknél.

    ```python
    headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
    params  = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
    ```

## <a name="send-a-request-and-get-a-response"></a>Kérelem küldése és Válasz kérése

1. A kérelmek könyvtárával hívja meg a Bing Visual Search API az előfizetési kulccsal, valamint az utolsó lépésben létrehozott szótár-objektumokkal.

    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = response.json()
    ```

2. `search_results`az API-ból származó választ JSON-objektumként tartalmazza. A válaszban szereplő cikkek leírásának elérése.
    
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
