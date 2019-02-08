---
title: 'Gyors útmutató: Az arcok a képen – Emotion API, a PHP érzelmek felismerése'
titlesuffix: Azure Cognitive Services
description: Információk és kódminták segítségével ismerkedhet meg az Emotion API PHP-val való használatának első lépéseivel.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: emotion-api
ms.topic: quickstart
ms.date: 05/23/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: 9faf263dbc5f8c590018d1c446ba340b3b9781eb
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55884069"
---
# <a name="quickstart-build-an-app-to-recognize-emotions-on-faces-in-an-image"></a>Gyors útmutató: Érzelemfelismerés az arcok a képen az alkalmazás létrehozása.

> [!IMPORTANT]
> Az Emotion API 2019. február 15-ével elavulttá válik. Az érzelemfelismerési képesség mostantól általánosan elérhető a [Face API](https://docs.microsoft.com/azure/cognitive-services/face/) részeként. 

Ez a cikk információkkal és kódmintákkal szolgál, amelyeken keresztül gyorsan elsajátíthatja, hogyan ismerheti fel a képeken szereplő személy vagy személyek által kifejezett érzelmeket az [Emotion API Recognize metódusa](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) és a PHP használatával.

## <a name="prerequisite"></a>Előfeltétel
* Ingyenes előfizetői azonosítóját [itt](https://azure.microsoft.com/try/cognitive-services/) szerezheti be

## <a name="recognize-emotions-php-example-request"></a>Érzelemfelismerési PHP-kérésminta

Módosítsa a REST URL-címét úgy, hogy azt a helyet használja, ahonnan az előfizetői azonosítóit beszerezte, módosítsa a törzset annak a képnek az URL-címére, amelyet tesztelni szeretne, és cserélje le az „Ocp-Apim-Subscription-Key” értéket érvényes előfizetői azonosítójára.

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

## <a name="recognize-emotions-sample-response"></a>Érzelemfelismerési válaszminta
A sikeres hívás egy, az arcrekordokat és a hozzájuk tartozó érzelempontszámokat tartalmazó tömböt ad vissza, amely az adatokat az arcot jelölő téglalap mérete szerinti csökkenő sorrendben listázza. Az üres válasz azt jelzi, hogy a rendszer nem észlelt arcot. Az érzelemrekord a következő mezőket foglalja magában:
* faceRectangle – Az arcot jelölő téglalap helye a képen.
* scores – A képen szereplő egyes arcokhoz tartozó érzelempontszámok.

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
