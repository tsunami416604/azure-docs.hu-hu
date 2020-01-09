---
title: 'Rövid útmutató: a Bing Custom Search végpont meghívása a Python SDK használatával'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval megkezdheti a Bing Custom Search-példány keresési eredményeinek kérését a Python SDK használatával.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.openlocfilehash: ca0c1174abb80452f78766c3c4f49af73c790d3e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448680"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-the-python-sdk"></a>Rövid útmutató: a Bing Custom Search végpont meghívása a Python SDK használatával 

Ezzel a rövid útmutatóval megkezdheti a Bing Custom Search-példány keresési eredményeinek kérését a Python SDK használatával. Habár a Bing Custom Search REST API kompatibilis a legtöbb programozási nyelvvel, a Bing Custom Search SDK egyszerű módszert kínál a szolgáltatás integrálására az alkalmazásokba. A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/custom_search_samples.py) található további hibakezelés és jegyzetek használatával.

## <a name="prerequisites"></a>Előfeltételek

- Egy Bing Custom Search példány. További információért tekintse [meg a rövid útmutató: az első Bing Custom Search példány létrehozása](quick-start.md) című témakört.
- Python [2. x vagy 3. x](https://www.python.org/) 
- A [Pythonhoz készült BING Custom Search SDK](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) 

## <a name="install-the-python-sdk"></a>A Python SDK telepítése

Telepítse a Bing Custom Search SDK-t a következő paranccsal.

```Console
python -m pip install azure-cognitiveservices-search-customsearch
```


## <a name="create-a-new-application"></a>Új alkalmazás létrehozása

Hozzon létre egy új Python-fájlt a kedvenc szerkesztőjében vagy az IDE-ben, és adja hozzá a következő importálásokat.

```python
from azure.cognitiveservices.search.customsearch import CustomSearchClient
from msrest.authentication import CognitiveServicesCredentials
```

## <a name="create-a-search-client-and-send-a-request"></a>Keresési ügyfél létrehozása és kérelem küldése

1. Hozzon létre egy változót az előfizetési kulcshoz.

    ```python
    subscription_key = 'your-subscription-key'
    ```

2. Hozzon létre egy `CustomSearchClient`egy példányát egy `CognitiveServicesCredentials` objektummal az előfizetési kulccsal. 

    ```python
    client = CustomSearchClient(CognitiveServicesCredentials(subscription_key))
    ```

3. Keresési kérelem küldése `client.custom_instance.search()`sal. Fűzze hozzá a keresési kifejezést a `query` paraméterhez, és állítsa be a `custom_config`t az egyéni konfigurációs AZONOSÍTÓra a keresési példány használatához. Az azonosítót a [Bing Custom Search-portálon](https://www.customsearch.ai/)szerezheti be, az **éles** lapon kattintva.

    ```python
    web_data = client.custom_instance.search(query="xbox", custom_config="your-configuration-id")
    ```

## <a name="view-the-search-results"></a>A keresési eredmények megtekintése

Ha a rendszer bármelyik weblap keresési eredményét észlelte, töltse ki az elsőt, és nyomtassa ki a megtalált nevet, URL-címet és összes weblapot.

```python
if web_data.web_pages.value:
    first_web_result = web_data.web_pages.value[0]
    print("Web Pages result count: {}".format(len(web_data.web_pages.value)))
    print("First Web Page name: {}".format(first_web_result.name))
    print("First Web Page url: {}".format(first_web_result.url))
else:
    print("Didn't see any web data..")
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Egyéni keresési Webalkalmazás létrehozása](./tutorials/custom-search-web-page.md)
