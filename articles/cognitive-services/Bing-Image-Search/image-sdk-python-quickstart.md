---
title: 'Gyors útmutató: Keresse meg a képeket, a Bing kép Search SDK és a Python használatával'
titleSuffix: Azure Cognitive Services
description: Ez a rövid útmutató segítségével keresse meg és -rendszerképek keresése a weben a Bing kép Search SDK és a Python használatával.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 08/28/2018
ms.author: aahi
ms.openlocfilehash: 4a24f1e4e051b627034f1d4664e94e0f47c43014
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45578293"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-and-python"></a>Gyors útmutató: Keresse meg a képeket a Bing kép Search SDK és a Python használatával

Ez a rövid útmutató segítségével győződjön meg arról, az első képkeresési Bing kép Search SDK használatával, amely egy burkoló a API-hoz, és ugyanazokat a szolgáltatásokat tartalmazza. Az egyszerű Python-alkalmazás-lemezkép keresési lekérdezést küld, elemzi a JSON-válasz és URL-címét adja vissza az első képet jeleníti meg.

Az ehhez a mintához forráskódja elérhető [a Githubon](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/image-search-quickstart.py) további hibakezelést és jegyzetek.

## <a name="prerequisites"></a>Előfeltételek 

* [Python 2.7 vagy 3.4-es](https://www.python.org/) és újabb verzióit.

* A [kép: Azure Search SDK](https://pypi.org/project/azure-cognitiveservices-search-imagesearch/) Python
    * Telepítés használatával `pip install azure-cognitiveservices-search-imagesearch`

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Hozzon létre, és az alkalmazás inicializálása

1. Hozzon létre egy új Python-szkriptet a kedvenc IDE vagy -szerkesztő és az alábbi importálásokat:

    ```python
    from azure.cognitiveservices.search.imagesearch import ImageSearchAPI
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Hozzon létre változókat az előfizetési kulcs és a keresési kifejezés.

    ```python
    subscription_key = "Enter your key here"
    search_term = "canadian rockies"
    ```

## <a name="create-the-image-search-client"></a>A kép keresési ügyfél létrehozása

3. Hozzon létre egy példányt `CognitiveServicesCredentials`, és hozza létre az ügyfél segítségével:

    ```python
    client = ImageSearchAPI(CognitiveServicesCredentials(subscription_key))
    ```
4. Keresési lekérdezés küldése a Bing Image Search API:
    ```python
    image_results = client.images.search(query=search_term)
    ```
## <a name="process-and-view-the-results"></a>Dolgozza fel, és az eredmények megtekintése

A kép eredményeket, a válasz elemzése.


A válasz tartalmazza a keresési eredmények, ha az első eredmény tárolja, és nyomtassa ki a részleteket, például a Miniatűr URL-cím, az eredeti URL-CÍMÉT, teljes számával együtt adja vissza a lemezképeket.  

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
> [Bing – Képkeresés egyoldalas alkalmazás oktatóanyag](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>Lásd még 

* [Mi az a Bing Képkeresés?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Próbálja ki az online interaktív bemutatót](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Ingyenesen a Cognitive Services hozzáférési kulcs lekérése](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)
* [Az Azure Cognitive Services SDK for Python-mintái](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)  
* [Az Azure Cognitive Services – dokumentáció](https://docs.microsoft.com/azure/cognitive-services)
* [A Bing Image Search API-referencia](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)