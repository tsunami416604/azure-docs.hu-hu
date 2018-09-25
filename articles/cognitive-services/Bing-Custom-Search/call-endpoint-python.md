---
title: Végpont meghívása a Microsoft Cognitive Services – Bing egyéni keresés – Python használatával
description: 'Ez a rövid útmutató bemutatja, hogyan keresési eredmények kérhet az egyéni keresőpéldányok: a Bing Custom Search-végpont meghívása a Python használatával.'
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: 3666e92372e9bed80e5c0c7991dcac730cebb588
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46967592"
---
# <a name="call-bing-custom-search-endpoint-python"></a>Hívást a Bing Custom Search-végpont (Python)

Ez a rövid útmutató bemutatja, hogyan kérhet a keresési eredmények a Python használatával hívja meg a Bing Custom Search-végpont egyéni keresési példány. 

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez a következőkre lesz szüksége:

- Egy használatra kész egyéni keresési példány. Lásd: [az első Bing Custom Search-példány létrehozása](quick-start.md).
- [Python](https://www.python.org/) telepítve.
- Egy előfizetési kulcsot. Is kap egy előfizetési kulcsot, aktiválásakor a [az ingyenes próbaidőszak](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search), vagy használhat egy díjköteles előfizetési kulcsot az Azure irányítópultján (lásd: [Cognitive Services API-fiók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)).    


## <a name="run-the-code"></a>A kód futtatása

Ez a példa futtatásához kövesse az alábbi lépéseket:

1. Hozzon létre egy mappát a kódot.  
  
2. Rendszergazdai parancssorból vagy egy terminálban keresse meg az imént létrehozott mappára.  
  
3. Telepítse a **kérelmek** python modult:  
  
    <pre>
    pip install pipenv
    pipenv install requests
    </pre>  
      
4. Hozzon létre egy fájlt a mappában létrehozott BingCustomSearch.py, és másolja az alábbi kódot a fájlba. Cserélje le **YOUR-SUBSCRIPTION-KEY** és **YOUR-CUSTOM-CONFIG-ID** a subscriptioin kulcs és a konfiguráció azonosítója.  
  
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
- [A központi felhasználói felület konfigurálása](./hosted-ui.md)
- [Kiemeli a szöveget decoration jelölők használatával](./hit-highlighting.md)
- [Oldal weblapok](./page-webpages.md)
