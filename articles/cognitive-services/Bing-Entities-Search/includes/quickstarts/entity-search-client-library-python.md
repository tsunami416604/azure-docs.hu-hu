---
title: Bing Entity Search Python ügyféloldali kódtár gyors üzembe helyezése
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: aahi
ms.openlocfilehash: 0c0a124773eab8166806312dc47ded24e1cd841f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "79136759"
---
Ezzel a rövid útmutatóval megkezdheti az entitások keresését a Pythonhoz készült Bing Entity Search ügyféloldali kódtár használatával. Habár a Bing Entity Search REST API kompatibilis a legtöbb programozási nyelvvel, az ügyféloldali kódtár egyszerű módszert kínál a szolgáltatás integrálására az alkalmazásokba. A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/entity_search_samples.py)található.

## <a name="prerequisites"></a>Előfeltételek

* Python [2. x vagy 3. x](https://www.python.org/)

* A [Pythonhoz készült BING Entity Search SDK](https://pypi.org/project/azure-cognitiveservices-search-entitysearch/)

Javasoljuk, hogy használjon Python virtuális környezetet. A venv modul használatával telepítheti és inicializálhatja a virtuális környezeteket. A virtualenv a használatával telepítheti:

```Console
python -m venv mytestenv
```

Telepítse a Bing Entity Search ügyféloldali függvénytárat a alábbiakkal:

```Console
cd mytestenv
python -m pip install azure-cognitiveservices-search-entitysearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új Python-fájlt a kedvenc IDE vagy szerkesztőben, és adja hozzá a következő importálási utasításokat. 

    ```python
    from azure.cognitiveservices.search.entitysearch import EntitySearchClient
    from azure.cognitiveservices.search.entitysearch.models import Place, ErrorResponseException
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Hozzon létre egy változót az előfizetési kulcs és a végpont számára. Hozza létre az ügyfelet egy új objektum létrehozásával a `CognitiveServicesCredentials` kulccsal.
    
    ```python
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    endpoint = "YOUR-ENDPOINT"
    client = EntitySearchclient(endpoint=endpoint, credentials=CognitiveServicesCredentials(subscription_key))
    ```

## <a name="send-a-search-request-and-receive-a-response"></a>Keresési kérelem küldése és válasz fogadása

1. Keresési kérelem küldése Bing Entity Searchhoz `client.entities.search()` és keresési lekérdezéssel. 
    
    ```python
    entity_data = client.entities.search(query="Gibralter")
    ```

2. Ha entitásokat adott vissza, Konvertáljon `entity_data.entities.value` egy listára, és nyomtassa ki az első eredményt.
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