---
title: 'Gyors útmutató: Lemezkép-elemzések, a Bing Visual Search REST API és a Node.js használatával'
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan kaphat elemzési információkat, és töltsön fel egy képet, a Bing Visual Search API.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 7a0103e21b4c287526e53b9f886e98027f49c392
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55863992"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-nodejs"></a>Gyors útmutató: Lemezkép-elemzések, a Bing Visual Search REST API és a Node.js használatával

Ez a rövid útmutató segítségével a Bing Visual Search API az első hívását, és megtekintheti a keresési eredmények között. Ez az egyszerű JavaScript alkalmazás Rendszerképadatok az API-t, és a visszaadott kapcsolatos információkat jeleníti meg. Az alkalmazás JavaScript nyelven van megírva, míg a API-ját egy REST-alapú webszolgáltatás szinte bármelyik programozási nyelvével kompatibilis.

Ha feltöltenek egy helyi lemezképet, az űrlapadatok tartalmaznia kell a tartalom-szabályozó fejléc. A `name` paraméter értéke „image” legyen, a `filename` paraméter értéke viszont bármilyen sztring lehet. Az űrlap tartalmát a kép bináris adatai adják. A legnagyobb feltölthető képméret 1 MB.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

## <a name="prerequisites"></a>Előfeltételek

* [Node.js](https://nodejs.org/en/download/)
* A JavaScript-kérelem modul
    * Ez a modul segítségével lehet telepíteni `npm install request`
* Az űrlap-adatok modul
    * Ez a modul segítségével lehet telepíteni `npm install form-data`


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]


## <a name="initialize-the-application"></a>Az alkalmazás inicializálása

1. Hozzon létre egy új JavaScript-fájlt a kedvenc integrált Fejlesztőkörnyezetével vagy szerkesztőjével, és állítsa be az alábbi követelményeknek:

    ```javascript
    var request = require('request');
    var FormData = require('form-data');
    var fs = require('fs');
    ```

2. Változók létrehozása az API-végpont, előfizetési kulcsot, és a lemezkép elérési útja.

    ```javascript
    var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
    var subscriptionKey = 'your-api-key';
    var imagePath = "path-to-your-image";
    ```

3. Hozzon létre egy függvényt, nevű `requestCallback()` nyomtatása válasz az API-ból.

    ```javascript
    function requestCallback(err, res, body) {
        console.log(JSON.stringify(JSON.parse(body), null, '  '))
    }
    ```

## <a name="construct-and-send-the-search-request"></a>Hozza létre, és a keresési kérelem küldése

1. Hozzon létre egy új űrlapadatokból `FormData()`, és fűzze hozzá a lemezkép elérési útja, használatával `fs.createReadStream()`.
    
    ```javascript
    var form = new FormData();
    form.append("image", fs.createReadStream(imagePath));
    ```

2. A kérelem könyvtár segítségével feltölti a lemezképet hívása `requestCallback()` a válasz. Győződjön meg arról, az előfizetési kulcs hozzáadása a kérelem fejlécében. 

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
> [Egyéni keresés webes alkalmazás készítése](../tutorial-bing-visual-search-single-page-app.md)
