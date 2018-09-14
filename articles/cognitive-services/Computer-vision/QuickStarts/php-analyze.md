---
title: Kép elemzése a Computer Vision API-val, PHP használatával – rövid útmutató | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Ebben a rövid útmutatóban egy képet fog elemezni a Computer Vision és a PHP használatával a Cognitive Servicesben.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 114674f47ee945717e866d97ffed747ae45decc8
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43770114"
---
# <a name="quickstart-analyze-a-remote-image---rest-php"></a>Rövid útmutató: Távoli kép elemzése – REST, PHP

Ebben a rövid útmutatóban egy képet fog elemezni vizuális jellemzők kinyeréséhez a Computer Visionnel.

## <a name="prerequisites"></a>Előfeltételek

A Computer Vision használatához előfizetési kulcsra van szüksége, lásd az [előfizetési kulcsok beszerzéséről](../Vision-API-How-to-Topics/HowToSubscribe.md) szóló témakört.

## <a name="analyze-image-request"></a>Képelemzési kérés

Az [Analyze Image metódussal](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) vizuális jellemzőket nyerhet ki a képek tartalma alapján. Feltöltheti a képet, vagy megadhatja a kép URL-címét, és kiválaszthatja a kapni kívánt jellemzőket, úgymint:

* a kép tartalmához kapcsolódó címkék részletes listája;
* a kép tartalmának leírása teljes mondatban;
* a képen szereplő arcok koordinátái, neme és kora;
* a kép típusa (ClipArt vagy vonalrajz);
* a domináns és a kiegészítő színek, vagy hogy a kép fekete-fehér;
* az ebben a [taxonómiában](../Category-Taxonomy.md) meghatározott kategória;
* tartalmaz-e a kép felnőtteknek szóló és szexuális tartalmat.

A minta futtatásához az alábbi lépéseket kell végrehajtania:

1. Másolja az alábbi kódot egy szerkesztőbe.
1. A `<Subscription Key>` helyére írja be az érvényes előfizetési kulcsot.
1. Ha szükséges, változtassa meg az `uriBase` elem értékét arra a helyre, ahonnan az előfizetési kulcsot beszerezte.
1. Az `imageUrl` elem értékét beállíthatja az elemezni kívánt kép elérési útjára.
1. A válasz nyelvét (`'language' => 'en'`) is módosíthatja.
1. Mentse a fájlt `.php` kiterjesztéssel.
1. Nyissa meg a fájlt egy PHP-támogatással rendelkező böngészőablakban.

Ebben a példában a PHP5 [HTTP_Request2](http://pear.php.net/package/HTTP_Request2) csomagját használjuk.

```php
<html>
<head>
    <title>Analyze Image Sample</title>
</head>
<body>
<?php
// Replace <Subscription Key> with a valid subscription key.
$ocpApimSubscriptionKey = '<Subscription Key>';

// You must use the same location in your REST call as you used to obtain
// your subscription keys. For example, if you obtained your subscription keys
// from westus, replace "westcentralus" in the URL below with "westus".
$uriBase = 'https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/';

$imageUrl = 'http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg';

require_once 'HTTP/Request2.php';

$request = new Http_Request2($uriBase . '/analyze');
$url = $request->getUrl();

$headers = array(
    // Request headers
    'Content-Type' => 'application/json',
    'Ocp-Apim-Subscription-Key' => $ocpApimSubscriptionKey
);
$request->setHeader($headers);

$parameters = array(
    // Request parameters
    'visualFeatures' => 'Categories,Description',
    'details' => '',
    'language' => 'en'
);
$url->setQueryVariables($parameters);

$request->setMethod(HTTP_Request2::METHOD_POST);

// Request body parameters
$body = json_encode(array('url' => $imageUrl));

// Request body
$request->setBody($body);

try
{
    $response = $request->send();
    echo "<pre>" .
        json_encode(json_decode($response->getBody()), JSON_PRETTY_PRINT) . "</pre>";
}
catch (HttpException $ex)
{
    echo "<pre>" . $ex . "</pre>";
}
?>
</body>
</html>
```

## <a name="analyze-image-response"></a>A képelemzés válasza

A rendszer JSON formátumban adja vissza a sikeres választ, például:

```json
{
  "categories": [
    {
      "name": "outdoor_water",
      "score": 0.9921875,
      "detail": {
        "landmarks": []
      }
    }
  ],
  "description": {
    "tags": [
      "nature",
      "water",
      "waterfall",
      "outdoor",
      "rock",
      "mountain",
      "rocky",
      "grass",
      "hill",
      "covered",
      "hillside",
      "standing",
      "side",
      "group",
      "walking",
      "white",
      "man",
      "large",
      "snow",
      "grazing",
      "forest",
      "slope",
      "herd",
      "river",
      "giraffe",
      "field"
    ],
    "captions": [
      {
        "text": "a large waterfall over a rocky cliff",
        "confidence": 0.916458423253597
      }
    ]
  },
  "requestId": "ebf5a1bc-3ba2-4c56-99b4-bbd20ba28705",
  "metadata": {
    "height": 959,
    "width": 1280,
    "format": "Jpeg"
  }
}
```

## <a name="next-steps"></a>További lépések

Ismerje meg a Computer Vision API-kat, amelyekkel képeket elemezhet, hírességeket és nevezetességeket azonosíthat rajtuk, valamint miniatűrt hozhat létre, illetve nyomtatott és kézzel írott szövegeket nyerhet ki belőlük. A Computer Vision API-kkal való gyors kísérletezéshez próbálja ki az [Open API-tesztkonzolt](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Ismerkedjen meg a Computer Vision API-k működésével](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
