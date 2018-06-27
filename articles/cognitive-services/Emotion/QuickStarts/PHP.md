---
title: Érzelemfelismerési API PHP – első lépések |} Microsoft Docs
description: Get információkat és a kód minták segítségével gyorsan használatának megkezdésében a Érzelemfelismerési API-t a PHP kognitív szolgáltatásban.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 05/23/2017
ms.author: anroth
ms.openlocfilehash: 987d5a3eedaa17f1127be34e5f90ec2456fab99b
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37019397"
---
# <a name="emotion-api-php-quick-start"></a>Érzelemfelismerési API PHP – első lépések

> [!IMPORTANT]
> Villámnézet API a 2017. október 30 véget ér. Kipróbálhatja az új [videó indexelő API előnézete](https://azure.microsoft.com/services/cognitive-services/video-indexer/) insights könnyen kibontani videók, és tartalom felderítési lép, például a keresési eredmények, növelje a szóbeli szavakat, a lapok, a karakterek és a érzelmek észlelésével. [További információk](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

Ez a cikk és a segítségével gyorsan mintakódok használatának megkezdésében a PHP és a [Érzelemfelismerési API-t ismeri fel a metódust](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) ismeri fel a kép egy vagy több személy által kifejezett érzelmek. 

## <a name="prerequisite"></a>Előfeltétel
* Az ingyenes előfizetés kulcs lekérése [Itt](https://azure.microsoft.com/try/cognitive-services/)

## <a name="recognize-emotions-php-example-request"></a>Ismeri fel a érzelmek PHP kérelem (példa)

Módosítsa a többi URL-címet a helyet, ahol szerezte be az előfizetés kulcsok, módosítsa a szervezet a vizsgálni kívánt kép URL-t és az "Ocp-Apim-előfizetés-kulcsot" érték lecserélése az érvényes előfizetés-kulcs.

```php
<?php
// This sample uses the Apache HTTP client from HTTP Components (http://hc.apache.org/httpcomponents-client-ga/)
require_once 'HTTP/Request2.php';

// NOTE: You must use the same region in your REST call as you used to obtain your subscription keys.
//   For example, if you obtained your subscription keys from westcentralus, replace "westus" in the 
//   URL below with "westcentralus".
$request = new Http_Request2('https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize');
$url = $request->getUrl();

$headers = array(
    // Request headers
    'Content-Type' => 'application/json',

    // NOTE: Replace the "Ocp-Apim-Subscription-Key" value with a valid subscription key.
    'Ocp-Apim-Subscription-Key' => '13hc77781f7e4b19b5fcdd72a8df7156',
);

$request->setHeader($headers);

$parameters = array(
    // Request parameters
);

$url->setQueryVariables($parameters);

$request->setMethod(HTTP_Request2::METHOD_POST);

// Request body
$request->setBody('{"url": "http://www.example.com/images/image.jpg"}');

try
{
    $response = $request->send();
    echo $response->getBody();
}
catch (HttpException $ex)
{
    echo $ex;
}

?>
```

## <a name="recognize-emotions-sample-response"></a>Ismeri fel a érzelmek Mintaválasz
Sikeres meghívását arcfelismerési bejegyzések tömbje és a kapcsolódó érzelemfelismerési eredményeiket, arc téglalap mérete csökkenő sorrendben szerinti sorrendben adja vissza. Üres választ, hogy nincs lapok észlelt. Érzelemfelismerési bejegyzés a következő mezőket tartalmazza:
* faceRectangle - felületen a kép helyének.
* pontszámok - Érzelemfelismerési pontszámok minden lap a lemezképben. 

```json
application/json 
[
  {
    "faceRectangle": {
      "left": 68,
      "top": 97,
      "width": 64,
      "height": 97
    },
    "scores": {
      "anger": 0.00300731952,
      "contempt": 5.14648448E-08,
      "disgust": 9.180124E-06,
      "fear": 0.0001912825,
      "happiness": 0.9875571,
      "neutral": 0.0009861537,
      "sadness": 1.889955E-05,
      "surprise": 0.008229999
    }
  }
]

