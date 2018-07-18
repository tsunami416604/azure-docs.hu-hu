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
ms.openlocfilehash: 889762ae0b401438f25546738268c584ddd58389
ms.sourcegitcommit: 15bfce02b334b67aedd634fa864efb4849fc5ee2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2018
ms.locfileid: "35349774"
---
# <a name="call-bing-custom-search-endpoint-python"></a>Hívást a Bing Custom Search-végpont (Python)

Ez a rövid útmutató bemutatja, hogyan keresési eredmények kérhet az egyéni keresőpéldányok: a Bing Custom Search-végpont meghívása a Python használatával. 

## <a name="prerequisites"></a>Előfeltételek
A rövid útmutató elvégzéséhez a következőkre lesz szüksége:

- Egy egyéni keresési példány. Lásd: [az első Bing Custom Search-példány létrehozása](quick-start.md).

-  [Python](https://www.python.org/) telepítve.

- A [Cognitive Services API-fiók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) a **Bing Search APIs**. A [az ingyenes próbaidőszak](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search) elegendő ehhez a gyors útmutatóhoz. Szüksége lesz a hozzáférési kulcsot, ha az ingyenes próbaverzió aktiválásának, vagy használhat egy díjköteles előfizetési kulcsot az Azure irányítópultján. 

## <a name="run-the-code"></a>A kód futtatása

A Bing Custom Search végpont meghívására, kövesse az alábbi lépéseket:

1. Hozzon létre egy mappát a kódot.
2. Rendszergazdai parancssorból vagy egy terminálban keresse meg az imént létrehozott mappára.
3. Telepítse a **kérelmek** python modult:
    <pre>
    pip install pipenv
    pipenv install requests
    </pre>
7. A fájl BingCustomSearch.py létrehozásához, és másolja a következő kód azt.
8. Cserélje le **YOUR-SUBSCRIPTION-KEY** és **YOUR-CUSTOM-CONFIG-ID** a kulcs és a konfigurációs azonosítóval (lásd az 1. lépés).

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
9. Futtassa a kódot az alábbi paranccsal.
    ```
    python BingCustomSearch.py
    ```

## <a name="next-steps"></a>További lépések
- [A központi felhasználói felület konfigurálása](./hosted-ui.md)
- [Kiemeli a szöveget decoration jelölők használatával](./hit-highlighting.md)
- [Oldal weblapok](./page-webpages.md)