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
ms.openlocfilehash: 73c31c7175bd4dfcb182fb76784937c176ac7702
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46977876"
---
# <a name="call-bing-custom-search-endpoint-nodejs"></a>Hívást a Bing Custom Search-végpont (Node.js)

Ez a rövid útmutató bemutatja, hogyan kérhet a keresési eredmények a Node.js használatával hívja meg a Bing Custom Search-végpont egyéni keresési példány. 

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez a következőkre lesz szüksége:

- Egy használatra kész egyéni keresési példány. Lásd: [az első Bing Custom Search-példány létrehozása](quick-start.md).
- [NODE.js](https://www.nodejs.org/) telepítve.
- Egy előfizetési kulcsot. Is kap egy előfizetési kulcsot, aktiválásakor a [az ingyenes próbaidőszak](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search), vagy használhat egy díjköteles előfizetési kulcsot az Azure irányítópultján (lásd: [Cognitive Services API-fiók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)).    

## <a name="run-the-code"></a>A kód futtatása

Ez a példa futtatásához kövesse az alábbi lépéseket:

1. Hozzon létre egy mappát a kódot.  
  
2. A parancssorba vagy terminálba keresse meg az imént létrehozott mappára.  
  
3. Telepítse a **kérelem** csomópont modul:
    <pre>
    npm install request
    </pre>  
    
4. Hozzon létre egy fájlt a mappában létrehozott BingCustomSearch.js, és másolja az alábbi kódot a fájlba. Cserélje le **YOUR-SUBSCRIPTION-KEY** és **YOUR-CUSTOM-CONFIG-ID** az előfizetési kulcs és a konfiguráció azonosítója.  
  
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
  
6. Futtassa a kódot, a következő paranccsal:  
  
    ```    
    node BingCustomSearch.js
    ``` 

## <a name="next-steps"></a>További lépések
- [A központi felhasználói felület konfigurálása](./hosted-ui.md)
- [Kiemeli a szöveget decoration jelölők használatával](./hit-highlighting.md)
- [Oldal weblapok](./page-webpages.md)
