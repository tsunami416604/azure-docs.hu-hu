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
ms.date: 05/22/2020
ms.author: aahi
ms.custom: seodec2018, tracking-python
ms.openlocfilehash: 37571113be715c7eb6b120aa592b5a2422a08ad8
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84609998"
---
# <a name="quickstart-perform-a-news-search-using-python-and-the-bing-news-search-rest-api"></a>Gyors útmutató: Hírek keresése a Python és a Bing News Search használatával REST API

Ezzel a rövid útmutatóval megteheti az első hívást a Bing News Search API. Ez az egyszerű Python-alkalmazás keresési lekérdezést küld az API-nak, és feldolgozza a JSON-eredményt. 

Bár az alkalmazás Pythonban íródott, az API egy REST-alapú webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel.

Ha ezt a kódrészletet Jupyter jegyzetfüzetként szeretné futtatni a [MyBinder](https://mybinder.org), válassza az **indítási kötés** jelvényét: 

[![kötés indítása](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingNewsSearchAPI.ipynb)

A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingNewsSearchv7.py)is elérhető.

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

Hozzon létre egy új Python-fájlt a kedvenc IDE vagy szerkesztőben, és importálja a kérelem modulját. Hozzon létre változókat az előfizetési kulcshoz, a végponthoz és a keresési kifejezéshez. Használhatja a globális végpontot a következő kódban, vagy használhatja az erőforráshoz tartozó Azure Portalban megjelenő [Egyéni altartomány](../../cognitive-services/cognitive-services-custom-subdomains.md) -végpontot.

```python
import requests

subscription_key = "your subscription key"
search_term = "Microsoft"
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
```

## <a name="create-parameters-for-the-request"></a>Paraméterek létrehozása a kérelemhez

Adja hozzá az előfizetési kulcsot egy új szótárhoz, `Ocp-Apim-Subscription-Key` a kulcsként használva. Tegye meg ugyanezt a keresési paramétereknél.

```python
headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
```

## <a name="send-a-request-and-get-a-response"></a>Kérelem küldése és Válasz kérése

1. A kérelmek könyvtárával hívja meg a Bing Visual Search API az előfizetési kulccsal, valamint az előző lépésben létrehozott szótári objektumokat.

    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = response.json()
    ```

2. Hozzáférés az API-tól kapott válaszban szereplő cikkek leírásához, amely JSON-objektumként van tárolva `search_results` . 
    
    ```python
    descriptions = [article["description"] for article in search_results["value"]]
    ```

## <a name="display-the-results"></a>Az eredmények megjelenítése

Ezek a leírások ezután táblaként jeleníthetők meg félkövérrel szedett keresési kulcsszóval.

```python
from IPython.display import HTML
rows = "\n".join(["<tr><td>{0}</td></tr>".format(desc)
                  for desc in descriptions])
HTML("<table>"+rows+"</table>")
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Egyoldalas webalkalmazás létrehozása](tutorial-bing-news-search-single-page-app.md)
