---
title: 'Gyors útmutató: Hajtsa végre a hírkeresési – a Bing News Search SDK a Pythonhoz'
titleSuffix: Azure Cognitive Services
description: Ez a rövid útmutató segítségével hírkeresés a Bing News Search SDK a Pythonhoz készült használatával, és a válasz feldolgozása.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 01/10/2019
ms.author: v-gedod
ms.custom: seodec2018
ms.openlocfilehash: 4b4706baa5148bba955a980b6184e0747f023066
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55875093"
---
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-python"></a>Gyors útmutató: Hajtsa végre a hírkeresési Bing News Search SDK-val a Pythonhoz

Ez a rövid útmutató segítségével a Pythonhoz hírkeresés a Bing News Search SDK-val keresés megkezdéséhez. Míg a Bing News Search REST API-val kompatibilis szinte bármelyik programozási nyelvével, az SDK biztosít egy egyszerű módja annak, hogy a szolgáltatás integrálása az alkalmazásokba. Ez a minta forráskódja találhatók [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/news_search_samples.py).

## <a name="prerequisites"></a>Előfeltételek

* [Python](https://www.python.org/) 2.x vagy 3.x

Javasoljuk, hogy használjon egy [virtuális környezet](https://docs.python.org/3/tutorial/venv.html) a python fejlesztési. Telepítheti, és a virtuális környezet inicializálása a [venv modul](https://pypi.python.org/pypi/virtualenv). Python 2.7 telepítenie kell egy virtualenv. A virtuális környezetet hozhat létre:

```console
python -m venv mytestenv
```

A Bing News Search SDK-függőségek a következő paranccsal telepítheti:
    
```console
python -m pip install azure-cognitiveservices-search-newssearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új Python-fájlt a kedvenc integrált Fejlesztőkörnyezetével vagy szerkesztőjével, és importálja a következő kódtárakra. Hozzon létre egy változót az előfizetési kulcs és a keresési kifejezést.

    ```python
    from azure.cognitiveservices.search.newssearch import NewsSearchAPI
    from msrest.authentication import CognitiveServicesCredentials
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    search_term = "Quantum Computing"
    ```

## <a name="initialize-the-client-and-send-a-request"></a>Az ügyfél inicializálása, és a egy kérelem küldése

1. Hozza létre a `CognitiveServicesCredentials` egy példányát. Ezután példányosítsa az ügyfelet:
    
    ```python
    client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))
    ```

2. Egy keresési lekérdezést küld a News Search API, a válasz tárolása.

    ```python
    news_result = client.news.search(query=search_term, market="en-us", count=10)
    ```

## <a name="parse-the-response"></a>A válasz elemzéséhez

Ha minden olyan keresési eredményeket talál, nyomtassa ki az első weblap eredmény:

```python
if news_result.value:
    first_news_result = news_result.value[0]
    print("Total estimated matches value: {}".format(news_result.total_estimated_matches))
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
[Egyoldalas webalkalmazás létrehozása](tutorial-bing-news-search-single-page-app.md)
