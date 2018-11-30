---
title: 'Rövid útmutató: Vizuális keresési lekérdezés létrehozása Node.js nyelven – Bing Visual Search'
titleSuffix: Azure Cognitive Services
description: Bemutatja, hogyan tölthet fel egy képet a Bing Visual Search API-ba, és kaphat a képpel kapcsolatos megállapításokat.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: quickstart
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 553d068d70f7e722f3c8e4de3978f3583b941963
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52442527"
---
# <a name="quickstart-your-first-bing-visual-search-query-in-javascript"></a>Rövid útmutató: Az első Bing Visual Search-lekérdezés létrehozása JavaScript nyelven

A Bing Visual Search API információkat ad vissza egy Ön által megadott képről. A kép feltöltéséhez használhatja a kép URL-címét, egy megállapítási jogkivonatot, vagy feltöltheti magát a képet. Információ ezekről a lehetőségekről: [Mi a Bing Visual Search API?](../overview.md) Ez a cikk egy kép feltöltését mutatja be. A képfeltöltés olyan mobil forgatókönyveknél lehet hasznos, amikor képet készít egy ismert nevezetességről, amelyről információt szeretne kapni. Az információk között szerepelhetnek például az adott nevezetességre vonatkozó érdekességek. 

Ha helyi képet tölt fel, az alábbiakban láthatja a POST-törzsben kötelezően megadandó űrlapadatokat. Az űrlapadatoknak tartalmazniuk kell a Tartalom-Témakör fejlécet. A `name` paraméter értéke „image” legyen, a `filename` paraméter értéke viszont bármilyen sztring lehet. Az űrlap tartalmát a kép bináris adatai adják. A legnagyobb feltölthető képméret 1 MB. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Ez a cikk egy egyszerű konzolalkalmazást ismertet, amely Bing Visual Search API-kérést küld, majd megjeleníti a JSON-keresés eredményeit. Bár ez az alkalmazás JavaScript nyelven lett megírva, az API egy RESTful-webszolgáltatás, azaz kompatibilis minden olyan programozási nyelvvel, amely képes HTTP-kérések küldésére és JSON-elemzésre. 

## <a name="prerequisites"></a>Előfeltételek
Ebben a rövid, szüksége lesz egy előfizetést, S9 árkategória elindításához, ahogyan [Cognitive Services díjszabás – keresési Bing-API](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/search-api/). 

Előfizetés indítása az Azure Portalon:
1. A szövegmezőbe, amely szerint az Azure portal tetején adja meg a "BingSearchV7" `Search resources, services, and docs`.  
2. Marketplace-en a legördülő listában, válassza a `Bing Search v7`.
3. Adja meg `Name` az új erőforrás.
4. Válassza ki `Pay-As-You-Go` előfizetés.
5. Válassza ki `S9` tarifacsomag.
6. Kattintson a `Enable` az előfizetés indításához.

A kód futtatásához [Node.js 6](https://nodejs.org/en/download/) szükséges.

## <a name="running-the-application"></a>Az alkalmazás futtatása

Az alábbiakban azt láthatja, hogyan küldhet üzenetet űrlapadatok használatával, Node.js nyelven.

Az alkalmazás futtatásához kövesse az alábbi lépéseket:

1. Hozzon létre egy új mappát a projekthez (vagy használja kedvenc IDE-környezetét vagy szerkesztőjét).
2. Egy parancssorban vagy terminálablakban lépjen a létrehozott mappába.
3. Telepítse a lekérési modulokat:  
  ```  
  npm install request  
  ```  
3. Telepítse az űrlapadatmodulokat:  
  ```  
  npm install form-data  
  ```  
4. Hozzon létre egy GetVisualInsights.js nevű fájlt, és adja hozzá az alábbi kódot.
5. Cserélje le a `subscriptionKey` értéket saját előfizetői azonosítójára.
6. Az `imagePath` értéket cserélje le a feltölteni kívánt kép elérési útjára.
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

[Képpel kapcsolatos információk lekérése Insights-jogkivonat segítségével](../use-insights-token.md)  
[Bing Visual Search képfeltöltés – oktatóanyag](../tutorial-visual-search-image-upload.md)
[Bing Visual Search egyoldalas alkalmazás – oktatóanyag](../tutorial-bing-visual-search-single-page-app.md)  
[Bing Visual Search – áttekintés](../overview.md)  
[Kipróbálás](https://aka.ms/bingvisualsearchtryforfree)  
[Ingyenes próbaverzióhoz tartozó hozzáférési kulcs lekérése](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Bing Visual Search API – referencia](https://aka.ms/bingvisualsearchreferencedoc)
