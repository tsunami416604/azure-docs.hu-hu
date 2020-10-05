---
title: 'Gyors útmutató: a REST API és a Node.js segítségével bemutatjuk a képelemzéseket Bing Visual Search'
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan tölthet fel egy rendszerképet a Bing Visual Search API és a Node.js használatával, és hogyan szerezhet be információkat a rendszerképpel kapcsolatban.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.author: scottwhi
ms.custom: devx-track-js
ms.openlocfilehash: e96ff9173d920208e067988602323626767d9017
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91277092"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-nodejs"></a>Gyors útmutató: a Bing Visual Search REST API és a Node.js

Ezzel a rövid útmutatóval megteheti az első hívást a Bing Visual Search API. Ez az egyszerű JavaScript-alkalmazás feltölt egy rendszerképet az API-ba, és megjeleníti a róluk visszaadott adatokat. Bár ez az alkalmazás JavaScript nyelven íródott, az API egy REST-alapú webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel.

## <a name="prerequisites"></a>Előfeltételek

* [Node.js](https://nodejs.org/en/download/)
* A JavaScript kérési modulja. `npm install request`A modul telepítéséhez a parancsot használhatja.
* Az űrlap-adatmodul. A `npm install form-data` parancs használatával telepítheti a modult. 

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Az alkalmazás inicializálása

1. Hozzon létre egy JavaScript-fájlt a kedvenc IDE vagy szerkesztőben, és adja meg a következő követelményeket:

    ```javascript
    var request = require('request');
    var FormData = require('form-data');
    var fs = require('fs');
    ```

2. Hozzon létre változókat az API-végponthoz, az előfizetési kulcshoz és a rendszerkép elérési útjához. Az érték esetében használhatja `baseUri` a globális végpontot a következő kódban, vagy használhatja az erőforráshoz tartozó Azure Portalban megjelenő [Egyéni altartomány](../../../cognitive-services/cognitive-services-custom-subdomains.md) -végpontot.

    ```javascript
    var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
    var subscriptionKey = 'your-api-key';
    var imagePath = "path-to-your-image";
    ```

3. Hozzon létre egy nevű függvényt az `requestCallback()` API válaszának kinyomtatásához.

    ```javascript
    function requestCallback(err, res, body) {
        console.log(JSON.stringify(JSON.parse(body), null, '  '))
    }
    ```

## <a name="construct-and-send-the-search-request"></a>A keresési kérelem létrehozása és elküldése

1. Helyi rendszerkép feltöltésekor az űrlapon szerepelnie kell a `Content-Disposition` fejlécnek. Állítsa a `name` paramétert a "rendszerkép" értékre, és állítsa a `filename` paramétert a rendszerkép fájlnevére. Az űrlap tartalma tartalmazza a rendszerkép bináris értékeit. A feltölthető maximális képméret 1 MB lehet.

   ```
   --boundary_1234-abcd
   Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

   ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

   --boundary_1234-abcd--
   ```

2. Hozzon létre egy új `FormData` objektumot a `FormData()` segítségével, és fűzze hozzá a rendszerkép elérési útját a használatával `fs.createReadStream()` .
    
    ```javascript
    var form = new FormData();
    form.append("image", fs.createReadStream(imagePath));
    ```

3. A kérelem könyvtárával töltse fel a képet, és hívja `requestCallback()` meg a válasz nyomtatását. Adja hozzá az előfizetési kulcsot a kérelem fejlécébe.

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
> [Visual Search egyoldalas Webalkalmazás létrehozása](../tutorial-bing-visual-search-single-page-app.md)
