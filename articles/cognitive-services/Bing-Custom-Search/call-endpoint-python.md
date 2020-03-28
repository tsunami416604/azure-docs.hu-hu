---
title: 'Rövid útmutató: Hívja meg a Bing egyéni keresési végpontját python használatával | Microsoft dokumentumok'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval elkezdheti a keresési eredmények et a Bing egyéni keresési példányából a Python használatával
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: a601b309d18e489f6b631cb26e5f3e13ef790b42
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80238833"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-python"></a>Rövid útmutató: A Bing egyéni keresési végpontjának hívása python használatával

Ezzel a rövid útmutatóval megkezdheti a keresési eredmények keresését a Bing egyéni keresési példányából. Bár ez az alkalmazás Pythonnyelven íródott, a Bing Egyéni keresési API egy RESTful webszolgáltatás, amely a legtöbb programozási nyelvvel kompatibilis. A minta forráskódja elérhető a [GitHubon.](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingCustomSearchv7.py)

## <a name="prerequisites"></a>Előfeltételek

- Bing egyéni keresési példánya. További információ: További információ [az első Bing egyéni keresési példány létrehozása](quick-start.md) című témakörben.
- [Piton](https://www.python.org/) 2.x vagy 3.x

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új Python-fájlt a kedvenc IDE-ben vagy szerkesztőjében, és adja hozzá a következő importálási kimutatásokat. Hozzon létre változókat az előfizetési kulcshoz, az egyéni konfigurációs azonosítóhoz és egy keresési kifejezéshez. 

    ```python
    import json
    import requests
    
    subscriptionKey = "YOUR-SUBSCRIPTION-KEY"
    customConfigId = "YOUR-CUSTOM-CONFIG-ID"
    searchTerm = "microsoft"
    ```

## <a name="send-and-receive-a-search-request"></a>Keresési kérelem küldése és fogadása 

1. A kérelem URL-címét úgy hozhatja `q=` létre, hogy hozzáfűzi a keresési `customconfig=`kifejezést a lekérdezési paraméterhez, és a keresési példány egyéni konfigurációazonosítóját a hoz. a paramétereket egy `&` karakterrel válassza el. Használhatja az alábbi globális végpontot, vagy az [egyéni altartomány-végpontot,](../../cognitive-services/cognitive-services-custom-subdomains.md) amely az azure-portálon jelenik meg az erőforráshoz.

    ```python
    url = 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 'q=' + searchTerm + '&' + 'customconfig=' + customConfigId
    ```

2. Küldje el a kérelmet a Bing egyéni keresési példányának, és nyomtassa ki a visszaadott keresési eredményeket.  

    ```python
    r = requests.get(url, headers={'Ocp-Apim-Subscription-Key': subscriptionKey})
    print(r.text)
    ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyéni keresési webalkalmazás létrehozása](./tutorials/custom-search-web-page.md)
