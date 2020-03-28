---
title: 'Rövid útmutató: Képelemzésbe való betekintés a REST API és a Node.js használatával – Bing Visual Search'
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogy miként tölthet fel képet a Bing Visual Search API-ba, és hogyan kaphat betekintést.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: scottwhi
ms.openlocfilehash: 373d6fa5402ba703cbebe88ad562974ba97f3391
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75379708"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-nodejs"></a>Rövid útmutató: Képelemzési adatok beszereznie a Bing Visual Search REST API-val és a Node.js használatával

Ezzel a rövid útmutatóval első alkalommal hívhatja meg a Bing Visual Search API-t, és megtekintheti a keresési eredményeket. Ez az egyszerű JavaScript-alkalmazás feltölt egy képet az API-ba, és megjeleníti a róla visszaadott információkat. Bár ez az alkalmazás JavaScript nyelven íródott, az API egy RESTful webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel.

## <a name="prerequisites"></a>Előfeltételek

* [Node.js](https://nodejs.org/en/download/)
* A JavaScript kérése modul. A modul `npm install request` telepítéséhez a paranccsal telepítheti a modult.
* Az űrlapadat modul. A parancs `npm install form-data` segítségével telepítheti a modult. 

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Az alkalmazás inicializálása

1. Hozzon létre egy JavaScript-fájlt kedvenc IDE-jében vagy szerkesztőjében, és állítsa be a következő követelményeket:

    ```javascript
    var request = require('request');
    var FormData = require('form-data');
    var fs = require('fs');
    ```

2. Hozzon létre változókat az API-végponthoz, az előfizetési kulcshoz és a lemezkép elérési úthoz. `baseUri`lehet az alábbi globális végpont, vagy az erőforráshoz az Azure Portalon megjelenő [egyéni altartomány-végpont:](../../../cognitive-services/cognitive-services-custom-subdomains.md)

    ```javascript
    var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
    var subscriptionKey = 'your-api-key';
    var imagePath = "path-to-your-image";
    ```

3. Hozzon létre `requestCallback()` egy függvényt, amely kinyomtatja a választ az API-ból:

    ```javascript
    function requestCallback(err, res, body) {
        console.log(JSON.stringify(JSON.parse(body), null, '  '))
    }
    ```

## <a name="construct-and-send-the-search-request"></a>A keresési kérelem megépítése és elküldése

Helyi kép feltöltésekénaz űrlapadatoknak `Content-Disposition` tartalmazniuk kell a fejlécet. Be kell `name` állítani a paraméter "kép", és a `filename` paraméter lehet beállítani, hogy bármilyen karakterláncot. Az űrlap tartalma tartalmazza a kép bináris adatait. A legnagyobb feltölthető képméret 1 MB.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

1. Hozzon létre egy `FormData()`új **FormData** objektumot a használatával, és fűzz hozzá a kép elérési útját a következő használatával: `fs.createReadStream()`
    
    ```javascript
    var form = new FormData();
    form.append("image", fs.createReadStream(imagePath));
    ```

2. A kérelemtár segítségével töltse fel `requestCallback()` a képet, és hívja a válasz nyomtatásához. Ügyeljen arra, hogy az előfizetési kulcsot hozzáadja a kérelem fejlécéhez:

    ```javascript
    form.getLength(function(err, length){
      if (err) {
        return requestCallback(err);
      }
      var r = request.post(baseUri, requestCallback);
      r._form = form; 
      r.setHeader('Ocp-Apim-Subscription-Key', subscriptionKey);
    });
    ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Visual Search egyoldalas webalkalmazás létrehozása](../tutorial-bing-visual-search-single-page-app.md)
