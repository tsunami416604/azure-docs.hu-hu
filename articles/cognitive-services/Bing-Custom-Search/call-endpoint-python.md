---
title: 'Rövid útmutató: Végpont meghívása a Python – Bing Custom Search használatával'
titlesuffix: Azure Cognitive Services
description: Ez a rövid útmutató bemutatja, hogyan kérhet keresési eredményeket az egyéni keresési példányán, Pythont használva a Bing Custom Search-végpont meghívásához.
services: cognitive-services
author: brapel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: quickstart
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: d16b7354b0ef727ba3c670b97105e1bd6f99034e
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48815460"
---
# <a name="quickstart-call-bing-custom-search-endpoint-python"></a>Rövid útmutató: a Bing Custom Search-végpont meghívása (Python)

Ez a rövid útmutató bemutatja, hogyan kérhet keresési eredményeket az egyéni keresési példányán, Pythont használva a Bing Custom Search-végpont meghívásához. 

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez a következőkre lesz szüksége:

- Egy használatra kész egyéni keresési példány. Lásd: [Az első Bing Custom Search-példány létrehozása](quick-start.md).
- Telepített [Python](https://www.python.org/).
- Előfizetési azonosító. Kaphat egy előfizetési azonosítót az [ingyenes próbaidőszak](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search) aktiválásakor, vagy használhat egy díjköteles előfizetési azonosítót az Azure irányítópultján (lásd: [Cognitive Services API-fiók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)).    


## <a name="run-the-code"></a>A kód futtatása

A példa futtatásához kövesse az alábbi lépéseket:

1. Hozzon létre egy mappát a kódjához.  
  
2. Egy adminisztrátori parancssorban vagy terminálablakban lépjen a létrehozott mappába.  
  
3. Telepítse a **kérések** python-modult:  
  
    <pre>
    pip install pipenv
    pipenv install requests
    </pre>  
      
4. A létrehozott mappában hozzon létre egy BingCustomSearch.py nevű fájlt, és másolja az alábbi kódot a fájlba. A **YOUR-SUBSCRIPTION-KEY** és a **YOUR-CUSTOM-CONFIG-ID** értékeit cserélje le az előfizetési azonosítójára és a konfiguráció azonosítójára.  
  
    ``` Python
    import json
    import requests
    
    subscriptionKey = "YOUR-SUBSCRIPTION-KEY"
    customConfigId = "YOUR-CUSTOM-CONFIG-ID"
    searchTerm = "microsoft"
    
    url = 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=' + searchTerm + '&customconfig=' + customConfigId
    r = requests.get(url, headers={'Ocp-Apim-Subscription-Key': subscriptionKey})
    print(r.text)
    ```  
  
7. Futtassa a kódot az alábbi paranccsal.  
  
    ```
    python BingCustomSearch.py
    ```

## <a name="next-steps"></a>További lépések
- [Szolgáltatott felhasználói felületi élmény konfigurálása](./hosted-ui.md)
- [Dekorációs jelölők használata szöveg kiemelésére](./hit-highlighting.md)
- [Weblapok lapozása](./page-webpages.md)
