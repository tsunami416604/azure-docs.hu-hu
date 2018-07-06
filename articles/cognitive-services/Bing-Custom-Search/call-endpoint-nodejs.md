---
title: Végpont meghívása a Microsoft Cognitive Services – Bing egyéni keresés – Node.js használatával
description: 'Ez a rövid útmutató bemutatja, hogyan keresési eredmények kérhet az egyéni keresőpéldányok: a Bing Custom Search-végpont meghívása Node.js használatával.'
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: 5d9391cc486dc868a1a291ccc7095291cddd3e4c
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2018
ms.locfileid: "37858459"
---
# <a name="call-bing-custom-search-endpoint-nodejs"></a>Hívást a Bing Custom Search-végpont (Node.js)

Ez a rövid útmutató bemutatja, hogyan keresési eredmények kérhet az egyéni keresőpéldányok: a Bing Custom Search-végpont meghívása Node.js használatával. 

## <a name="prerequisites"></a>Előfeltételek
A rövid útmutató elvégzéséhez a következőkre lesz szüksége:

- Egy egyéni keresési példány. Lásd: [az első Bing Custom Search-példány létrehozása](quick-start.md).

- [NODE.js](https://www.nodejs.org/) telepítve.

-  [Cognitive Services API-fiók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) a **Bing Search APIs**. A [az ingyenes próbaidőszak](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search) elegendő ehhez a gyors útmutatóhoz. Szüksége lesz a hozzáférési kulcsot, ha az ingyenes próbaverzió aktiválásának, vagy használhat egy díjköteles előfizetési kulcsot az Azure irányítópultján.

## <a name="run-the-code"></a>A kód futtatása

A Bing Custom Search végpont meghívására, kövesse az alábbi lépéseket:

1. Hozzon létre egy mappát a kódot.

2. A parancssorba vagy terminálba keresse meg az imént létrehozott mappára.

3. Telepítse a **kérelem** csomópont modul:
    <pre>
    npm install request
    </pre>
    
4. A fájl BingCustomSearch.js létrehozásához, és másolja a következő kód azt.

5. Cserélje le **YOUR-SUBSCRIPTION-KEY** és **YOUR-CUSTOM-CONFIG-ID** a kulcs és a konfigurációs azonosítóval (lásd az 1. lépés).

    ``` javascript
    var request = require("request");
    
    var subscriptionKey = 'YOUR-SUBSCRIPTION-KEY';
    var customConfigId = 'YOUR-CUSTOM-CONFIG-ID';
    var searchTerm = 'microsoft';
    
    var options = {
        url: 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 
          'q=' + searchTerm + 
          '&customconfig=' + customConfigId,
        headers: {
            'Ocp-Apim-Subscription-Key' : subscriptionKey
        }
    }
    
    request(options, function(error, response, body){
        var searchResponse = JSON.parse(body);
        for(var i = 0; i < searchResponse.webPages.value.length; ++i){
            var webPage = searchResponse.webPages.value[i];
            console.log('name: ' + webPage.name);
            console.log('url: ' + webPage.url);
            console.log('displayUrl: ' + webPage.displayUrl);
            console.log('snippet: ' + webPage.snippet);
            console.log('dateLastCrawled: ' + webPage.dateLastCrawled);
            console.log();
        }
    })
    ```
6. Futtassa a kódot az alábbi paranccsal.
    ```    
    node BingCustomSearch.js
   ``` 

## <a name="next-steps"></a>További lépések
- [A központi felhasználói felület konfigurálása](./hosted-ui.md)
- [Kiemeli a szöveget decoration jelölők használatával](./hit-highlighting.md)
- [Oldal weblapok](./page-webpages.md)
