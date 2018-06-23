---
title: Hírek keresési SDK Python gyors üzembe helyezés |} Microsoft Docs
description: A telepítő hírek keresési SDK konzol alkalmazáshoz.
titleSuffix: Azure News Search SDK Python quickstart
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 02/14/2018
ms.author: v-gedod
ms.openlocfilehash: 6d212d1477ecf583a038e33e72aab3d60f6aa050
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349466"
---
# <a name="news-search-sdk-python-quickstart"></a>Hírek keresési SDK Python gyors üzembe helyezés

A hírek keresési SDK a REST API webkiszolgáló lekérdezések és elemzési eredmények funkcióit tartalmazza. 

A [forráskód Python Bing hírek keresési SDK minták](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/news_search_samples.py) érhető el a Git központ.

## <a name="application-dependencies"></a>Alkalmazásfüggőségek
Ha az még nincs, telepítse a Python. Az SDK nem kompatibilis a Python 2.7, 3.3-as, 3.4, 3.5-ös és 3.6.

A Python fejlesztési általános javaslat, hogy használja a [virtuális környezet](https://docs.python.org/3/tutorial/venv.html). Telepítse, és a virtuális környezet inicializálása a [venv modul](https://pypi.python.org/pypi/virtualenv). Python 2.7 virtualenv kell telepítenie.
```
python -m venv mytestenv
```
Bing hírek keresési SDK-függőség telepítése:
```
cd mytestenv
python -m pip install azure-cognitiveservices-search-newssearch
```
## <a name="news-search-client"></a>Hírek keresési ügyfél
Első egy [kognitív szolgáltatások hozzáférési kulcs](https://azure.microsoft.com/try/cognitive-services/) alatt *keresési*. Adja hozzá a importálásokat:
```
from azure.cognitiveservices.search.newssearch import NewsSearchAPI
from msrest.authentication import CognitiveServicesCredentials

subscription_key = "YOUR-SUBSCRIPTION-KEY"
```
Hozzon létre egy példánya `CognitiveServicesCredentials`. Az ügyfél hozható létre:
```
client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))
```
Keresse meg az eredményeket, és nyomtassa ki az első képernyőn látható weblapon eredmény:
```
news_result = client.news.search(query="Quantum Computing", market="en-us", count=10)
print("Search news for query \"Quantum Computing\" with market and count")

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
A szűrőket a "Mesterséges Eszközintelligencia" kapcsolatos legfrissebb híreket tartalmazó keresési `freshness` és `sortBy` paraméterek. Ellenőrizze az eredmények számát, és nyomtassa ki `totalEstimatedMatches`, `name`, `url`, `description`, `published time`, és `name of provider` az első hírek elem eredmény.
```
def news_search_with_filtering(subscription_key):

    client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        news_result = client.news.search(
            query="Artificial Intelligence",
            market="en-us",
            freshness="Week",
            sort_by="Date"
        )
        print("\r\nSearch most recent news for query \"Artificial Intelligence\" with freshness and sortBy")

        if news_result.value:
            first_news_result = news_result.value[0]
            print("News result count: {}".format(len(news_result.value)))
            print("First news name: {}".format(first_news_result.name))
            print("First news url: {}".format(first_news_result.url))
            print("First news description: {}".format(first_news_result.description))
            print("First published time: {}".format(first_news_result.date_published))
            print("First news provider: {}".format(first_news_result.provider[0].name))
        else:
            print("Didn't see any news result data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))

```
Keresés a kategória hírek movie és TV Szórakozás biztonságos keresés. Ellenőrizze az eredmények számát, és nyomtassa ki `category`, `name`, `url`, `description`, `published time`, és `name of provider` az első hírek elem eredmény.
```
def news_category(subscription_key):

    client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        news_result = client.news.category(
            category="Entertainment_MovieAndTV",
            market="en-us",
            safe_search="strict"
        )
        print("\r\nSearch category news for movie and TV entertainment with safe search")

        if news_result.value:
            first_news_result = news_result.value[0]
            print("News result count: {}".format(len(news_result.value)))
            print("First news category: {}".format(first_news_result.category))
            print("First news name: {}".format(first_news_result.name))
            print("First news url: {}".format(first_news_result.url))
            print("First news description: {}".format(first_news_result.description))
            print("First published time: {}".format(first_news_result.date_published))
            print("First news provider: {}".format(first_news_result.provider[0].name))
        else:
            print("Didn't see any news result data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))


```
Bing hírek trendekkel kapcsolatos témakörök keresni.  Ellenőrizze az eredmények számát, és nyomtassa ki `name`, `text of query`, `webSearchUrl`, `newsSearchUrl`, és `image Url` az első hírek eredmény.
```
def news_trending(subscription_key):

    client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        trending_topics = client.news.trending(market="en-us")
        print("\r\nSearch news trending topics in Bing")

        if trending_topics.value:
            first_topic = trending_topics.value[0]
            print("News result count: {}".format(len(trending_topics.value)))
            print("First topic name: {}".format(first_topic.name))
            print("First topic query: {}".format(first_topic.query.text))
            print("First topic image url: {}".format(first_topic.image.url))
            print("First topic webSearchUrl: {}".format(first_topic.web_search_url))
            print("First topic newsSearchUrl: {}".format(first_topic.news_search_url))
        else:
            print("Didn't see any topics result data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))

```

## <a name="next-steps"></a>További lépések

[Kognitív szolgáltatások Python SDK-minták](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)


