---
title: 'Rövid útmutató: a Bing Custom Search végpont meghívása a Python használatával | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval megkezdheti a Bing Custom Search-példány keresési eredményeinek kérését a Python használatával.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: tracking-python
ms.openlocfilehash: b5a39951301256894553a036786ddd2fff251109
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84606003"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-python"></a>Rövid útmutató: a Bing Custom Search végpont meghívása a Python használatával

Ebből a rövid útmutatóból megtudhatja, hogyan kérheti le a Bing Custom Search-példány keresési eredményeit. Bár ez az alkalmazás Pythonban íródott, a Bing Custom Search API egy REST-alapú webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel. A minta forráskódja elérhető a [githubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingCustomSearchv7.py).

## <a name="prerequisites"></a>Előfeltételek

- Egy Bing Custom Search példány. További információ: gyors útmutató [: az első Bing Custom Search példány létrehozása](quick-start.md).
- [Python](https://www.python.org/) 2. x vagy 3. x.

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

- Hozzon létre egy új Python-fájlt a kedvenc IDE vagy szerkesztőben, és adja hozzá a következő importálási utasításokat. Hozzon létre változókat az előfizetési kulcshoz, az egyéni konfigurációs AZONOSÍTÓhoz és a keresési kifejezéshez.

    ```python
    import json
    import requests
    
    subscriptionKey = "YOUR-SUBSCRIPTION-KEY"
    customConfigId = "YOUR-CUSTOM-CONFIG-ID"
    searchTerm = "microsoft"
    ```

## <a name="send-and-receive-a-search-request"></a>Keresési kérelem küldése és fogadása 

1. Hozza létre a kérelem URL-címét úgy, hogy hozzáfűzi a keresési kifejezést a `q=` lekérdezési paraméterhez, a keresési példány egyéni konfigurációs azonosítóját pedig a `customconfig=` paraméterhez. Válassza el a paramétereket egy jellel ( `&` ). Használhatja a globális végpontot a következő kódban, vagy használhatja az erőforráshoz tartozó Azure Portalban megjelenő [Egyéni altartomány](../../cognitive-services/cognitive-services-custom-subdomains.md) -végpontot.

    ```python
    url = 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 'q=' + searchTerm + '&' + 'customconfig=' + customConfigId
    ```

2. Küldje el a kérést a Bing Custom Search példányának, és nyomtassa ki a visszaadott keresési eredményeket.  

    ```python
    r = requests.get(url, headers={'Ocp-Apim-Subscription-Key': subscriptionKey})
    print(r.text)
    ```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Egyéni keresési Webalkalmazás létrehozása](./tutorials/custom-search-web-page.md)
