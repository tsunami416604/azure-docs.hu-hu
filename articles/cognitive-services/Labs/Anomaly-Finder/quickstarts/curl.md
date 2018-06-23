---
title: Az Anomáliadetektálási kereső API használata cURL - Microsoft kognitív szolgáltatások |} Microsoft Docs
description: Get-információk segítségével gyorsan használatának megkezdésében cURL és a Anomáliadetektálási kereső API kognitív szolgáltatásban.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 3c1d791b8c0478715b4ffa93cd7dfa43f9be4586
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348162"
---
# <a name="use-the-anomaly-finder-api-with-curl"></a>Az Anomáliadetektálási kereső API használata cURL

Ez a cikk és a segítségével gyorsan mintakódok Ismerkedés az Anomáliadetektálási kereső API használata cURL feladatnak az első idő adatsorok anomáliadetektálási eredménye.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-the-anomaly-finder-api-using-curl"></a>Az anomáliadetektálási pontok első használata cURL használatával Anomáliadetektálási kereső API-val 

[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>Adatsorozat időadatok – példa

A példa az adatsorozat pontjainak idő a következőképpen történik.

[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-curl-example"></a>Adatok elemzése és anomáliadetektálási pontot kap a cURL – példa

A példa lépésein a következők:

1. Cserélje le a `[YOUR_SUBSCRIPTION_KEY]` értéke az érvényes előfizetés-kulccsal.
2. Cserélje le a `[YOUR_REGION]` a helyet, ahol szerezte be az előfizetés kulcsok.
3. Cserélje le a `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` a példa vagy a saját adatpontot tartalmaznak.
4. Hajtható végre, és ellenőrizze a választ.

```cURL

curl -v -X POST "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection"
-H "Content-Type: application/json"
-H "Ocp-Apim-Subscription-Key: [YOUR_SUBSCRIPTION_KEY]"
--data-ascii "[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]" 

```

### <a name="example-response"></a>Példaválasz
A sikeres válasz JSON-ban adja vissza. Példa egy válasz a következőképpen történik: [!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [REST API – referencia](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
