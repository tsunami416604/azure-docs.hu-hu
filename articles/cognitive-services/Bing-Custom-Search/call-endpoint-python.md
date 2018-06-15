---
title: Hívás végpont Python - Bing egyéni keresés – Microsoft kognitív Services segítségével
description: A gyors üzembe helyezés keresési eredmények kérhet az egyéni keresési példányát Python hívni a Bing egyéni keresési végpont használatával mutatja be.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: 889762ae0b401438f25546738268c584ddd58389
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "35349774"
---
# <a name="call-bing-custom-search-endpoint-python"></a>Hívás Bing egyéni keresés végpont (Python)

A gyors üzembe helyezés keresési eredmények kérhet az egyéni keresési példányát Python hívni a Bing egyéni keresési végpont használatával mutatja be. 

## <a name="prerequisites"></a>Előfeltételek
A rövid útmutató elvégzéséhez a következőkre lesz szüksége:

- Egy egyéni keresési példányát. Lásd: [hozza létre az első Bing egyéni keresési példányát](quick-start.md).

-  [Python](https://www.python.org/) telepítve.

- A [kognitív szolgáltatások API-fiók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) rendelkező **Bing keresési API-k**. A [ingyenes próbaverzió](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search) elegendő-e a gyors üzembe helyezés. Az elérési kulcsot, ha aktiválja az ingyenes próbaverzió, vagy egy fizetős kulcsot használhatja az Azure irányítópultról van szüksége. 

## <a name="run-the-code"></a>A kód futtatása

A Bing egyéni keresés végpont hívására, kövesse az alábbi lépéseket:

1. Hozzon létre egy mappát a kódot.
2. Egy rendszergazdai jogú parancssort vagy a Terminálszolgáltatások keresse meg az imént létrehozott mappára.
3. Telepítse a **kérelmek** python modult:
    <pre>
    pip install pipenv
    pipenv install requests
    </pre>
7. Hozza létre a fájlt BingCustomSearch.py, és másolja azt a következő kódot.
8. Cserélje le **YOUR-ELŐFIZETÉS-kulcs** és **YOUR-egyéni-CONFIG-ID** a kulcs és a konfigurációs azonosítójú (lásd az 1. lépés).

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
9. Futtassa a következő parancsot a kódot.
    ```
    python BingCustomSearch.py
    ```

## <a name="next-steps"></a>További lépések
- [A központi felhasználói felületi élmény konfigurálása](./hosted-ui.md)
- [Jelölje ki a szöveget decoration jelölők használatával](./hit-highlighting.md)
- [Lap szolgáltatásának konfigurálása](./page-webpages.md)