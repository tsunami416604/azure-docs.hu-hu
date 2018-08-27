---
title: 'Gyors útmutató: A Bing Web Search SDK Pythonhoz készült használja.'
description: A telepítő a Web Search SDK konzolalkalmazást.
titleSuffix: Azure Cognitive Services Web search SDK Python quickstart
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 08/16/2018
ms.author: v-gedod, erhopf
ms.openlocfilehash: faf43d84724cdbf799219c120f87dfc333c5026f
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42888526"
---
# <a name="quickstart-use-the-bing-web-search-sdk-for-python"></a>Gyors útmutató: A Bing Web Search SDK Pythonhoz készült használja.

A Bing Web Search SDK tartalmazza a REST API, webes lekérdezések és az elemzési eredmények funkcióit.

A [forráskódját Python a Bing Web Search SDK-minták](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/web_search_samples.py) elérhető a Githubon.

## <a name="application-dependencies"></a>Alkalmazásfüggőségek
Ha ez már nincs, telepítse a Pythont. Az SDK-t, a Python 2.7-es, 3.3-as, 3.4-es, 3.5-ös és 3.6-os verziója kompatibilis.

A Python fejlesztési általános javaslat, hogy használja a [virtuális környezet](https://docs.python.org/3/tutorial/venv.html).
Telepítse, és a virtuális környezet inicializálása a [venv modul](https://pypi.python.org/pypi/virtualenv). Python 2.7-es virtualenv gépre kell telepítenie.
```
python -m venv mytestenv
```
A Bing webes keresési SDK-függőség telepítése:
```
cd mytestenv
python -m pip install azure-cognitiveservices-search-websearch
```
## <a name="web-search-client"></a>Webes keresés ügyfél
Get- [Cognitive Services előfizetési kulcs](https://azure.microsoft.com/try/cognitive-services/) alatt *keresési*.
Adjon hozzá importálja, és hozzon létre egy példányt a `CognitiveServicesCredentials`:
```
from azure.cognitiveservices.search.websearch import WebSearchAPI
from azure.cognitiveservices.search.websearch.models import SafeSearch
from msrest.authentication import CognitiveServicesCredentials

subscription_key = "YOUR-SUBSCRIPTION-KEY"
```
Ezután hozza létre az ügyfél:
```
client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))
```
Keresse meg az eredményeket, és nyomtassa ki az első weblap eredményt:
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
Nyomtatási más eredménytípusú lekérdezéseknél, beleértve a képeket, híreket és videókat:
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
Keresse meg a (Budapesten található ajánlott éttermek), ellenőrizze a eredmények számát, és nyomtassa ki az `name` és `URL` az első eredmény.
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
A "Xbox" Keresés `response_filter` rendelt `News`.  Nyomtatási hírkeresési eredmények részletei.
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
Lekérdezés "Niagara esik", a keresés használatával `answerCount` és `promote` paramétereket. Nyomtassa ki a részletes eredmények.
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

[Cognitive Services Python SDK-minták](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)
