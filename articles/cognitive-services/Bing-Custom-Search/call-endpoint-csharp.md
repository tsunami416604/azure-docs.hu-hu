---
title: 'Első lépések: Végpont hívása Node.js használatával – Bing Custom Search'
titlesuffix: Azure Cognitive Services
description: Ez a rövid útmutató bemutatja, hogyan kérhet le keresési eredményeket egyéni keresési példányokról a Bing Custom Search-végpont Node.js-sel való meghívásával.
services: cognitive-services
author: brapel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: quickstart
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: af77b4c06b61cda4fd18d19ac3578129004c4914
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816701"
---
# <a name="quickstart-call-bing-custom-search-endpoint-nodejs"></a>Első lépések: Bing Custom Search-végpont hívása (Node.js)

Ez a rövid útmutató bemutatja, hogyan kérhet le keresési eredményeket egyéni keresési példányokról a Bing Custom Search-végpont Node.js-sel való meghívásával. 

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez a következőkre lesz szüksége:

- Egy használatra kész Custom Search-példány. Lásd: [Az első Bing Custom Search-példánya létrehozása](quick-start.md).
- Telepített [Node.js](https://www.nodejs.org/).
- Egy előfizetői kulcs. Használhatja az [ingyenes próbaverzió](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search) aktiválásakor kapott előfizetői azonosítót, vagy egy fizetős előfizetői azonosítót az Azure-irányítópultról (lásd: [Cognitive Services API-fiók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)).    

## <a name="run-the-code"></a>A kód futtatása

A példa futtatásához kövesse az alábbi lépéseket:

1. Hozzon létre egy mappát a kód számára.  
  
2. Egy parancssorban vagy terminálablakban lépjen a létrehozott mappába.  
  
3. Telepítse a következő **request** csomópontmodult:
    <pre>
    npm install request
    </pre>  
    
4. A létrehozott mappában hozzon létre egy BingCustomSearch.js nevű fájlt, és másolja az alábbi kódot a fájlba. A **YOUR-SUBSCRIPTION-KEY** (előfizetői azonosító) és a **YOUR-CUSTOM-CONFIG-ID** (konfigurációs azonosító) helyére írja be saját adatait.  
  
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
  
6. Futtassa a kódot az alábbi paranccsal:  
  
    ```    
    node BingCustomSearch.js
    ``` 

## <a name="next-steps"></a>További lépések
- [Üzemeltetett felhasználói felület konfigurálása](./hosted-ui.md)
- [Díszítő jelölők használata szövegkiemeléshez](./hit-highlighting.md)
- [Weboldalak lapozása](./page-webpages.md)
