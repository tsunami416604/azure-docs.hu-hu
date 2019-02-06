---
title: 'Gyors útmutató: Bing Entity Search SDK, Python'
titlesuffix: Azure Cognitive Services
description: A Bing Entity Search SDK konzolalkalmazás beállítása.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: v-gedod
ms.openlocfilehash: 50620eba35be136e38d5b1f8c3083f9ddf189d9e
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55757459"
---
# <a name="quickstart-bing-entity-search-sdk-with-python"></a>Gyors útmutató: A Bing Entity Search, a Python SDK

Ez a rövid útmutató segítségével kezdeni a keresést a Bing Entity Search SDK rendelkező entitások esetében a Pythonhoz készült. Míg a Bing Entity Search REST API-val kompatibilis szinte bármelyik programozási nyelvével, az SDK biztosít egy egyszerű módja annak, hogy a szolgáltatás integrálása az alkalmazásokba. Ez a minta forráskódja találhatók [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/entity_search_samples.py).

## <a name="prerequisites"></a>Előfeltételek

* Python [2.x vagy 3.x](https://www.python.org/)

* A [a Bing Entity Search SDK a Pythonhoz](https://pypi.org/project/azure-cognitiveservices-search-entitysearch/)

Javasoljuk, hogy a python virtuális környezetet használja. Telepítse, és a egy virtuális környezethez, a venv modul inicializálása. A Python 2.7-es virtualenv is telepítheti:

```Console
python -m venv mytestenv
```

A Bing Entity keresse az SDK telepítése:

```Console
cd mytestenv
python -m pip install azure-cognitiveservices-search-entitysearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új Python-fájlt a kedvenc integrált Fejlesztőkörnyezetével vagy szerkesztőjével, és adja hozzá a következő importálási utasításokat. 

    ```python
    from azure.cognitiveservices.search.entitysearch import EntitySearchAPI
    from azure.cognitiveservices.search.entitysearch.models import Place, ErrorResponseException
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Hozzon létre egy változót az előfizetési kulcs, és hozza létre az ügyfél hozzon létre egy új `CognitiveServicesCredentials` objektum vele.
    
    ```python
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    client = EntitySearchAPI(CognitiveServicesCredentials(subscription_key))
    ```

## <a name="send-a-search-request-and-receive-a-response"></a>A keresési kéréseket küldeni és fogadni a választ

1. Egy keresési kérelmet küld a Bing Entity Search `client.entities.search()` és a egy keresési lekérdezést. 
    
    ```python
    entity_data = client.entities.search(query="Gibralter")
    ```

2. Entitást adott vissza, ha konvertálni `entity_data.entities.value` és a egy lista nyomtatás az első eredményt.
    ```python
    if entity_data.entities.value:
    
        main_entities = [entity for entity in entity_data.entities.value
                         if entity.entity_presentation_info.entity_scenario == "DominantEntity"]
    
        if main_entities:
            print(main_entities[0].description)
    ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas webalkalmazás létrehozása](../tutorial-bing-entities-search-single-page-app.md)

* [Mi az a Bing Entity Search API?](../overview.md )