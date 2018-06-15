---
title: Node.js - Bing egyéni keresés – Microsoft kognitív szolgáltatásokkal hívás végpont
description: A gyors üzembe helyezés bemutatja, hogyan keresési eredmények kérhet az egyéni keresési példányát hívni a Bing egyéni keresési végpont Node.js használatával.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: 48fc234e15ce3b9172d766f6fae11b51a017ce70
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "35349771"
---
# <a name="call-bing-custom-search-endpoint-nodejs"></a>Hívás Bing egyéni keresés végpont (Node.js)

A gyors üzembe helyezés bemutatja, hogyan keresési eredmények kérhet az egyéni keresési példányát hívni a Bing egyéni keresési végpont Node.js használatával. 

## <a name="prerequisites"></a>Előfeltételek
A rövid útmutató elvégzéséhez a következőkre lesz szüksége:

- Egy egyéni keresési példányát. Lásd: [hozza létre az első Bing egyéni keresési példányát](quick-start.md).

- [NODE.js](https://www.nodejs.org/) telepítve.

-  [Kognitív szolgáltatások API-fiók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) rendelkező **Bing keresési API-k**. A [ingyenes próbaverzió](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search) elegendő-e a gyors üzembe helyezés. Az elérési kulcsot, ha aktiválja az ingyenes próbaverzió, vagy egy fizetős kulcsot használhatja az Azure irányítópultról van szüksége.

## <a name="run-the-code"></a>A kód futtatása

A Bing egyéni keresés végpont hívására, kövesse az alábbi lépéseket:

1. Hozzon létre egy mappát a kódot.
2. A parancssor vagy a Terminálszolgáltatások keresse meg az imént létrehozott mappára.
3. Telepítse a **kérelem** csomópont modul:
    <pre>
    npm install request
    </pre>
4. Hozza létre a fájlt BingCustomSearch.js, és másolja azt a következő kódot.
5. Cserélje le **YOUR-ELŐFIZETÉS-kulcs** és **YOUR-egyéni-CONFIG-ID** a kulcs és a konfigurációs azonosítójú (lásd az 1. lépés).

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
6. Futtassa a következő parancsot a kódot.
    ```    
    node BingCustomSearch.js
   ``` 

## <a name="next-steps"></a>További lépések
- [A központi felhasználói felületi élmény konfigurálása](./hosted-ui.md)
- [Jelölje ki a szöveget decoration jelölők használatával](./hit-highlighting.md)
- [Lap szolgáltatásának konfigurálása](./page-webpages.md)