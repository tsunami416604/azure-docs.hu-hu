---
title: 'Rövid útmutató: Bing News Search SDK, Python'
titleSuffix: Azure Cognitive Services
description: A Bing News Search SDK-konzolalkalmazás beállítása.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: quickstart
ms.date: 02/14/2018
ms.author: v-gedod
ms.openlocfilehash: 6e029b7e830b6ec157d03d17dc24e65282a9c742
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52316803"
---
# <a name="quickstart-bing-news-search-sdk-with-python"></a>Rövid útmutató: A Bing News Search SDK és a Python használata

A News Search SDK a REST API funkcióit biztosítja az internetes lekérdezésekhez és az eredmények elemzéséhez. 

A [Python Bing News Search SDK-minták forráskódja](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/news_search_samples.py) elérhető a GitHubon.

## <a name="application-dependencies"></a>Alkalmazásfüggőségek
A [Cognitive Services hozzáférési kulcsát](https://azure.microsoft.com/try/cognitive-services/) a **Keresés** területen kérheti le.  Lásd még: [a Cognitive Services díjszabás – keresési Bing-API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

Ha még nincs telepítve a Python, telepítse. Az SDK a Python 2.7-es, 3.3-as, 3.4-es, 3.5-ös és 3.6-os verziójával kompatibilis.

A Pythonnal való fejlesztéskor általánosságban javasolt egy [virtuális környezet](https://docs.python.org/3/tutorial/venv.html) használata. Telepítse és inicializálja a virtuális környezetet az új [venv modullal](https://pypi.python.org/pypi/virtualenv). Telepítse a Python 2.7-hez készült virtualenv modult.
```
python -m venv mytestenv
```
Telepítse a Bing News Search SDK függőségeit:
```
cd mytestenv
python -m pip install azure-cognitiveservices-search-newssearch
```
## <a name="news-search-client"></a>News Search-ügyfél
A [Cognitive Services hozzáférési kulcsát](https://azure.microsoft.com/try/cognitive-services/) a *Keresés* területen kérheti le. Adja hozzá az importálásokat:
```
from azure.cognitiveservices.search.newssearch import NewsSearchAPI
from msrest.authentication import CognitiveServicesCredentials

subscription_key = "YOUR-SUBSCRIPTION-KEY"
```
Hozza létre a `CognitiveServicesCredentials` egy példányát. Ezután példányosítsa az ügyfelet:
```
client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))
```
Keressen rá az eredményekre, és jelenítse meg az eredményként kapott első weblapot:
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
Szűrőkkel keresse meg a legfrissebb híreket az „Artificial Intelligence” (mesterséges intelligencia) témában a `freshness` és a `sortBy` paraméterrel. Ellenőrizze a találatok számát, és jelenítse meg az első eredményül kapott hír `totalEstimatedMatches`, `name`, `url`, `description`, `published time` és `name of provider` tulajdonságát.
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
Keressen a mozis és televíziós kategóriák híreiben biztonságos kereséssel. Ellenőrizze a találatok számát, és jelenítse meg az első eredményül kapott hír `category`, `name`, `url`, `description`, `published time` és `name of provider` tulajdonságát.
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
Keressen népszerű témákkal foglalkozó hírek között a Bing keresővel.  Ellenőrizze a találatok számát, és jelenítse meg az első eredményül kapott hír `name`, `text of query`, `webSearchUrl`, `newsSearchUrl`, `image Url` és tulajdonságát.
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

[Cognitive Services Python SDK-minták](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)


