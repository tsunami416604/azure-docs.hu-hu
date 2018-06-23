---
title: A Bing Visual keresés API a JavaScript gyors üzembe helyezés |} Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Bemutatja, hogyan lemezkép feltöltése a Bing Visual keresési API-hoz, és vissza a képet kaphat.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: dd28c829d8d24980a746244dc6aca880d2d69224
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349346"
---
# <a name="your-first-bing-visual-search-query-in-javascript"></a>A JavaScript első Bing Visual keresési lekérdezés

Bing Visual Search API, amely megadja a képfájl információt ad vissza. A kép URL-címe, lexikális elem, vagy egy kép feltöltésével egy insights segítségével megadhatja a lemezképet. További információ a kapcsolókról: [Bing Visual Search API újdonságai?](../overview.md) Ez a cikk bemutatja, hogy egy kép feltöltésével. Egy kép feltöltésével olyan mobil forgatókönyveknél, ahol készíthet egy képet arról a jól ismert jellegzetes, és arra vonatkozó információval segítségnyújtáshoz hasznos lehet. Például a feltárása a jellegzetes kapcsolatos trivia tartalmazhatnak. 

Ha feltölti egy helyi lemezképet, a következő jeleníti meg az űrlap adatait meg kell adni a FELADÁS egy vagy több törzsében. Az űrlap adatait tartalmaznia kell a tartalom-szabályozó fejlécben. A `name` paraméter "kép" értékre kell állítani, és a `filename` paraméter bármilyen karakterlánc lehet beállítani. A képernyő tartalmát a bináris kép. A maximális kép lehet, hogy feltölti mérete 1 MB. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

A cikk tartalmaz egy egyszerű konzolalkalmazást, amely a Bing Visual keresési API-kérést küld, és a JSON-keresési eredményeit jeleníti meg. Az alkalmazás JavaScript nyelven van megírva, amíg az API-t olyan kompatibilis bármely programozási nyelv, amely HTTP-kérelmeket, és elemezni a JSON a RESTful webes szolgáltatás. 

## <a name="prerequisites"></a>Előfeltételek

Szüksége [Node.js 6](https://nodejs.org/en/download/) futtatásához ezt a kódot.

A gyors üzembe helyezés, a segítségével egy [ingyenes próbaverzió](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) előfizetés vagy egy fizetős billentyűt.

## <a name="running-the-application"></a>Az alkalmazás futtatása

A következő bemutatja, hogyan FormData használata node.js-e az üzenetet.

Az alkalmazás futtatásához kövesse az alábbi lépéseket:

1. Hozzon létre egy mappát a projekthez (vagy a kedvenc IDE vagy-szerkesztő).
2. A parancssor vagy a Terminálszolgáltatások keresse meg az imént létrehozott mappára.
3. A kérelem-modulok telepítése:  
  ```  
  npm install request  
  ```  
3. A űrlapadat modulok telepítése:  
  ```  
  npm install form-data  
  ```  
4. Hozzon létre egy GetVisualInsights.js nevű fájlt, és adja hozzá a következő kódot az.
5. Cserélje le a `subscriptionKey` az Előfizetés kulcs értékének.
6. Cserélje le a `imagePath` a kép töltse fel az elérési úttal rendelkező értékhez.
7. Futtassa a programot.  
  ```
  node GetVisualInsights.js
  ```

```javascript
var request = require('request');
var FormData = require('form-data');
var fs = require('fs');

var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
var subscriptionKey = '<yoursubscriptionkeygoeshere>';
var imagePath = "<pathtoyourimagegoeshere>";

var form = new FormData();
form.append("image", fs.createReadStream(imagePath));

form.getLength(function(err, length){
  if (err) {
    return requestCallback(err);
  }

  var r = request.post(baseUri, requestCallback);
  r._form = form; 
  r.setHeader('Ocp-Apim-Subscription-Key', subscriptionKey);
});

function requestCallback(err, res, body) {
    console.log(JSON.stringify(JSON.parse(body), null, '  '))
}
```


## <a name="next-steps"></a>További lépések

[Mélyebb betekintés insights token használatával kép](../use-insights-token.md)  
[Bing Visual keresési alkalmazás oktatóanyag](../tutorial-bing-visual-search-single-page-app.md)  
[Bing Visual keresési áttekintése](../overview.md)  
[Próbálja ki](https://aka.ms/bingvisualsearchtryforfree)  
[Egy ingyenes próba hozzáférési kulcs beszerzése](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Bing Visual keresési API-referencia](https://aka.ms/bingvisualsearchreferencedoc)
