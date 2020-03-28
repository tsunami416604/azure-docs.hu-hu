---
title: 'Rövid útmutató: Bing Visual Search SDK, Python'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval a Python SDK használatával megkezdheti a rendszerelemzéseket a Bing Visual Search szolgáltatásból.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: 2a5fb6d72ab2259b2c11d1d71e93aa635da36946
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75446553"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-sdk-for-python"></a>Rövid útmutató: Képelemzési adatok beszereznie a Bing Visual Search SDK python-hoz használatával

Ezzel a rövid útmutatóval a Python SDK használatával megkezdheti a rendszerelemzéseket a Bing Visual Search szolgáltatásból. Bár a Bing Visual Search a legtöbb programozási nyelvvel kompatibilis REST API-val rendelkezik, az SDK segítségével egyszerűen integrálhatja a szolgáltatást az alkalmazásokba. A minta forráskódja megtalálható a [GitHubon](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/visual_search_samples.py) 

## <a name="prerequisites"></a>Előfeltételek

* [Piton](https://www.python.org/) 2.x vagy 3.x
* Javasoljuk, hogy használja a [virtuális környezetben](https://docs.python.org/3/tutorial/venv.html). Telepítse és inicializálja a virtuális környezetet az új [venv modullal](https://pypi.python.org/pypi/virtualenv).
* A Bing Visual Search SDK python. A következő parancsokkal telepítheti:
    1. `cd mytestenv`
    2. `python -m pip install azure-cognitiveservices-search-visualsearch`

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új Python-fájlt a kedvenc IDE-ben vagy szerkesztőjében, és adja hozzá a következő importálási kimutatásokat. 

    ```python
    import http.client, urllib.parse
    import json
    import os.path
    from azure.cognitiveservices.search.visualsearch import VisualSearchClient
    from azure.cognitiveservices.search.visualsearch.models import (
        VisualSearchRequest,
        CropArea,
        ImageInfo,
        Filters,
        KnowledgeRequest,
    )
    from msrest.authentication import CognitiveServicesCredentials
    ```
2. Hozzon létre változókat az előfizetési kulcshoz, az egyéni konfigurációs azonosítóhoz és a feltölteni kívánt lemezképhez. 
    
    ```python
    subscription_key = 'YOUR-VISUAL-SEARCH-ACCESS-KEY'
    PATH = 'C:\\Users\\USER\\azure-cognitive-samples\\mytestenv\\TestImages\\'
    image_path = os.path.join(PATH, "image.jpg")
    
    ```

3. Az ügyfél példányosítása

    ```python
    client = VisualSearchClient(endpoint="https://api.cognitive.microsoft.com", credentials=CognitiveServicesCredentials(subscription_key))
    ```

## <a name="send-the-search-request"></a>A keresési kérelem elküldése

1. Nyissa meg a képfájlt, `VisualSearchRequest()`szerializálja `knowledge_request` a `visual_search()`t, és adja át a paraméterként.

    ```python
    with open(image_path, "rb") as image_fd:
        # You need to pass the serialized form of the model
        knowledge_request = json.dumps(VisualSearchRequest().serialize())
    
        print("\r\nSearch visual search request with binary of dog image")
        result = client.images.visual_search(image=image_fd, knowledge_request=knowledge_request)
    ```

2. Ha bármilyen eredményt adott vissza, nyomtassa ki őket, a címkéket és az első címkében lévő műveleteket.

    ```python
    if not result:
            print("No visual search result data.")
    
            # Visual Search results
        if result.image.image_insights_token:
            print("Uploaded image insights token: {}".format(result.image.image_insights_token))
        else:
            print("Couldn't find image insights token!")
    
        # List of tags
        if result.tags:
            first_tag = result.tags[0]
            print("Visual search tag count: {}".format(len(result.tags)))
    
            # List of actions in first tag
            if first_tag.actions:
                first_tag_action = first_tag.actions[0]
                print("First tag action count: {}".format(len(first_tag.actions)))
                print("First tag action type: {}".format(first_tag_action.action_type))
            else:
                print("Couldn't find tag actions!")
        else:
            print("Couldn't find image tags!")
    ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas webalkalmazás készítése](tutorial-bing-visual-search-single-page-app.md)
