---
title: 'Gyors útmutató: Hívja meg a Bing Custom Search-végpont a Python SDK-val |} A Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: A Bing egyéni keresés Pythonhoz készült SDK segítségével egyéni keresési eredmények eléréséhez.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 03/05/2019
ms.author: aahi
ms.openlocfilehash: c4c5059bc57ea33357145f6b119456dc6c5bdb7b
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57571894"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-the-python-sdk"></a>Gyors útmutató: A Python SDK-val Bing Custom Search-végpont meghívása 

Ez a rövid útmutató segítségével megkezdheti a keresési eredmények kér a Bing Custom Search példány, a Python SDK használatával. Míg a Bing Custom Search REST API-val kompatibilis szinte bármelyik programozási nyelvével, a Bing Custom Search SDK biztosít egy egyszerű módja annak, hogy a szolgáltatás integrálása az alkalmazásokba. Ez a minta forráskódja találhatók [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/custom_search_samples.py) további hibakezelést és jegyzetek.

## <a name="prerequisites"></a>Előfeltételek

- Bing Custom Search-példány. Lásd: [a rövid útmutató: Az első Bing Custom Search-példány létrehozása](quick-start.md) további információt.
- Python [2.x vagy 3.x](https://www.python.org/) 
- A [Bing Custom Search SDK a Pythonhoz](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) 

## <a name="install-the-python-sdk"></a>A Python SDK telepítése

A Bing Custom Search SDK telepítése az alábbi parancsot.

```Console
python -m pip install azure-cognitiveservices-search-customsearch
```


## <a name="create-a-new-application"></a>Új alkalmazás létrehozása

Hozzon létre egy új Python-fájlt a kedvenc szerkesztőjében, vagy IDE, és adja hozzá az alábbi importálásokat.

```python
from azure.cognitiveservices.search.customsearch import CustomSearchClient
from msrest.authentication import CognitiveServicesCredentials
```

## <a name="create-a-search-client-and-send-a-request"></a>Hozzon létre egy keresési ügyfél és a egy kérelem küldése

1. Hozzon létre egy változót az előfizetési kulcs.

    ```python
    subscription_key = 'your-subscription-key'
    ```

2. Hozzon létre egy példányt `CustomSearchClient`révén egy `CognitiveServicesCredentials` objektum az előfizetési kulcsot. 

    ```python
    client = CustomSearchClient(CognitiveServicesCredentials(subscription_key))
    ```

3. A keresési kérelem küldése `client.custom_instance.search()`. A keresett kifejezés hozzáfűzése a `query` paramétert, és állítsa be `custom_config` az egyéni konfigurációs azonosító a search-példányt kíván használni. Az a Azonosítójának lekéréséhez a [Bing Custom Search portál](https://www.customsearch.ai/), kattintva a **éles** fülre.

    ```python
    web_data = client.custom_instance.search(query="xbox", custom_config="your-configuration-id")
    ```

## <a name="view-the-search-results"></a>A keresési eredmények megtekintése

Minden olyan weblap keresési eredmény, ha lekérése az elsőt, és nyomtassa ki a nevét, URL-címet, és teljes weblapok található.

```python
if web_data.web_pages.value:
    first_web_result = web_data.web_pages.value[0]
    print("Web Pages result count: {}".format(len(web_data.web_pages.value)))
    print("First Web Page name: {}".format(first_web_result.name))
    print("First Web Page url: {}".format(first_web_result.url))
else:
    print("Didn't see any web data..")
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyéni keresés webes alkalmazás készítése](./tutorials/custom-search-web-page.md)
