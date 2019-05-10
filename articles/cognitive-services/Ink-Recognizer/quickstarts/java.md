---
title: 'Gyors útmutató: Ismeri fel a digitális ink a szabadkézi felismerő REST API-t és a Java'
description: A tinta felismerő API használatával indítsa el a digitális tollvonások felismerve.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: article
ms.date: 05/02/2019
ms.author: aahi
ms.openlocfilehash: 6237253922544dc47bb11aec4dd58139f99eb0da
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2019
ms.locfileid: "65518615"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-java"></a>Gyors útmutató: Ismeri fel a digitális ink a szabadkézi felismerő REST API-t és a Java

Ez a rövid útmutató segítségével a digitális tollvonások a szabadkézi felismerő API használatának megkezdéséhez. A Java-alkalmazás egy JSON-formátumú ink körvonal adatokat tartalmazó API-kérést küld, és lekéri a válaszban.

Ezt az alkalmazást a Java nyelven van megírva, míg a API-ját egy REST-alapú webszolgáltatás szinte bármelyik programozási nyelvével kompatibilis.

Általában az API-t kellene hívása egy digitális szabadkézi alkalmazásból. Ez a rövid útmutató a következő kézzel írt mintát ink körvonal adatokat küld a JSON-fájlból.

![írt szöveg felismerése képekből képe](../media/handwriting-sample.jpg)

Ez a rövid útmutató forráskódja találhatók [GitHub](https://go.microsoft.com/fwlink/?linkid=2089904).

## <a name="prerequisites"></a>Előfeltételek

- A [Java&trade; fejlesztési Kit(JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) vagy újabb.

- Ezek a kódtárak importálja a Maven tárházból
    - [A Java JSON](https://mvnrepository.com/artifact/org.json/json) csomag
    - [Az Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) csomag

- Ez a rövid útmutató példa ink körvonal adatai találhatók [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-ink-strokes.json).

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

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
    import java.io.IOException;
    import java.nio.file.Files;
    import java.nio.file.Paths;
    ```

2. Változók létrehozása az előfizetési kulcs és a végpontot. Alább az URI-t is használhatja az ink-felismerés van. A szolgáltatás-végpontra később az API-kérelem URL-Címének létrehozására lesz hozzáfűzve.

    ```java
    // Replace the subscriptionKey string value with your valid subscription key.
    static final String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
    // Replace the dataPath string with a path to the JSON formatted ink stroke data file.
    static final String dataPath = "PATH_TO_INK_STROKE_DATA";
    
    static final String endpoint = "https://api.cognitive.microsoft.com";
    static final String inkRecognitionUrl = "/inkrecognizer/v1.0-preview/recognize";
    ```

## <a name="create-a-function-to-send-requests"></a>Kérelmek küldése függvény létrehozása

1. Hozzon létre egy új függvényt nevű `sendRequest()` átkerül a fent létrehozott változókat. Hajtsa végre az alábbi lépéseket.

2. Hozzon létre egy `CloseableHttpClient` objektum, amely kéréseket küldhetnek az API-t. A kérelem küldése egy `HttpPut` támogatásikérelem-objektum a végponthoz, és a szabadkézi felismerő URL-cím kombinálásával.

3. Használja a kérelem `setHeader()` függvény beállítása a `Content-Type` fejlécet `application/json`, és adja hozzá az előfizetési kulcs, a `Ocp-Apim-Subscription-Key` fejléc.

4. A kérelem használata `setEntity()` függvényt, hogy az adatok küldésének.   

5. Az ügyfél használata `execute()` függvényt a kérelem elküldéséhez, és mentse azt egy `CloseableHttpResponse` objektum. 

6. Hozzon létre egy `HttpEntity` objektum a válasz tartalom tárolásához. A tartalom lekérése `getEntity()`. Ha a válasz nem üres, adja vissza.
    
    ```java
    static String sendRequest(String apiAddress, String endpoint, String subscriptionKey, String requestData) {
        try (CloseableHttpClient client = HttpClients.createDefault()) {
            HttpPut request = new HttpPut(endpoint + apiAddress);
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
            System.err.println("Exception recognizing ink: " + ex.getMessage());
            ex.printStackTrace();
        }
        return null;
    }
    ```

## <a name="send-an-ink-recognition-request"></a>-Ink felismerés kérés küldése

Hozzon létre egy meghívott metódus `recognizeInk()` felismerni a szabadkézi körvonal adatait. Hívja a `sendRequest()` metódus a végpont URL-címe, előfizetési kulcs és json-adatok a fent létrehozott. Az eredményt kapja, és nyomtassa ki a konzolhoz.

```java
static void recognizeInk(String requestData) {
    System.out.println("Sending an ink recognition request");
    String result = sendRequest(inkRecognitionUrl, endpoint, subscriptionKey, requestData);
    System.out.println(result);
}
```

## <a name="load-your-digital-ink-data-and-send-the-request"></a>A digitális ink-adatok betöltése és a kérés küldése

1. A fő metódus az alkalmazás olvassa el a kérelmeket a hozzáadni kívánt adatokat tartalmazó JSON-fájlt.

2. A fent létrehozott ink felismerés függvény hívása.
    
    ```java
    public static void main(String[] args) throws Exception {
        String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "UTF-8");
        recognizeInk(requestData);
    }
    ```

## <a name="run-the-application-and-view-the-response"></a>Futtassa az alkalmazást, és tekintse meg a választ

Futtassa az alkalmazást. A sikeres válasz JSON formátumban. A JSON-válasz is megtalálhatja a [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-response.json).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [REST API – referencia](https://go.microsoft.com/fwlink/?linkid=2089907)


A tinta Recognition API működését egy digitális szabadkézi alkalmazás megtekintéséhez tekintse meg a következő minta alkalmazásokat a Githubon:
* [C# és Univerzális Windows-platform (Universal Windows Platform, UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# és Windows megjelenítési alaprendszer (Windows Presentation Foundation, WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [JavaScript-webböngészőalkalmazás](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java- és Android-mobilalkalmazás](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift- és iOS-mobilalkalmazás](https://go.microsoft.com/fwlink/?linkid=2089805)
