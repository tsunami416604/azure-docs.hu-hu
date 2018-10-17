---
title: 'Rövid útmutató: Miniatűr létrehozása – REST, Node.js – Computer Vision'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban miniatűrt hozhat létre egy képből a Computer Vision API és Node.js használatával.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 9029806119f6ee308ba9f0a5c2d45bfce38b5b54
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2018
ms.locfileid: "45633281"
---
# <a name="quickstart-generate-a-thumbnail-using-the-rest-api-and-nodejs-in-computer-vision"></a>Rövid útmutató: Miniatűr létrehozása a REST API és Node.js használatával a Computer Visionben

Ebben a rövid útmutatóban miniatűrt hozhat létre egy képből a Computer Vision API REST API-jának használatával. A [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) metódussal egy miniatűrt készíthet egy képből. Megadhatja a magasságát és a szélességét, amely eltérhet a bemeneti kép oldalarányától. A Computer Vision intelligens körbevágással azonosítja a releváns régiót, amely alapján létrehozza a vágási koordinátákat.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

- A [Node.js](https://nodejs.org) 4.x-es vagy újabb verziójával kell rendelkeznie.
- Rendelkeznie kell az [npm-mel](https://www.npmjs.com/).
- Szüksége lesz egy Computer Vision-előfizetői azonosítóra. Az előfizetői azonosító beszerzéséhez lásd az [előfizetői azonosítók beszerzéséről](../Vision-API-How-to-Topics/HowToSubscribe.md) szóló témakört.

## <a name="create-and-run-the-sample"></a>A minta létrehozása és futtatása

A minta létrehozásához és futtatásához az alábbi lépéseket kell végrehajtania:

1. Telepítse az npm [`request`](https://www.npmjs.com/package/request) csomagot.
   1. Nyissa meg a parancssori ablakot rendszergazdaként.
   1. Futtassa az alábbi parancsot:

      ```console
      npm install request
      ```

   1. A csomag sikeres telepítése után zárja be a parancssori ablakot.

1. Másolja az alábbi kódot egy szövegszerkesztőbe.
1. Hajtsa végre a következő módosításokat a kód megfelelő területein:
    1. Cserélje le a `subscriptionKey` értéket az előfizetői azonosítóra.
    1. Ha szükséges, cserélje le az `uriBase` értéket azon Azure-régió [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) metódusának végponti URL-címére, ahol az előfizetői azonosítókat beszerezte.
    1. Ha szeretné, cserélje le az `imageUrl` értéket egy másik elemzendő kép URL-címére.
1. Mentse a kódot fájlként `.js` kiterjesztéssel. Például: `get-thumbnail.js`.
1. Nyisson meg egy parancssort.
1. Amikor a rendszer kéri, a `node` paranccsal futtassa a fájlt. Például: `node get-thumbnail.js`.

```nodejs
'use strict';

const request = require('request');

// Replace <Subscription Key> with your valid subscription key.
const subscriptionKey = '<Subscription Key>';

// You must use the same location in your REST call as you used to get your
// subscription keys. For example, if you got your subscription keys from
// westus, replace "westcentralus" in the URL below with "westus".
const uriBase =
    'https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/generateThumbnail';

const imageUrl =
    'https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg';

// Request parameters.
const params = {
    'width': '100',
    'height': '100',
    'smartCropping': 'true'
};

const options = {
    uri: uriBase,
    qs: params,
    body: '{"url": ' + '"' + imageUrl + '"}',
    headers: {
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key' : subscriptionKey
    }
};

request.post(options, (error, response, body) => {
  if (error) {
    console.log('Error: ', error);
    return;
  }
});
```

## <a name="examine-the-response"></a>A válasz vizsgálata

A rendszer bináris formátumban adja vissza a sikeres választ, ahol a bináris adatok a miniatűr képadatainak felelnek meg. Ha a kérés meghiúsul, a válasz a konzolablakban jelenik meg. A meghiúsult kérés válasza tartalmaz egy hibakódot és egy üzenetet, amely segít meghatározni a hiba okát.

## <a name="next-steps"></a>További lépések

Ismerje meg a Computer Vision API-t, amellyel képeket elemezhet, hírességeket és nevezetességeket azonosíthat rajtuk, valamint miniatűrt hozhat létre, illetve nyomtatott és kézzel írott szövegeket nyerhet ki belőlük. A Computer Vision API-val való gyors kísérletezéshez próbálja ki az [Open API-tesztkonzolt](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [A Computer Vision API bemutatása](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
