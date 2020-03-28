---
title: Bing entity Search Python-ügyféltár – rövid útmutató
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: aahi
ms.openlocfilehash: 0c0a124773eab8166806312dc47ded24e1cd841f
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "79136759"
---
Ezzel a rövid útmutatóval megkezdheti az entitások keresését a Bing Entity Search ügyfélkódtára pythonhoz. Bár a Bing Entity Search a legtöbb programozási nyelvvel kompatibilis REST API-val rendelkezik, az ügyfélkódtár egyszerű módot kínál a szolgáltatás alkalmazásokba való integrálására. A minta forráskódja megtalálható a [GitHubon.](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/entity_search_samples.py)

## <a name="prerequisites"></a>Előfeltételek

* Python [2.x vagy 3.x](https://www.python.org/)

* A [Bing entitás Keresés SDK python](https://pypi.org/project/azure-cognitiveservices-search-entitysearch/)

Ajánlott python virtuális környezet használata. Virtuális környezetet telepíthet és inicializálhat a venv modullal. A virtualenv a következőkkel telepíthető:

```Console
python -m venv mytestenv
```

Telepítse a Bing Entity Search ügyfélkönyvtárat a következőkkel:

```Console
cd mytestenv
python -m pip install azure-cognitiveservices-search-entitysearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új Python-fájlt a kedvenc IDE-ben vagy szerkesztőjében, és adja hozzá a következő importálási kimutatásokat. 

    ```python
    from azure.cognitiveservices.search.entitysearch import EntitySearchClient
    from azure.cognitiveservices.search.entitysearch.models import Place, ErrorResponseException
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Hozzon létre egy változót az előfizetési kulcshoz és a végponthoz. Az ügyfél létrehozása új `CognitiveServicesCredentials` objektum létrehozásával a kulccsal.
    
    ```python
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    endpoint = "YOUR-ENDPOINT"
    client = EntitySearchclient(endpoint=endpoint, credentials=CognitiveServicesCredentials(subscription_key))
    ```

## <a name="send-a-search-request-and-receive-a-response"></a>Keresési kérelem küldése és válasz fogadása

1. Keresési kérelem küldése a Bing `client.entities.search()` Entitáskeresésnek és keresési lekérdezés. 
    
    ```python
    entity_data = client.entities.search(query="Gibralter")
    ```

2. Ha entitásokat adott `entity_data.entities.value` vissza, konvertáljon listává, és nyomtassa ki az első eredményt.
    ```python
    if entity_data.entities.value:
    
        main_entities = [entity for entity in entity_data.entities.value
                         if entity.entity_presentation_info.entity_scenario == "DominantEntity"]
    
        if main_entities:
            print(main_entities[0].description)
    ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas webalkalmazás létrehozása](../../tutorial-bing-entities-search-single-page-app.md)

* [Mi a Bing Entity Search API?](../../overview.md )