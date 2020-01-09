---
title: 'Rövid útmutató: Hírek keresése a Pythonhoz készült SDK használatával – Bing News Search'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval híreket kereshet a Pythonhoz készült Bing News Search SDK-val, és feldolgozhatja a választ.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 12/12/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: b84b5ee8682007191953bef34579973c7c24ca45
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448515"
---
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-python"></a>Gyors útmutató: Hírek keresése a Pythonhoz készült Bing News Search SDK-val

Ezzel a rövid útmutatóval megkezdheti a hírek keresését a Pythonhoz készült Bing News Search SDK-val. Habár a Bing News Search REST API kompatibilis a legtöbb programozási nyelvvel, az SDK egyszerű módszert kínál a szolgáltatás integrálására az alkalmazásokba. A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/news_search_samples.py)található.

## <a name="prerequisites"></a>Előfeltételek

* [Python](https://www.python.org/) 2. x vagy 3. x

Javasoljuk, hogy használjon egy [virtuális környezetet](https://docs.python.org/3/tutorial/venv.html) a Python-fejlesztéshez. A virtuális környezetet a [venv modul](https://pypi.python.org/pypi/virtualenv)használatával telepítheti és inicializálhatja. Telepítenie kell egy virtualenv a Python 2,7-hez. Létrehozhat egy virtuális környezetet a használatával:

```console
python -m venv mytestenv
```

A Bing News Search SDK-függőségek a következő paranccsal telepíthetők:
    
```console
python -m pip install azure-cognitiveservices-search-newssearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új Python-fájlt a kedvenc IDE vagy szerkesztőben, és importálja a következő könyvtárakat. Hozzon létre egy változót az előfizetési kulcshoz és a keresett kifejezéshez.

    ```python
    from azure.cognitiveservices.search.newssearch import NewsSearchAPI
    from msrest.authentication import CognitiveServicesCredentials
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    search_term = "Quantum Computing"
    ```

## <a name="initialize-the-client-and-send-a-request"></a>Az ügyfél inicializálása és kérelem küldése

1. Hozza létre a `CognitiveServicesCredentials` egy példányát. Ezután példányosítsa az ügyfelet:
    
    ```python
    client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))
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

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Egyoldalas webalkalmazás létrehozása](tutorial-bing-news-search-single-page-app.md)
