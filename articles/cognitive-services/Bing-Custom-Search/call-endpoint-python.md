---
title: 'Gyors útmutató: A Bing Custom Search végpont meghívása a Python használatával | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval megkezdheti a Bing Custom Search-példány keresési eredményeinek kérését a Python használatával
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 7d3aeb2fd9072c508cad5adec9ce9129087d3a88
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405141"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-python"></a>Gyors útmutató: Bing Custom Search végpont meghívása a Python használatával

Ezzel a rövid útmutatóval megkezdheti a Bing Custom Search-példány keresési eredményeinek kérelmezését. Habár az alkalmazás Pythonban íródott, a Bing Custom Search API egy REST-alapú webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel. A minta forráskódja a [GitHubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingCustomSearchv7.py) érhető el.

## <a name="prerequisites"></a>Előfeltételek

- Egy Bing Custom Search példány. Lásd [: gyors útmutató: További információért hozza létre az](quick-start.md) első Bing Custom Search-példányát.
- [Python](https://www.python.org/) 2. x vagy 3. x

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új Python-fájlt a kedvenc IDE vagy szerkesztőben, és adja hozzá a következő importálási utasításokat. Hozzon létre változókat az előfizetési kulcshoz, az egyéni konfigurációs AZONOSÍTÓhoz és a keresési kifejezéshez. 

    ```python
    import json
    import requests
    
    subscriptionKey = "YOUR-SUBSCRIPTION-KEY"
    customConfigId = "YOUR-CUSTOM-CONFIG-ID"
    searchTerm = "microsoft"
    ```

## <a name="send-and-receive-a-search-request"></a>Keresési kérelem küldése és fogadása 

1. Hozza létre a kérelem URL-címét úgy, hogy hozzáfűzi `q=` a keresési kifejezést a lekérdezési paraméterhez, a keresési példány `customconfig=`egyéni konfigurációs azonosítóját pedig a következőre:. a paramétereket `&` a karakterrel válassza el. 

    ```python
    url = 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 'q=' + searchTerm + '&' + 'customconfig=' + customConfigId
    ```

2. Küldje el a kérést a Bing Custom Search példányának, és nyomtassa ki a visszaadott keresési eredményeket.  

    ```python
    r = requests.get(url, headers={'Ocp-Apim-Subscription-Key': subscriptionKey})
    print(r.text)
    ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyéni keresési Webalkalmazás létrehozása](./tutorials/custom-search-web-page.md)
