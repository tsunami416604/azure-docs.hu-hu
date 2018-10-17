---
title: 'Gyorskonfigurálás: Képek keresése a Python-hoz készült Bing Image Search SDK használatával'
titleSuffix: Azure Cognitive Services
description: Ebből a rövid útmutatóból megtudhatja, hogyan hajthatja végre első képkeresését a Bing Image Search SDK használatával, amely az API burkolójaként szolgál, és ugyanazokkal a funkciókkal rendelkezik. Ez az egyszerű Python-alkalmazás elküld egy képkeresési lekérdezést, elemzi a JSON-választ, és megjeleníti az első visszaadott kép URL-címét.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: aahi
ms.openlocfilehash: 7afe19cf0167784a5c8b3e2751ec869a2664935d
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46296617"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-and-python"></a>Gyorskonfigurálás: Képek keresése a Bing Image Search SDK és a Python használatával

Ebből a rövid útmutatóból megtudhatja, hogyan hajthatja végre első képkeresését a Bing Image Search SDK használatával, amely az API burkolójaként szolgál, és ugyanazokkal a funkciókkal rendelkezik. Ez az egyszerű Python-alkalmazás elküld egy képkeresési lekérdezést, elemzi a JSON-választ, és megjeleníti az első visszaadott kép URL-címét.

A minta forráskódja, további hibakezeléssel és megjegyzésekkel együtt, elérhető a [GitHubon](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/image-search-quickstart.py).

## <a name="prerequisites"></a>Előfeltételek

* [Python 2.7 vagy 3.4-es](https://www.python.org/) vagy ennél újabb verzió.

* [Azure Image Search SDK](https://pypi.org/project/azure-cognitiveservices-search-imagesearch/) Pythonhoz
    * Végezze el a telepítést a `pip install azure-cognitiveservices-search-imagesearch` paranccsal

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új Python-szkriptet a kedvenc IDE-környezetében vagy szerkesztőjében, az alábbi importálásokkal:

    ```python
    from azure.cognitiveservices.search.imagesearch import ImageSearchAPI
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Hozza létre az előfizetési kulcs és a keresett kifejezés változóit.

    ```python
    subscription_key = "Enter your key here"
    search_term = "canadian rockies"
    ```

## <a name="create-the-image-search-client"></a>A képkeresési ügyfél létrehozása

3. Hozza létre a `CognitiveServicesCredentials` egy példányát, és példányosítsa az ügyfelet:

    ```python
    client = ImageSearchAPI(CognitiveServicesCredentials(subscription_key))
    ```
4. Küldjön egy keresési lekérdezést a Bing Image Search API-nak:
    ```python
    image_results = client.images.search(query=search_term)
    ```
## <a name="process-and-view-the-results"></a>Dolgozza fel és tekintse meg az eredményeket

Elemezze a válaszban visszaadott képtalálatokat.


Ha a válasz tartalmaz keresési eredményeket, tárolja az első találatot, és jelenítse meg annak részleteit, például a miniatűr URL-címét és az eredeti URL-címet a visszaadott képek teljes számával együtt.  

```python
if image_results.value:
    first_image_result = image_results.value[0]
    print("Total number of images returned: {}".format(len(image_results.value)))
    print("First image thumbnail url: {}".format(first_image_result.thumbnail_url))
    print("First image content url: {}".format(first_image_result.content_url))
else:
    print("No image results returned!")
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag Bing Image Search egyoldalas alkalmazáshoz ](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>Lásd még

* [Mi a Bing Image Search?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Online interaktív bemutató kipróbálása](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Ingyenes Cognitive Services hozzáférési kulcs beszerzése](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)
* [Python-minták az Azure Cognitive Services SDK-hoz](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)  
* [Az Azure Cognitive Services dokumentációja](https://docs.microsoft.com/azure/cognitive-services)
* [A Bing Image Search API referenciaanyaga](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
