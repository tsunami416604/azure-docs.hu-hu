---
title: 'Gyors útmutató: Rendellenességek észlelése az idősoros adatokat az Anomáliadetektálási detector használatával REST API-t és a Java használatával |} A Microsoft Docs'
description: Az Anomáliadetektálási detector használatával API segítségével észlelheti a rendellenességeket az adatsorozat egy kötegelt vagy a streamelt adatokon.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 1c8ce91a0fd8805b307e1e21bc08f9050b8a47d4
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59547039"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-java"></a>Gyors útmutató: Rendellenességek észlelése az idősoros adatokat az Anomáliadetektálási detector használatával REST API-t és a Java használatával

Ez a rövid útmutató segítségével indítsa el a rendellenességek észlelése az idősoros adatokat az Anomáliadetektálási detector használatával API két észlelési mód használatával. A Java-alkalmazás két API-kérések tartalmazó JSON-formátumú idősoros adatokat küld, és lekérdezi a válaszokat.

| API-kérelem                                        | Alkalmazás kimenete                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Kötegelt feladatként rendellenességek észlelése                        | Az idősorozat-adatokat, és minden észlelt rendellenességek igazítja adatpontok anomáliadetektálási állapota (és más adatok) tartalmazó JSON-választ. |
| A legújabb adatpont anomáliadetektálási állapotának észlelése | Az anomáliadetektálási állapota (és egyéb adatokat) az idősorozat-adatok a legutóbbi adatok pontját tartalmazó JSON-választ.                                                                                                                                         |

 Ezt az alkalmazást a Java nyelven van megírva, míg a API-ját egy REST-alapú webszolgáltatás szinte bármelyik programozási nyelvével kompatibilis.

## <a name="prerequisites"></a>Előfeltételek

- A [Java&trade; fejlesztési Kit(JDK) 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) vagy újabb.

- Ezek a kódtárak importálja a Maven tárházból
    - [A Java JSON](https://mvnrepository.com/artifact/org.json/json) csomag
    - [Az Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) csomag

- A JSON fájlt tartalmazó idősorozat-adatok mutat. A példaadatokat ebben a rövid útmutatóban található [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

## <a name="create-a-new-application"></a>Új alkalmazás létrehozása

1. Hozzon létre egy új Java-projektet a kedvenc IDE-környezetében vagy szerkesztőjében, és importálja az alábbi kódtárakat.

    ```java
    import org.apache.http.HttpEntity;
    import org.apache.http.client.methods.CloseableHttpResponse;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.entity.StringEntity;
    import org.apache.http.impl.client.CloseableHttpClient;
    import org.apache.http.impl.client.HttpClients;
    import org.apache.http.util.EntityUtils;
    import org.json.JSONArray;
    import org.json.JSONObject;
    import java.io.IOException;
    import java.nio.file.Files;
    import java.nio.file.Paths;
    ```

2. Változók létrehozása az előfizetési kulcs és a végpontot. Az alábbiakban az URI-k rendellenességek észlelése is használhat. Ezek hozzá lesznek fűzve a később, hozhat létre az API-Szolgáltatásvégpont URL-címeket.

    |Észlelési módszer  |URI  |
    |---------|---------|
    |Batch-észlelés    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |A legújabb adatok ponton észlelése     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    ```java
    // Replace the subscriptionKey string value with your valid subscription key.
    static final String subscriptionKey = "[YOUR_SUBSCRIPTION_KEY]";
    //replace the endpoint URL with the correct one for your subscription. Your endpoint can be found in the Azure portal. 
    //For example: https://westus2.api.cognitive.microsoft.com
    static final String endpoint = "[YOUR_ENDPOINT_URL]";
    // Replace the dataPath string with a path to the JSON formatted time series data.
    static final String dataPath = "[PATH_TO_TIME_SERIES_DATA]";
    static final String latestPointDetectionUrl = "/anomalydetector/v1.0/timeseries/last/detect";
    static final String batchDetectionUrl = "/anomalydetector/v1.0/timeseries/entire/detect";
    ```

3. Olvassa el a JSON-adatok fájlban

    ```java
    String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "utf-8");
    ```

## <a name="create-a-function-to-send-requests"></a>Kérelmek küldése függvény létrehozása

1. Hozzon létre egy új függvényt nevű `sendRequest()` átkerül a fent létrehozott változókat. Hajtsa végre az alábbi lépéseket.

2. Hozzon létre egy `CloseableHttpClient` objektum, amely kéréseket küldhetnek az API-t. A kérelem küldése egy `HttpPost` támogatásikérelem-objektum kombinálásával a végponthoz, és a egy Anomáliadetektálási detector használatával URL-CÍMÉT.

3. Használja a kérelem `setHeader()` függvény beállítása a `Content-Type` fejlécet `application/json`, és adja hozzá az előfizetési kulcs, a `Ocp-Apim-Subscription-Key` fejléc.

4. A kérelem használata `setEntity()` függvényt, hogy az adatok küldésének.

5. Az ügyfél használata `execute()` függvényt a kérelem elküldéséhez, és mentse azt egy `CloseableHttpResponse` objektum.

6. Hozzon létre egy `HttpEntity` objektum a válasz tartalom tárolásához. A tartalom lekérése `getEntity()`. Ha a válasz nem üres, adja vissza.

```java
static String sendRequest(String apiAddress, String endpoint, String subscriptionKey, String requestData) {
    try (CloseableHttpClient client = HttpClients.createDefault()) {
        HttpPost request = new HttpPost(endpoint + apiAddress);
        // Request headers.
        request.setHeader("Content-Type", "application/json");
        request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
        request.setEntity(new StringEntity(requestData));
        try (CloseableHttpResponse response = client.execute(request)) {
            HttpEntity respEntity = response.getEntity();
            if (respEntity != null) {
                return EntityUtils.toString(respEntity, "utf-8");
            }
        } catch (Exception respEx) {
            respEx.printStackTrace();
        }
    } catch (IOException ex) {
        System.err.println("Exception on Anomaly Detector: " + ex.getMessage());
        ex.printStackTrace();
    }
    return null;
}
```

## <a name="detect-anomalies-as-a-batch"></a>Kötegelt feladatként rendellenességek észlelése

1. Hozzon létre egy meghívott metódus `detectAnomaliesBatch()` során az adatokat egy kötegelt rendellenességek észlelése. Hívja a `sendRequest()` metódus a végpont URL-címe, előfizetési kulcs és json-adatok a fent létrehozott. Az eredményt kapja, és nyomtassa ki a konzolhoz.

2. Ha a válasz tartalmaz `code` mezőben nyomtassa ki a hibakódot és a hibaüzenet jelenik meg.

3. Ellenkező esetben találja az adatkészlet a pozíciók a rendellenességek észlelését. A válasz `isAnomaly` mező arra, hogy egy adott adatpontot anomáliát vonatkozó logikai értéket tartalmaz. A JSON-tömböt beszerzése és végigléptetni, az index bármelyik nyomtatási `true` értékeket. Ha találhatók esetleges rendellenes adatpontok index ezeket az értékeket felelnek meg.

```java
static void detectAnomaliesBatch(String requestData) {
    System.out.println("Detecting anomalies as a batch");
    String result = sendRequest(batchDetectionUrl, endpoint, subscriptionKey, requestData);
    if (result != null) {
        System.out.println(result);
        JSONObject jsonObj = new JSONObject(result);
        if (jsonObj.has("code")) {
            System.out.println(String.format("Detection failed. ErrorCode:%s, ErrorMessage:%s", jsonObj.getString("code"), jsonObj.getString("message")));
        } else {
            JSONArray jsonArray = jsonObj.getJSONArray("isAnomaly");
            System.out.println("Anomalies found in the following data positions:");
            for (int i = 0; i < jsonArray.length(); ++i) {
                if (jsonArray.getBoolean(i))
                    System.out.print(i + ", ");
            }
            System.out.println();
        }
    }
}
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>A legújabb adatpont anomáliadetektálási állapotának észlelése

* Hozzon létre egy meghívott metódus `detectAnomaliesLatest()` az adatkészlet az utolsó adatpont anomáliadetektálási állapotának észleléséhez. Hívja a `sendRequest()` metódus a végpont URL-címe, előfizetési kulcs és json-adatok a fent létrehozott. Az eredményt kapja, és nyomtassa ki a konzolhoz.

```java
static void detectAnomaliesLatest(String requestData) {
    System.out.println("Determining if latest data point is an anomaly");
    String result = sendRequest(latestPointDetectionUrl, endpoint, subscriptionKey, requestData);
    System.out.println(result);
}
```

## <a name="load-your-time-series-data-and-send-the-request"></a>Az idősorozat-adatok betöltése, és a kérés küldése

1. A fő metódus az alkalmazás olvassa el a kérelmeket a hozzáadni kívánt adatokat tartalmazó JSON-fájlt.

2. Hívja meg a fent létrehozott két anomáliadetektálási észlelési funkciók.

```java
public static void main(String[] args) throws Exception {
    String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "utf-8");
    detectAnomaliesBatch(requestData);
    detectAnomaliesLatest(requestData);
}
```

### <a name="example-response"></a>Példaválasz

A sikeres válasz JSON formátumban. A JSON-válasz megtekintése a Githubon, az alábbi hivatkozásokra kattintva:
* [A batch észlelési példaválasz](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Legújabb pont észlelési példaválasz](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [REST API – referencia](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)