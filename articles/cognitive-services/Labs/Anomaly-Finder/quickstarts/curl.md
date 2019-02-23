---
title: Az Anomáliadetektálási kereső API használata cURL - a Microsoft Cognitive Services |} A Microsoft Docs
description: Get-információk segítségével gyorsan használatának első lépései a cURL és az Anomáliadetektálási kereső API-t a Cognitive Services.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 7a4996c83d57b34fcfcff43650b21359acb4e65e
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56730885"
---
# <a name="use-the-anomaly-finder-api-with-curl"></a>Az Anomáliadetektálási kereső API-val a curl használatával

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Ez a cikk bemutatja, és kódminták segítségével gyorsan az Anomáliadetektálási kereső API-használatának cURL a feladatnak az anomáliadetektálási eredményét idősorozat-adatok beolvasása.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-the-anomaly-finder-api-using-curl"></a>A cURL használatával Anomáliadetektálási kereső API-val anomáliadetektálási pontok beolvasása 

[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>Idősorozat-adatok – példa

A példában az adatsorozat adatpontjainak idő a következőképpen történik.

[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-curl-example"></a>Adatok elemzése és anomáliadetektálási pontot kap a cURL-példa

A példa lépései a következők.

1. Cserélje le a `[YOUR_SUBSCRIPTION_KEY]` értéket az érvényes előfizetői azonosítóra.
2. Cserélje le a `[YOUR_REGION]` a helyet, ahol szerezte be az előfizetési kulcsok.
3. Cserélje le a `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` a példa vagy a saját adatpont.
4. Hajtsa végre, és ellenőrizze a választ.

```cURL

curl -v -X POST "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection"
-H "Content-Type: application/json"
-H "Ocp-Apim-Subscription-Key: [YOUR_SUBSCRIPTION_KEY]"
--data-ascii "[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]" 

```

### <a name="example-response"></a>Példaválasz
A rendszer JSON formátumban adja vissza a sikeres választ. Példaválasz a következőképpen történik: [!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [REST API – referencia](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
