---
title: Az Anomáliadetektálási kereső API használata Ruby - Microsoft kognitív szolgáltatások |} Microsoft Docs
description: Get információkat és a kód minták segítségével gyorsan használatának megkezdésében Ruby és a Anomáliadetektálási kereső API kognitív szolgáltatásban.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: ca4754514ba5012f7e9e28981d0869d174561fb3
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348167"
---
# <a name="use-the-anomaly-finder-api-with-ruby"></a>Az Anomáliadetektálási kereső API használata Ruby

Ez a cikk és a segítségével gyorsan mintakódok használatának megkezdésében a Anomáliadetektálási kereső API Ruby a feladatnak az első alkalommal adatsorok anomáliadetektálási észlelésének eredménye.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-anomaly-finder-api-using-ruby"></a>Anomáliadetektálási kereső API-t használó Ruby anomáliadetektálási pontok lekérdezése 
[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>Adatsorozat időadatok – példa
Az adatsorozat pontjainak idő formátuma a következő, [!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-ruby-example"></a>Adatok elemzése és anomáliadetektálási pontot kap Ruby – példa

A példa lépésein a következők:

1. Telepítés [rest-ügyfél](https://github.com/rest-client/rest-client) 'gem telepítés rest-ügyfél' futtatásával.
2. Mentse alábbi kód .rb.
3. Cserélje le a `[YOUR_SUBSCRIPTION_KEY]` értéke az érvényes előfizetés-kulccsal.
4. Cserélje le a `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` a példa vagy a saját adatpontot tartalmaznak.
5. Hajtható végre, és ellenőrizze a választ.

```ruby
# https://github.com/rest-client/rest-client
require 'rest_client'

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the subscriptionKey string value with your valid subscription key.
subscription_key = '[YOUR_SUBSCRIPTION_KEY]';

endpoint = "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection";

# Replace the request data with your real data.
requestData = '[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]';

header = {
    :content_type => 'application/json',
    :'Ocp-Apim-Subscription-Key' => subscription_key
}

response = RestClient::Request.execute(
    :url => endpoint,
    :method => :post,
    :verify_ssl => true,
    :payload => requestData,
    :header => header)

# You will see the response with anomaly results
puts response.body
```

### <a name="example-response"></a>Példaválasz

A sikeres válasz JSON-ban adja vissza. Mintaválasz a következőképpen történik.
[!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [REST API – referencia](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
