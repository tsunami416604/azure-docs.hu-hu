---
title: 'Gyors útmutató: A Bing Custom Search végpont meghívása a Node. js használatával | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval megkezdheti a Bing Custom Search-példány keresési eredményeinek kérését a Node. js használatával
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: 3703658d6cab78592c50a23deaf9da2a426aebec
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405170"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-nodejs"></a>Gyors útmutató: Bing Custom Search végpont meghívása a Node. js használatával

Ezzel a rövid útmutatóval megkezdheti a Bing Custom Search-példány keresési eredményeinek kérelmezését. Az alkalmazás JavaScriptben való megírásakor a Bing Custom Search API egy REST-alapú webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel. A minta forráskódja a [GitHubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingCustomSearchv7.js) érhető el.

## <a name="prerequisites"></a>Előfeltételek

- Egy Bing Custom Search példány. Lásd [: gyors útmutató: További információért hozza létre az](quick-start.md) első Bing Custom Search-példányát.

- [Node.js](https://www.nodejs.org/)

- A [JavaScript-kérelem könyvtára](https://github.com/request/request)

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új JavaScript-fájlt a kedvenc ide vagy szerkesztőben, `require()` és adjon hozzá egy utasítást a kérelmek könyvtárához. Hozzon létre változókat az előfizetési kulcshoz, az egyéni konfigurációs AZONOSÍTÓhoz és a keresési kifejezéshez. 

    ```javascript
    var request = require("request");
    
    var subscriptionKey = 'YOUR-SUBSCRIPTION-KEY';
    var customConfigId = 'YOUR-CUSTOM-CONFIG-ID';
    var searchTerm = 'microsoft';
    ```

## <a name="send-and-receive-a-search-request"></a>Keresési kérelem küldése és fogadása 

1. Hozzon létre egy változót a kérelemben elküldött információk tárolásához. Hozza létre a kérelem URL-címét úgy, hogy hozzáfűzi `q=` a keresési kifejezést a lekérdezési paraméterhez, a keresési példány `customconfig=`egyéni konfigurációs azonosítóját pedig a következőre:. a paramétereket `&` a karakterrel válassza el. 

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

1. A JavaScript-kérelem könyvtárával keresési kérést küldhet a Bing Custom Search példányának, és kinyomtathatja az eredményekre vonatkozó információkat, beleértve annak nevét, URL-címét és a weblap utolsó bejárásának dátumát.

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
> [Egyéni keresési Webalkalmazás létrehozása](./tutorials/custom-search-web-page.md)
