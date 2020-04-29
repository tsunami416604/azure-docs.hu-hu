---
title: 'Gyors útmutató: képelemzések beolvasása a REST API és a Node. js használatával – Bing Visual Search'
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan tölthet fel képet a Bing Visual Search API, és hogyan szerezhet be információkat.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: scottwhi
ms.openlocfilehash: 373d6fa5402ba703cbebe88ad562974ba97f3391
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75379708"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-nodejs"></a>Gyors útmutató: képelemzések beolvasása a Bing Visual Search REST API és Node. js használatával

Ezzel a rövid útmutatóval elvégezheti az első hívását a Bing Visual Search API, és megtekintheti a keresési eredményeket. Ez az egyszerű JavaScript-alkalmazás feltölt egy rendszerképet az API-ba, és megjeleníti a róluk visszaadott adatokat. Az alkalmazás JavaScriptben való megírásakor az API egy REST-alapú webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel.

## <a name="prerequisites"></a>Előfeltételek

* [Node.js](https://nodejs.org/en/download/)
* A JavaScript kérési modulja. A modul telepítéséhez `npm install request` a parancsot használhatja.
* Az űrlap-adatmodul. A `npm install form-data` parancs használatával telepítheti a modult. 

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Az alkalmazás inicializálása

1. Hozzon létre egy JavaScript-fájlt a kedvenc IDE vagy szerkesztőben, és adja meg a következő követelményeket:

    ```javascript
    var request = require('request');
    var FormData = require('form-data');
    var fs = require('fs');
    ```

2. Hozzon létre változókat az API-végponthoz, az előfizetési kulcshoz és a rendszerkép elérési útjához. `baseUri`az alábbi globális végpont lehet, vagy az erőforráshoz tartozó Azure Portalban megjelenő [Egyéni altartomány](../../../cognitive-services/cognitive-services-custom-subdomains.md) végpontja:

    ```javascript
    var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
    var subscriptionKey = 'your-api-key';
    var imagePath = "path-to-your-image";
    ```

3. Hozzon létre egy `requestCallback()` nevű függvényt az API válaszának kinyomtatásához:

    ```javascript
    function requestCallback(err, res, body) {
        console.log(JSON.stringify(JSON.parse(body), null, '  '))
    }
    ```

## <a name="construct-and-send-the-search-request"></a>A keresési kérelem létrehozása és elküldése

Helyi rendszerkép feltöltésekor az űrlaphoz tartozó adatoknak szerepelniük `Content-Disposition` kell a fejlécben. A `name` paramétert a "rendszerkép" értékre kell állítani, `filename` és a paraméter tetszőleges sztringre állítható be. Az űrlap tartalma tartalmazza a rendszerkép bináris értékeit. A legnagyobb feltölthető képméret 1 MB.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

1. Hozzon létre **FormData** egy új FormData `FormData()`objektumot a használatával, és fűzze hozzá a rendszerkép elérési útját a következő használatával `fs.createReadStream()`:
    
    ```javascript
    var form = new FormData();
    form.append("image", fs.createReadStream(imagePath));
    ```

2. A kérelem könyvtárával töltse fel a képet, és hívja `requestCallback()` meg a válasz nyomtatását. Ügyeljen arra, hogy az előfizetési kulcsot hozzáadja a kérelem fejlécébe:

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
