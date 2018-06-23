---
title: Webes keresés SDK Python gyors üzembe helyezés |} Microsoft Docs
description: A telepítő a webes keresés SDK konzolalkalmazást.
titleSuffix: Azure Cognitive Services Web search SDK Python quickstart
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 02/14/2018
ms.author: v-gedod
ms.openlocfilehash: 2a5fed58be863b882b827dbed73862bc690bab1e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349482"
---
# <a name="web-search-sdk-python-quickstart"></a>Webes keresés SDK Python gyors üzembe helyezés

A Bing webes keresés SDK tartalmazza a REST API webkiszolgáló lekérdezések és elemzési eredmények funkcióit. 

A [forráskód Python Bing webes keresés SDK minták](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/web_search_samples.py) érhető el a Git központ.

## <a name="application-dependencies"></a>Alkalmazásfüggőségek
Ha az még nincs, telepítse a Python. Az SDK nem kompatibilis a Python 2.7, 3.3-as, illetve 3.4, 3.5-ös és 3.6.

A Python fejlesztési általános javaslat, hogy használja a [virtuális környezet](https://docs.python.org/3/tutorial/venv.html). Telepítse, és a virtuális környezet inicializálása a [venv modul](https://pypi.python.org/pypi/virtualenv). Python 2.7 virtualenv kell telepítenie.
```
python -m venv mytestenv
```
Bing webes keresés SDK-függőség telepítése:
```
cd mytestenv
python -m pip install azure-cognitiveservices-search-websearch
```
## <a name="web-search-client"></a>Keresési webügyfél
Első egy [kognitív szolgáltatások hozzáférési kulcs](https://azure.microsoft.com/try/cognitive-services/) alatt *keresési*. Adja hozzá a importálja, és hozzon létre egy példányát a `CognitiveServicesCredentials`:
```
from azure.cognitiveservices.search.websearch import WebSearchAPI
from azure.cognitiveservices.search.websearch.models import SafeSearch
from msrest.authentication import CognitiveServicesCredentials

subscription_key = "YOUR-SUBSCRIPTION-KEY"
```
Az ügyfél ezután példányosítható:
```
client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))
```
Keresse meg az eredményeket, és nyomtassa ki az első weblap eredmény:
```
web_data = client.web.search(query="Yosemite")
print("\r\nSearched for Query# \" Yosemite \"")

# WebPages
if web_data.web_pages.value:

    print("\r\nWebpage Results#{}".format(len(web_data.web_pages.value)))

    first_web_page = web_data.web_pages.value[0]
    print("First web page name: {} ".format(first_web_page.name))
    print("First web page URL: {} ".format(first_web_page.url))

else:
    print("Didn't see any Web data..")
```
Nyomtassa ki más eredménytípusai, beleértve a képeket, híreket és videókat:
```
# Images
if web_data.images.value:

    print("\r\nImage Results#{}".format(len(web_data.images.value)))

    first_image = web_data.images.value[0]
    print("First Image name: {} ".format(first_image.name))
    print("First Image URL: {} ".format(first_image.url))

else:
    print("Didn't see any Image..")
        
# News
if web_data.news.value:

    print("\r\nNews Results#{}".format(len(web_data.news.value)))

    first_news = web_data.news.value[0]
    print("First News name: {} ".format(first_news.name))
    print("First News URL: {} ".format(first_news.url))

else:
    print("Didn't see any News..")
            
# Videos
if web_data.videos.value:

    print("\r\nVideos Results#{}".format(len(web_data.videos.value)))

    first_video = web_data.videos.value[0]
    print("First Videos name: {} ".format(first_video.name))
    print("First Videos URL: {} ".format(first_video.url))

else:
    print("Didn't see any Videos..")

```
Keresse meg a (Budapesten legjobb éttermekben), ellenőrizze a találatok száma, és nyomtassa ki `name` és `URL` az első találatra.
```
def web_results_with_count_and_offset(subscription_key):

    client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        web_data = client.web.search(query="Best restaurants in Seattle", offset=10, count=20)
        print("\r\nSearched for Query# \" Best restaurants in Seattle \"")

        if web_data.web_pages.value:

            print("Webpage Results#{}".format(len(web_data.web_pages.value)))

            first_web_page = web_data.web_pages.value[0]
            print("First web page name: {} ".format(first_web_page.name))
            print("First web page URL: {} ".format(first_web_page.url))

        else:
            print("Didn't see any Web data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))```

```
"Xbox" keres `response_filter` rendelt `News`.  Nyomtatás hírek eredmények részleteit.
```
def web_search_with_response_filter(subscription_key):

    client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        web_data = client.web.search(query="xbox", response_filter=["News"])
        print("\r\nSearched for Query# \" xbox \" with response filters \"News\"")

        # News attribute since I filtered "News"
        if web_data.news.value:

            print("Webpage Results#{}".format(len(web_data.news.value)))

            first_web_page = web_data.news.value[0]
            print("First web page name: {} ".format(first_web_page.name))
            print("First web page URL: {} ".format(first_web_page.url))

        else:
            print("Didn't see any Web data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))

```
A Keresés a lekérdezés "Niagara esik", használatával `answerCount` és `promote` paraméterek. Az eredmények nyomtatási adatokat.
```
def web_search_with_answer_count_promote_and_safe_search(subscription_key):

    client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        web_data = client.web.search(
            query="Niagara Falls",
            answer_count=2,
            promote=["videos"],
            safe_search=SafeSearch.strict  # or directly "Strict"
        )
        print("\r\nSearched for Query# \" Niagara Falls\"")

        if web_data.web_pages.value:

            print("Webpage Results#{}".format(len(web_data.web_pages.value)))

            first_web_page = web_data.web_pages.value[0]
            print("First web page name: {} ".format(first_web_page.name))
            print("First web page URL: {} ".format(first_web_page.url))

        else:
            print("Didn't see any Web data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))

```
## <a name="next-steps"></a>További lépések

[Kognitív szolgáltatások Python SDK-minták](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)


