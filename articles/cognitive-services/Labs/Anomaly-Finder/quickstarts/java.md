---
title: Az Anomáliadetektálási kereső API használata Java - Microsoft kognitív szolgáltatások |} Microsoft Docs
description: Get információkat és a kód minták segítségével gyorsan használatának megkezdésében Java és a Anomáliadetektálás kognitív szolgáltatásban.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: kefre
ms.openlocfilehash: 8152c23e6c5332d243d851be56bab1e4085dbe5a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348182"
---
# <a name="use-the-anomaly-finder-api-with-java"></a>Az Anomáliadetektálási kereső API használata Javával

Ez a cikk és a segítségével gyorsan mintakódok használatának megkezdésében a Anomáliadetektálási észlelési API Java feladatnak az anomáliadetektálási észlelésének eredménye idő adatsorozat beolvasása.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-the-anomaly-detection-api-using-java"></a>Az Anomáliadetektálási észlelési API-t használó Java anomáliadetektálási pontok lekérdezése

[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>Adatsorozat időadatok – példa

A példa az adatsorozat pontjainak idő a következőképpen történik.

[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-java-example"></a>Adatok elemzése és anomáliadetektálási pontok például Java beolvasása

A minta futtatásához hajtsa végre a következő lépéseket:
1. Hozzon létre egy új parancssori alkalmazást.
2. Cserélje le a következő kódot a fő osztály (megtartani `package` utasítások).
3. Cserélje le a `[YOUR_SUBSCRIPTION_KEY]` értéke az érvényes előfizetés-kulccsal.
4. Cserélje le a `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` a példa vagy a saját adatpontot tartalmaznak.
5. A globális tárak letöltését a Maven tárház a `lib` a projekt könyvtár:
   * `org.apache.httpcomponents:httpclient:4.5.2`
6. Futtassa a "Fő".

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

A sikeres válasz JSON-ban adja vissza. Példa egy válasz a következőképpen történik.
[!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Java-alkalmazás](../tutorials/java-tutorial.md)
