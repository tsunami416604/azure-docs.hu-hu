---
title: 'Gyors útmutató: A Python használatával a Bing Custom Search-végpont meghívása |} A Microsoft Docs'
titlesuffix: Azure Cognitive Services
description: Ez a rövid útmutató segítségével megkezdheti a keresési eredmények kér a Bing Custom Search példány Python használatával
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: quickstart
ms.date: 05/07/2018
ms.author: aahi
ms.openlocfilehash: 9534a60e66f194bf653e1bfd28d6d6f2a96ba90a
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2018
ms.locfileid: "53558719"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-python"></a>Gyors útmutató: A Python használatával a Bing Custom Search-végpont meghívása

Ez a rövid útmutató segítségével megkezdheti a keresési eredmények kér a Bing Custom Search-példányt. Bár ez az alkalmazás pythonban írt, a Bing Custom Search API olyan kompatibilis szinte bármelyik programozási nyelvével webes RESTful szolgáltatás. A minta forráskódja a [GitHubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingCustomSearchv7.py) érhető el.

## <a name="prerequisites"></a>Előfeltételek

- Bing Custom Search-példány. Lásd: [a rövid útmutató: Az első Bing Custom Search-példány létrehozása](quick-start.md) további információt.
- [Python](https://www.python.org/) 2.x vagy 3.x

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új Python-fájlt a kedvenc integrált Fejlesztőkörnyezetével vagy szerkesztőjével, és adja hozzá a következő importálási utasításokat. Hozzon létre változókat az előfizetési kulcs, egyéni konfiguráció azonosítója és a egy keresési kifejezést. 

    ```python
    import json
    import requests
    
    subscriptionKey = "YOUR-SUBSCRIPTION-KEY"
    customConfigId = "YOUR-CUSTOM-CONFIG-ID"
    searchTerm = "microsoft"
    ```

## <a name="send-and-receive-a-search-request"></a>Küldeni és fogadni egy keresési kérelmet 

1. Hozza létre a kérelem URL-CÍMÉT a keresett kifejezés hozzáfűzésével a `q=` lekérdezési paraméter, és a keresési példány egyéni konfiguráció azonosítója a `customconfig=`. a paraméterek a egy `&` karakter. 

    ```python
    url = 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 'q=' + searchTerm + '&' + 'customconfig=' + customConfigId
    ```

2. A kérés küldése a Bing Custom Search-példányt, és nyomtassa ki az eredményül kapott keresési találatok.  

    ```python
    r = requests.get(url, headers={'Ocp-Apim-Subscription-Key': subscriptionKey})
    print(r.text)
    ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyéni keresés webes alkalmazás készítése](./tutorials/custom-search-web-page.md)