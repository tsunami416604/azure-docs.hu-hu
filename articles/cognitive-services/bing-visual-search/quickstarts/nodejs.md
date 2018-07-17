---
title: A Bing Visual Search API a JavaScript rövid |} A Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Bemutatja, hogyan tölthet fel képeket a Bing Visual Search API, majd a kép információival.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 60b1dc9b8ea9eda258e9776b8967df38c97d964e
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39071702"
---
# <a name="your-first-bing-visual-search-query-in-javascript"></a>Az első a Bing vizuális keresési lekérdezés JavaScript

A Bing Visual Search API az Ön által megadott kép adatait adja vissza. Megadhatja a kép URL-címét a képet, egy jogkivonat, vagy egy kép feltöltésével insights használatával. Ezek a beállítások kapcsolatos információkért lásd: [Mi az a Bing Visual Search API?](../overview.md) Ez a cikk bemutatja a kép feltöltése. Kép feltöltése mobil forgatókönyveknél, ahol, egy jól ismert tereptárgyak egy képet, majd az információk hasznosak lehetnek. Az insights például magukban foglalhatják a tereptárgyak kapcsolatos trivia. 

Ha a helyi rendszerképet tölt fel, az alábbiakban látható az űrlapadatok szerepelnie kell a bejegyzés törzse. Az űrlap adatait tartalmaznia kell a tartalom-szabályozó fejléc. A `name` paraméter "image" értékre kell állítani, és a `filename` paraméter bármilyen karakterlánc lehet beállítani. A képernyő tartalmát a rendszerkép a bináris. A maximális képméret tölthet fel érték 1 MB. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Ez a cikk tartalmaz egy egyszerű konzolalkalmazást, amely a Bing Visual Search API-kérést küld, és a JSON keresési eredményeit jeleníti meg. Ez az alkalmazás JavaScript nyelven van megírva, míg a API-ját kompatibilis minden programozási nyelvet, amely JSON elemzése és a HTTP-kéréseket a webes RESTful szolgáltatás. 

## <a name="prerequisites"></a>Előfeltételek

Szükséges [Node.js 6](https://nodejs.org/en/download/) Ez a kód futtatásához.

Használhatja az ebben a rövid útmutatóban egy [az ingyenes próbaidőszak](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) előfizetési kulcs vagy egy fizetős kulcsot.

## <a name="running-the-application"></a>Az alkalmazás futtatása

A következő bemutatja, hogyan szeretné elküldeni az üzenetet, a node.js-ben űrlapadat-tartalmat használ.

Az alkalmazás futtatásához kövesse az alábbi lépéseket:

1. Hozzon létre egy mappát a projekt számára (vagy a kedvenc integrált Fejlesztőkörnyezetével vagy szerkesztőjével használja).
2. A parancssorba vagy terminálba keresse meg az imént létrehozott mappára.
3. A kérelem-modulok telepítéséhez:  
  ```  
  npm install request  
  ```  
3. Az űrlap-data-modulok telepítéséhez:  
  ```  
  npm install form-data  
  ```  
4. Hozzon létre egy fájlt GetVisualInsights.js, és adja hozzá a következő kód azt.
5. Cserélje le a `subscriptionKey` az előfizetési kulcs-érték.
6. Cserélje le a `imagePath` a Rendszerkép feltöltése az elérési úttal rendelkező értékhez.
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

[Lemezkép insights token használatával kapcsolatos elemzések lekérése](../use-insights-token.md)  
[A Bing Visual Search Rendszerkép feltöltése az oktatóanyag](../tutorial-visual-search-image-upload.md)
[Bing Visual Search egyoldalas alkalmazás oktatóanyaga](../tutorial-bing-visual-search-single-page-app.md)  
[Bing vizuális keresés áttekintése](../overview.md)  
[Próbálja ki](https://aka.ms/bingvisualsearchtryforfree)  
[Ingyenes próba hozzáférési kulcs lekérése](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[A Bing Visual Search API-referencia](https://aka.ms/bingvisualsearchreferencedoc)
