---
title: Bing News Search Python-ügyféltár – rövid útmutató
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.openlocfilehash: c1bd0d86a3fd9d19d67d84b9b05955421373e01e
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "79503880"
---
Ezzel a rövid útmutatóval megkezdheti a hírek keresését a Bing News Search ügyféltárpythonhoz. Bár a Bing News Search a legtöbb programozási nyelvvel kompatibilis REST API-val rendelkezik, az ügyfélkódtár egyszerű módot kínál a szolgáltatás alkalmazásokba való integrálására. A minta forráskódja megtalálható a [GitHubon.](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/news_search_samples.py)

## <a name="prerequisites"></a>Előfeltételek

* [Piton](https://www.python.org/) 2.x vagy 3.x

Javasoljuk, hogy egy [virtuális környezetet](https://docs.python.org/3/tutorial/venv.html) a python fejlesztés. A virtuális környezetet a [venv modullal](https://pypi.python.org/pypi/virtualenv)telepítheti és inicializálhatja. Telepítenie kell egy virtualenv python 2.7. Virtuális környezetet a következő kkel hozhat létre:

```console
python -m venv mytestenv
```

A Bing News Search ügyfélkönyvtár-függőségeit a következő paranccsal telepítheti:
    
```console
python -m pip install azure-cognitiveservices-search-newssearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új Python-fájlt a kedvenc IDE-ben vagy szerkesztőjében, és importálja a következő könyvtárakat. Hozzon létre egy változót az előfizetési kulcshoz és a keresési kifejezéshez.

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

2. Küldjön egy keresési lekérdezést a Hírkeresés API-ba, tárolja a választ.

    ```python
    news_result = client.news.search(query=search_term, market="en-us", count=10)
    ```

## <a name="parse-the-response"></a>A válasz feldolgozása

Ha a keresési eredmények et talál, nyomtassa ki az első weblap eredményét:

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
