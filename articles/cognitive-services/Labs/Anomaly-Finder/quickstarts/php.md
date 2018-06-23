---
title: Az Anomáliadetektálási kereső API használata PHP - Microsoft kognitív szolgáltatások |} Microsoft Docs
description: Get információkat és a kód minták segítségével gyorsan használatának megkezdésében Anomáliadetektálási kereső a PHP kognitív szolgáltatásban.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: f81c99b77f931b5b259633fa8fcd0bf3e358e281
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348170"
---
# <a name="use-the-anomaly-finder-api-with-php"></a>Az Anomáliadetektálási kereső API használata PHP

Ez a cikk és a segítségével gyorsan mintakódok használatának megkezdésében a Anomáliadetektálási kereső API a PHP a feladatnak az anomáliadetektálási eredmény idő adatsorozat beolvasása.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-anomaly-finder-api-using-php"></a>Anomáliadetektálási pontok első Anomáliadetektálási kereső API-val php-s környezetekben
[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>Adatsorozat időadatok – példa
A sorozat időadatok példája az alábbiak szerint.
[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-php-example"></a>Adatok elemzése és anomáliadetektálási pontot kap PHP – példa
1. Cserélje le a `[YOUR_SUBSCRIPTION_KEY]` értéke az érvényes előfizetés-kulccsal.
2. Cserélje le a `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` a példa vagy a saját adatpontot tartalmaznak.
3. Hajtható végre, és ellenőrizze a választ.

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

A sikeres válasz JSON-ban adja vissza. Mintaválasz a következőképpen történik.
[!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [REST API – referencia](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
