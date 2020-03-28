---
title: 'Rövid útmutató: A Bing egyéni keresési végpontjának felhívása a Node.js használatával | Microsoft dokumentumok'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval elkezdheti a keresési eredmények et a Bing egyéni keresési példányából a Node.js használatával
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: 1c7bd97de4e46e1c8da467840006fe2520851caf
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80238865"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-nodejs"></a>Rövid útmutató: A Bing egyéni keresési végpontjának hívása a Node.js használatával

Ezzel a rövid útmutatóval megkezdheti a keresési eredmények keresését a Bing egyéni keresési példányából. Bár ez az alkalmazás JavaScript nyelven íródott, a Bing Egyéni keresési API egy RESTful webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel. A minta forráskódja elérhető a [GitHubon.](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingCustomSearchv7.js)

## <a name="prerequisites"></a>Előfeltételek

- Bing egyéni keresési példánya. További információ: További információ [az első Bing egyéni keresési példány létrehozása](quick-start.md) című témakörben.

- [Node.js](https://www.nodejs.org/)

- A [JavaScript lekérési kódtára](https://github.com/request/request)

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új JavaScript-fájlt a `require()` kedvenc IDE-ben vagy szerkesztőjében, és adjon hozzá egy utasítást a kéréstárhoz. Hozzon létre változókat az előfizetési kulcshoz, az egyéni konfigurációs azonosítóhoz és egy keresési kifejezéshez. 

    ```javascript
    var request = require("request");
    
    var subscriptionKey = 'YOUR-SUBSCRIPTION-KEY';
    var customConfigId = 'YOUR-CUSTOM-CONFIG-ID';
    var searchTerm = 'microsoft';
    ```

## <a name="send-and-receive-a-search-request"></a>Keresési kérelem küldése és fogadása 

1. Hozzon létre egy változót a kérelemben küldött adatok tárolására. A kérelem URL-címét úgy hozhatja `q=` létre, hogy hozzáfűzi a keresési `customconfig=`kifejezést a lekérdezési paraméterhez, és a keresési példány egyéni konfigurációazonosítóját a hoz. a paramétereket egy `&` karakterrel válassza el. Használhatja az alábbi globális végpontot, vagy az [egyéni altartomány-végpontot,](../../cognitive-services/cognitive-services-custom-subdomains.md) amely az azure-portálon jelenik meg az erőforráshoz.

    ```javascript
    var info = {
        url: 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 
            'q=' + searchTerm + "&" +
            'customconfig=' + customConfigId,
        headers: {
            'Ocp-Apim-Subscription-Key' : subscriptionKey
        }
    }
    ```

1. A JavaScript-kérelemtár segítségével keresési kérelmet küldhet a Bing egyéni keresési példányának, és kinyomtathatja az eredményekre vonatkozó információkat, például a nevét, az URL-címét és a weblap legutóbbi feltérképezésének dátumát.

    ```javascript
    request(info, function(error, response, body){
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
    ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyéni keresési webalkalmazás létrehozása](./tutorials/custom-search-web-page.md)
