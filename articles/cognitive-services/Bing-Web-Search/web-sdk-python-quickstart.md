---
title: 'Gyors útmutató: A Bing Web Search SDK Pythonhoz készült használata'
titleSuffix: Azure Cognitive Services
description: A Bing Web Search SDK megkönnyíti a Bing Web Search integrálását a Python-alkalmazásába. Ebben a rövid útmutatóban elsajátíthatja a kérésküldésnek, a JSON-válaszok fogadásának, valamint az eredmények szűrésének és elemzésének módját.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 03/12/2019
ms.author: aahi
ms.openlocfilehash: 273922c8cf48c24ff3b1b55fa44b36b69e061057
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57863899"
---
# <a name="quickstart-use-the-bing-web-search-sdk-for-python"></a>Gyors útmutató: A Bing Web Search SDK Pythonhoz készült használata

A Bing Web Search SDK megkönnyíti a Bing Web Search integrálását a Python-alkalmazásába. Ebben a rövid útmutatóban elsajátíthatja a kérésküldésnek, a JSON-válaszok fogadásának, valamint az eredmények szűrésének és elemzésének módját.

Szeretné most rögtön megtekinteni a kódot? A [Pythonhoz készült Bing Web Search SDK-minták](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples) megtekinthetők a GitHubon.

[!INCLUDE [bing-web-search-quickstart-signup](../../../includes/bing-web-search-quickstart-signup.md)]

Lásd még: [a Cognitive Services díjszabás – keresési Bing-API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="prerequisites"></a>Előfeltételek
A Bing Web Search SDK a Python 2.7-es, 3.3-as, 3.4-es, 3.5-ös, és 3.6-os verziójával kompatibilis. Jelen rövid útmutatóhoz ajánlott egy virtuális környezet használata.

* Python 2.7, 3.3, 3.4, 3.5 vagy 3.6
* [virtualenv](https://docs.python.org/3/tutorial/venv.html) a Python 2.7 esetében
* [venv](https://pypi.python.org/pypi/virtualenv) a Python 3.x esetében

## <a name="create-and-configure-your-virtual-environment"></a>A virtuális környezet létrehozása és konfigurálása

A virtuális környezet létrehozására és konfigurálására vonatkozó útmutatás eltérő a Python 2.x és a Python 3.x használata esetében. Az alábbi lépéseket követve hozza létre és inicializálja a virtuális környezetet.

### <a name="python-2x"></a>Python 2.x

Virtuális környezet létrehozása a `virtualenv` segítségével Python 2.7 használata esetén:

```console
virtualenv mytestenv
```

Aktiválja a környezetet:

```console
cd mytestenv
source bin/activate
```

Telepítse a Bing Web Search SDK függőségeit:

```console
python -m pip install azure-cognitiveservices-search-websearch
```

### <a name="python-3x"></a>Python 3.x

Virtuális környezet létrehozása a `venv` segítségével Python 3.x használata esetén:

```console
python -m venv mytestenv
```

Telepítse a Bing Web Search SDK függőségeit:

```console
cd mytestenv
python -m pip install azure-cognitiveservices-search-websearch
```

## <a name="create-a-client-and-print-your-first-results"></a>Ügyfél létrehozása és az első eredmények megjelenítése

Most, hogy beállítottuk a virtuális környezetet, és telepítettük a függőségeket, ideje létrehozni az ügyfelet. Az ügyfél fogja kezelni a Bing Web Search API-ra küldött kéréseket és az onnan érkező válaszokat.

Ha a válasz tartalmaz weblapokat, képeket, cikkeket vagy videókat, a rendszer mindegyikből megjeleníti az elsőt.

1. Hozzon létre egy új Python-projektet a kedvenc IDE-környezetében vagy szerkesztőjében.

1. Másolja ezt a mintakódot a projektbe:  

    ```python
    # Import required modules.
    from azure.cognitiveservices.search.websearch import WebSearchAPI
    from azure.cognitiveservices.search.websearch.models import SafeSearch
    from msrest.authentication import CognitiveServicesCredentials

    # Replace with your subscription key.
    subscription_key = "YOUR_SUBSCRIPTION_KEY"

    # Instantiate the client.
    client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

    # Make a request. Replace Yosemite if you'd like.
    web_data = client.web.search(query="Yosemite")
    print("\r\nSearched for Query# \" Yosemite \"")

    '''
    Web pages
    If the search response contains web pages, the first result's name and url
    are printed.
    '''
    if hasattr(web_data.web_pages, 'value'):

        print("\r\nWebpage Results#{}".format(len(web_data.web_pages.value)))

        first_web_page = web_data.web_pages.value[0]
        print("First web page name: {} ".format(first_web_page.name))
        print("First web page URL: {} ".format(first_web_page.url))

    else:
        print("Didn't find any web pages...")

    '''
    Images
    If the search response contains images, the first result's name and url
    are printed.
    '''
    if hasattr(web_data.images, 'value'):

        print("\r\nImage Results#{}".format(len(web_data.images.value)))

        first_image = web_data.images.value[0]
        print("First Image name: {} ".format(first_image.name))
        print("First Image URL: {} ".format(first_image.url))

    else:
        print("Didn't find any images...")

    '''
    News
    If the search response contains news, the first result's name and url
    are printed.
    '''
    if hasattr(web_data.news, 'value'):

        print("\r\nNews Results#{}".format(len(web_data.news.value)))

        first_news = web_data.news.value[0]
        print("First News name: {} ".format(first_news.name))
        print("First News URL: {} ".format(first_news.url))

    else:
        print("Didn't find any news...")

    '''
    If the search response contains videos, the first result's name and url
    are printed.
    '''
    if hasattr(web_data.videos, 'value'):

        print("\r\nVideos Results#{}".format(len(web_data.videos.value)))

        first_video = web_data.videos.value[0]
        print("First Videos name: {} ".format(first_video.name))
        print("First Videos URL: {} ".format(first_video.url))

    else:
        print("Didn't find any videos...")
    ```

1. Cserélje le a `subscription_key` értékét egy érvényes előfizetői azonosítóra.

1. Futtassa a programot. Például: `python your_program.py`.

## <a name="define-functions-and-filter-results"></a>Függvények definiálása és az eredmények szűrése

Most, hogy létrehozta az első hívás a Bing Web Search API, tekintsük át néhány funkciók. A következő szakaszok kiemelnek SDK funkciókat lekérdezések pontosítását és szűrés eredményei. A Python program, az előző szakaszban létrehozott minden egyes függvény is hozzáadhatók.

### <a name="limit-the-number-of-results-returned-by-bing"></a>A Bing által visszaadott eredmények számának korlátozása

Ebben a példában a `count` és az `offset` paramétert használjuk az SDK [`search` metódusa](https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python) által visszaadott eredmények számának korlátozására. Az első eredményhez tartozó `name` és `url` értékét a rendszer megjeleníti.

1. Adja hozzá ezt a kódot a Python-projekthez:

   ```python
    # Declare the function.
    def web_results_with_count_and_offset(subscription_key):
        client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

        try:
            '''
            Set the query, offset, and count using the SDK's search method. See:
            https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python.
            '''
            web_data = client.web.search(query="Best restaurants in Seattle", offset=10, count=20)
            print("\r\nSearching for \"Best restaurants in Seattle\"")

            if web_data.web_pages.value:
                '''
                If web pages are available, print the # of responses, and the first and second
                web pages returned.
                '''
                print("Webpage Results#{}".format(len(web_data.web_pages.value)))

                first_web_page = web_data.web_pages.value[0]
                print("First web page name: {} ".format(first_web_page.name))
                print("First web page URL: {} ".format(first_web_page.url))

            else:
                print("Didn't find any web pages...")

        except Exception as err:
            print("Encountered exception. {}".format(err))
    ```

1. Futtassa a programot.

### <a name="filter-for-news-and-freshness"></a>Hírek és frissesség szűrése

Ez a példa a `response_filter` és a `freshness` paraméter segítségével szűri az SDK [`search` metódusa](/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations) által visszaadott keresési eredményeket. A visszaadott keresési eredmények a sajtóhírekre, azon belül pedig a Bing által az elmúlt 24 órában észlelt oldalakra van korlátozva. Az első eredményhez tartozó `name` és `url` értékét a rendszer megjeleníti.

1. Adja hozzá ezt a kódot a Python-projekthez:

    ```python
    # Declare the function.
    def web_search_with_response_filter(subscription_key):
        client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))
        try:
            '''
            Set the query, response_filter, and freshness using the SDK's search method. See:
            https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python.
            '''
            web_data = client.web.search(query="xbox",
                response_filter=["News"],
                freshness="Day")
            print("\r\nSearching for \"xbox\" with the response filter set to \"News\" and freshness filter set to \"Day\".")

            '''
            If news articles are available, print the # of responses, and the first and second
            articles returned.
            '''
            if web_data.news.value:

                print("# of news results: {}".format(len(web_data.news.value)))

                first_web_page = web_data.news.value[0]
                print("First article name: {} ".format(first_web_page.name))
                print("First article URL: {} ".format(first_web_page.url))

                print("")

                second_web_page = web_data.news.value[1]
                print("\nSecond article name: {} ".format(second_web_page.name))
                print("Second article URL: {} ".format(second_web_page.url))

            else:
                print("Didn't find any news articles...")

        except Exception as err:
            print("Encountered exception. {}".format(err))

    # Call the function.
    web_search_with_response_filter(subscription_key)
    ```

1. Futtassa a programot.

### <a name="use-safe-search-answer-count-and-the-promote-filter"></a>A biztonságos keresés, a válaszszám és az előléptetés szűrő használata

Ez a példa a `answer_count`, a `promote` és a `safe_search` paraméter segítségével szűri az SDK [`search` metódusa](https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python) által visszaadott keresési eredményeket. A kód megjeleníti az első eredmény `name` és `url` értékét.

1. Adja hozzá ezt a kódot a Python-projekthez:

    ```python
    # Declare the function.
    def web_search_with_answer_count_promote_and_safe_search(subscription_key):

        client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

        try:
            '''
            Set the query, answer_count, promote, and safe_search parameters using the SDK's search method. See:
            https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python.
            '''
            web_data = client.web.search(
                query="Niagara Falls",
                answer_count=2,
                promote=["videos"],
                safe_search=SafeSearch.strict  # or directly "Strict"
            )
            print("\r\nSearching for \"Niagara Falls\"")

            '''
            If results are available, print the # of responses, and the first result returned.
            '''
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

1. Futtassa a programot.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett ezzel a projekttel, ne felejtse el eltávolítani az előfizetői azonosítót a program kódjából és inaktiválni a virtuális környezetet.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Cognitive Services Python SDK-minták](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

## <a name="see-also"></a>Lásd még

* [Azure Python SDK-referencia](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/websearch)
