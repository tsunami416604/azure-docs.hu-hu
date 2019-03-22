---
title: 'Gyors útmutató: Képkeresés – Bing kép Search SDK a Pythonhoz'
titleSuffix: Azure Cognitive Services
description: Ebből a rövid útmutatóból megtudhatja, hogyan hajthatja végre első képkeresését a Bing Image Search SDK használatával, amely az API burkolójaként szolgál, és ugyanazokkal a funkciókkal rendelkezik. Ez az egyszerű Python-alkalmazás elküld egy képkeresési lekérdezést, elemzi a JSON-választ, és megjeleníti az első visszaadott kép URL-címét.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: cabf29423123d988b08e002077b4782529e96aa0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58095636"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-for-python"></a>Gyors útmutató: Keresse meg a lemezképek lemezkép Bing Search SDK-val a Pythonhoz

Ebből a rövid útmutatóból megtudhatja, hogyan hajthatja végre első képkeresését a Bing Image Search SDK használatával, amely az API burkolójaként szolgál, és ugyanazokkal a funkciókkal rendelkezik. Ez az egyszerű Python-alkalmazás elküld egy képkeresési lekérdezést, elemzi a JSON-választ, és megjeleníti az első visszaadott kép URL-címét.

A minta forráskódja további hibakezeléssel és megjegyzésekkel együtt elérhető a [GitHubon](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/image-search-quickstart.py).

## <a name="prerequisites"></a>Előfeltételek
A [Cognitive Services hozzáférési kulcsát](https://azure.microsoft.com/try/cognitive-services/) a **Keresés** területen kérheti le.  Lásd még: [a Cognitive Services díjszabás – keresési Bing-API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

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

1. Hozza létre a `CognitiveServicesCredentials` egy példányát, és példányosítsa az ügyfelet:

    ```python
    client = ImageSearchAPI(CognitiveServicesCredentials(subscription_key))
    ```
1. Küldjön egy keresési lekérdezést a Bing Image Search API-nak:
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
> [Egyoldalas alkalmazás-oktatóanyag a Bing Image Search használatához](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>Lásd még

* [Mi a Bing Image Search?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Online interaktív bemutató kipróbálása](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Ingyenes Cognitive Services hozzáférési kulcs beszerzése](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)
* [Python-minták az Azure Cognitive Services SDK-hoz](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)  
* [Az Azure Cognitive Services dokumentációja](https://docs.microsoft.com/azure/cognitive-services)
* [Bing Image Search API – referencia](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
