---
title: 'Gyors útmutató: entitások keresése a Pythonhoz készült SDK-val – Bing Entity Search'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval megkeresheti az entitásokat a Pythonhoz készült Bing Entity Search SDK-val.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/11/2020
ms.author: aahi
ms.openlocfilehash: a6b62f7ab95f7b2720434c0cf59cce33b0adb1b4
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201237"
---
# <a name="quickstart-bing-entity-search-sdk-with-python"></a>Rövid útmutató: A Bing Entity Search SDK a Python használatával

Ezzel a rövid útmutatóval megkezdheti az entitások keresését a Pythonhoz készült Bing Entity Search SDK-val. Habár a Bing Entity Search REST API kompatibilis a legtöbb programozási nyelvvel, az SDK egyszerű módszert kínál a szolgáltatás integrálására az alkalmazásokba. A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/entity_search_samples.py)található.

## <a name="prerequisites"></a>Előfeltételek

* Python [2. x vagy 3. x](https://www.python.org/)

* A [Pythonhoz készült BING Entity Search SDK](https://pypi.org/project/azure-cognitiveservices-search-entitysearch/)

Javasoljuk, hogy használjon Python virtuális környezetet. A venv modul használatával telepítheti és inicializálhatja a virtuális környezeteket. A virtualenv a használatával telepítheti:

```Console
python -m venv mytestenv
```

Telepítse a Bing Entity Search SDK-t a használatával:

```Console
cd mytestenv
python -m pip install azure-cognitiveservices-search-entitysearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új Python-fájlt a kedvenc IDE vagy szerkesztőben, és adja hozzá a következő importálási utasításokat. 

    ```python
    from azure.cognitiveservices.search.entitysearch import EntitySearchClient
    from azure.cognitiveservices.search.entitysearch.models import Place, ErrorResponseException
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Hozzon létre egy változót az előfizetési kulcs és a végpont számára. Hozza létre az ügyfelet egy új `CognitiveServicesCredentials` objektum létrehozásával a kulccsal.
    
    ```python
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    endpoint = "YOUR-ENDPOINT"
    client = EntitySearchclient(endpoint=endpoint, credentials=CognitiveServicesCredentials(subscription_key))
    ```

## <a name="send-a-search-request-and-receive-a-response"></a>Keresési kérelem küldése és válasz fogadása

1. Keresési kérelem küldése a Bing Entity Searchnek `client.entities.search()` és keresési lekérdezéssel. 
    
    ```python
    entity_data = client.entities.search(query="Gibralter")
    ```

2. Ha entitásokat adott vissza, alakítsa át `entity_data.entities.value` egy listára, és nyomtassa ki az első eredményt.
    ```python
    if entity_data.entities.value:
    
        main_entities = [entity for entity in entity_data.entities.value
                         if entity.entity_presentation_info.entity_scenario == "DominantEntity"]
    
        if main_entities:
            print(main_entities[0].description)
    ```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Egyoldalas webalkalmazás létrehozása](../tutorial-bing-entities-search-single-page-app.md)

* [Mi a Bing Entity Search API?](../overview.md )