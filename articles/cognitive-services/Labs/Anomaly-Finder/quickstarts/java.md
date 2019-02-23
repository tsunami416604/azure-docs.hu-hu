---
title: Az Anomáliadetektálási kereső API használata a Javával – a Microsoft Cognitive Services |} A Microsoft Docs
description: Get information és kód minták segítségével gyorsan használatának első lépései a Java és a rendellenességek észlelése a Cognitive Services.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: kefre
ms.openlocfilehash: 0f96ee13e6020bc69508f8ced45741fe7dec396a
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56732879"
---
# <a name="use-the-anomaly-finder-api-with-java"></a>A Anomáliaészlelő API használata Javával

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Ez a cikk bemutatja, és kódminták segítségével gyorsan az Anomáliadetektáló API-használatának Java a feladatnak az anomáliadetektálási az észlelés eredménye idősorozat-adatok beolvasása.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-the-anomaly-detection-api-using-java"></a>A Java használatával Anomáliadetektálás API kezdeti lépéseket ismertető anomáliadetektálási pontokat

[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>Idősorozat-adatok – példa

A példában az adatsorozat adatpontjainak idő a következőképpen történik.

[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-java-example"></a>Adatok elemzése és anomáliadetektálási pontok első Java-példában

A minta futtatásához hajtsa végre az alábbi lépéseket:
1. Hozzon létre egy új parancssori alkalmazást.
2. Cserélje le a Main osztály tartalmát az alábbi kódra (a `package` utasításokat tartsa meg).
3. Cserélje le a `[YOUR_SUBSCRIPTION_KEY]` értéket az érvényes előfizetői azonosítóra.
4. Cserélje le a `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` a példa vagy a saját adatpont.
5. Töltse le az alábbi globális kódtárakat a Maven-adattárból a projektje `lib` könyvtárába:
   * `org.apache.httpcomponents:httpclient:4.5.2`
6. Futtassa a „Main” parancsot.

```java

import org.apache.http.HttpEntity;
import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.StringEntity;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.util.EntityUtils;

public class Main {
    // **********************************************
    // *** Update or verify the following values. ***
    // **********************************************

    // Replace the subscriptionKey string value with your valid subscription key.
    public static final String subscriptionKey = "[YOUR_SUBSCRIPTION_KEY]";

    public static final String uriBase = "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection";

    public static void main(String[] args) {
        final String content = "[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]";

        CloseableHttpClient client = HttpClients.createDefault();
        HttpPost request = new HttpPost(uriBase);

        // Request headers.
        request.setHeader("Content-Type", "application/json");
        request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

        try {
            StringEntity params = new StringEntity(content);
            request.setEntity(params);

            CloseableHttpResponse response = client.execute(request);
            try {
                HttpEntity respEntity = response.getEntity();
                if (respEntity != null) {
                    System.out.println("----------");
                    System.out.println(response.getStatusLine());
                    System.out.println("Response content is :\n");
                    System.out.println(EntityUtils.toString(respEntity, "utf-8"));
                    System.out.println("----------");
                }
            } catch (Exception respEx) {
                respEx.printStackTrace();
            } finally {
                response.close();
            }

        } catch (Exception ex) {
            System.err.println("Exception on Anomaly Detection: " + ex.getMessage());
            ex.printStackTrace();
        } finally {
            try {
                client.close();
            } catch (Exception e) {
                System.err.println("Exception on closing HttpClient: " + e.getMessage());
                e.printStackTrace();
            }
        }
    }
}

```

### <a name="example-response"></a>Példaválasz

A rendszer JSON formátumban adja vissza a sikeres választ. Példaválasz a következőképpen történik.
[!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Java-alkalmazás](../tutorials/java-tutorial.md)
