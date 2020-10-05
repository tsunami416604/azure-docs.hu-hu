---
title: Bing News Search Python ügyféloldali kódtár gyors üzembe helyezése
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.openlocfilehash: c1bd0d86a3fd9d19d67d84b9b05955421373e01e
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "79503880"
---
Ezzel a rövid útmutatóval megkezdheti a hírek keresését a Pythonhoz készült Bing News Search ügyféloldali kódtár használatával. Habár a Bing News Search REST API kompatibilis a legtöbb programozási nyelvvel, az ügyféloldali kódtár egyszerű módszert kínál a szolgáltatás integrálására az alkalmazásokba. A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/news_search_samples.py)található.

## <a name="prerequisites"></a>Előfeltételek

* [Python](https://www.python.org/) 2. x vagy 3. x

Javasoljuk, hogy használjon egy [virtuális környezetet](https://docs.python.org/3/tutorial/venv.html) a Python-fejlesztéshez. A virtuális környezetet a [venv modul](https://pypi.python.org/pypi/virtualenv)használatával telepítheti és inicializálhatja. Telepítenie kell egy virtualenv a Python 2,7-hez. Létrehozhat egy virtuális környezetet a használatával:

```console
python -m venv mytestenv
```

A Bing News Search ügyféloldali függvénytár függőségei a következő paranccsal telepíthetők:
    
```console
python -m pip install azure-cognitiveservices-search-newssearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új Python-fájlt a kedvenc IDE vagy szerkesztőben, és importálja a következő könyvtárakat. Hozzon létre egy változót az előfizetési kulcshoz és a keresett kifejezéshez.

    ```python
    from azure.cognitiveservices.search.newssearch import NewsSearchClient
    from msrest.authentication import CognitiveServicesCredentials
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    endpoint = "YOUR-ENDPOINT"
    search_term = "Quantum Computing"
    ```

## <a name="initialize-the-client-and-send-a-request"></a>Az ügyfél inicializálása és kérelem küldése

1. Hozza létre a `CognitiveServicesCredentials` egy példányát.
    
    ```python
    client = NewsSearchClient(endpoint=endpoint, credentials=CognitiveServicesCredentials(subscription_key))
    ```

2. Keresési lekérdezés küldése a News Search API-nak, tárolja a választ.

    ```python
    news_result = client.news.search(query=search_term, market="en-us", count=10)
    ```

## <a name="parse-the-response"></a>A válasz feldolgozása

Ha keresési eredmények találhatók, nyomtassa ki az első weblap eredményét:

```python
if news_result.value:
    first_news_result = news_result.value[0]
    print("Total estimated matches value: {}".format(
        news_result.total_estimated_matches))
    print("News result count: {}".format(len(news_result.value)))
    print("First news name: {}".format(first_news_result.name))
    print("First news url: {}".format(first_news_result.url))
    print("First news description: {}".format(first_news_result.description))
    print("First published time: {}".format(first_news_result.date_published))
    print("First news provider: {}".format(first_news_result.provider[0].name))
else:
    print("Didn't see any news result data..")
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas webalkalmazás létrehozása](../../tutorial-bing-news-search-single-page-app.md)
