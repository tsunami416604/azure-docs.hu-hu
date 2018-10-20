---
title: Az Anomáliadetektálási kereső API használata – PHP és a Microsoft Cognitive Services |} A Microsoft Docs
description: Get information és kód minták segítségével gyorsan az PHP, a Cognitive Services-használatának Anomáliaészlelő.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.component: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: cb149d7a0a1eef2c096a3562f5d0256deca19527
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49458071"
---
# <a name="use-the-anomaly-finder-api-with-php"></a>A PHP a Anomáliaészlelő API használata

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Ez a cikk bemutatja, és kódminták segítségével gyorsan az Anomáliadetektálási kereső API-használatának PHP az anomáliadetektálási eredmény idősorozat-adatok beolvasása a feladat elvégzéséhez.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-anomaly-finder-api-using-php"></a>Az Anomáliadetektálási kereső API-jával PHP anomáliadetektálási pontok beolvasása
[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>Idősorozat-adatok – példa
A példa az idősorozat-adatok a következőképpen történik.
[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-php-example"></a>Adatok elemzése és anomáliadetektálási pontot kap a PHP-példa
1. Cserélje le a `[YOUR_SUBSCRIPTION_KEY]` értéket az érvényes előfizetői azonosítóra.
2. Cserélje le a `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` a példa vagy a saját adatpont.
3. Hajtsa végre, és ellenőrizze a választ.

```PHP
<?php
# This sample uses the Apache HTTP client from HTTP components (http://hc.apache.org/httpcomponents-client-ga/)
require_once 'HTTP/Request2.php';

$request = new HTTP_Request2('https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection');
$url = $request->getUrl();

$requestData = '[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]';

$headers = array(
    # Request headers
    'Content-Type' => 'application/json',
    # NOTE: Replace the "Ocp-Apim-Subscription-Key" value with a valid subscription key.
    'Ocp-Apim-Subscription-Key' => '[YOUR_SUBSCRIPTION_KEY]',
);

$request->setHeader($headers);

$request->setMethod(HTTP_Request2::METHOD_POST);

# Request body
$request->setBody($requestData);

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

### <a name="example-response"></a>Példaválasz

A rendszer JSON formátumban adja vissza a sikeres választ. Mintaválasz a következőképpen történik.
[!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [REST API – referencia](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
