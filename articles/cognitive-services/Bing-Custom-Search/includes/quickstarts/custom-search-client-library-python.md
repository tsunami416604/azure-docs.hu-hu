---
title: Bing Egyéni Keresés Python-ügyféltár – rövid útmutató
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.author: aahi
ms.openlocfilehash: ec0ffdcf86e67a7126a3100c1e20b6e5c3474e35
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "78252883"
---
Ismerkedés a Bing Egyéni keresés ügyféltár pythonhoz. Az alábbi lépésekkel telepítheti a csomagot, és kipróbálhatja az alapvető feladatok példakódját. A Bing egyéni keresési API lehetővé teszi, hogy személyre szabott, hirdetésmentes keresési élményeket hozzon létre az Ön számára fontos témakörökhöz. A minta forráskódja megtalálható a [GitHubon.](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/custom_search_samples.py)

A Bing Egyéni keresés ügyféltár a Pythonhoz:
* Keresse meg a keresési eredményeket az interneten a Bing egyéni keresés példányából.

[Referenciadokumentációkönyvtár](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/customsearch?view=azure-python) | [forráskódjának](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-search-customsearch) | [csomag (PyPi)](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) | [mintái](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/)


## <a name="prerequisites"></a>Előfeltételek

- Bing egyéni keresési példánya. További információ: További információ [az első Bing egyéni keresési példány létrehozása](../../quick-start.md) című témakörben.
- Python [2.x vagy 3.x](https://www.python.org/) 
- A [Bing egyéni keresés SDK python](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) 

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="install-the-python-client-library"></a>A Python-ügyféltár telepítése

Telepítse a Bing Egyéni keresés ügyfélkönyvtárát a következő paranccsal.

```Console
python -m pip install azure-cognitiveservices-search-customsearch
```


## <a name="create-a-new-application"></a>Új alkalmazás létrehozása

Hozzon létre egy új Python-fájlt a kedvenc szerkesztőjében vagy IDE-jében, és adja hozzá a következő importálást.

```python
from azure.cognitiveservices.search.customsearch import CustomSearchClient
from msrest.authentication import CognitiveServicesCredentials
```

## <a name="create-a-search-client-and-send-a-request"></a>Keresési ügyfél létrehozása és kérelem küldése

1. Hozzon létre egy változót az előfizetési kulcshoz és a végponthoz.

    ```python
    subscription_key = 'your-subscription-key'
    endpoint = 'your-endpoint'
    ```

2. Hozzon létre `CustomSearchClient`egy `CognitiveServicesCredentials` példányt a használatával, amely objektumot használ az előfizetési kulccsal. 

    ```python
    client = CustomSearchClient(endpoint=endpoint, credentials=CognitiveServicesCredentials(subscription_key))
    ```

3. Keresési kérelem küldése `client.custom_instance.search()`a segítségével. Fűzze hozzá a `query` keresési kifejezést `custom_config` a paraméterhez, és állítsa be az egyéni konfigurációazonosítót a keresési példány használatához. Az azonosítót a [Bing egyéni keresési portálján](https://www.customsearch.ai/)szerezheti be, ha az Éles **környezet** fülre kattint.

    ```python
    web_data = client.custom_instance.search(query="xbox", custom_config="your-configuration-id")
    ```

## <a name="view-the-search-results"></a>A keresési eredmények megtekintése

Ha bármilyen weboldal keresési eredmények találhatók, kap az első, és nyomtassa ki a nevét, URL-címét, és az összes weboldal található.

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
> [Egyéni keresési webalkalmazás létrehozása](../../tutorials/custom-search-web-page.md)
