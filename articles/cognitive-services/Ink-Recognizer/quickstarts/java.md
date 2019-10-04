---
title: 'Gyors útmutató: Digitális tinta felismerése a kézírás-felismerővel REST API és Java'
titleSuffix: Azure Cognitive Services
description: A tinta-felismerő API használatával megkezdheti a digitális tollvonások felismerését.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 09/23/2019
ms.author: aahi
ms.openlocfilehash: 36ff0fe4550b140a722ed25f4e372f7c88581211
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212682"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-java"></a>Gyors útmutató: Digitális tinta felismerése a kézírás-felismerővel REST API és Java

Ezzel a rövid útmutatóval megkezdheti a tinta-felismerő API használatát a digitális tollvonásokon. Ez a Java-alkalmazás egy olyan API-kérést küld, amely JSON formátumú szabadkézi adatokat tartalmaz, és lekéri a választ.

Habár ez az alkalmazás Java nyelven íródott, az API egy REST-alapú webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel.

Általában az API-t egy Digital unking-alkalmazásból hívja meg. Ez a rövid útmutató egy JSON-fájlból a következő kézírásos minta adatait küldi el: tollvonási adatok.

![egy kézzel írott szöveg képe](../media/handwriting-sample.jpg)

Ennek a rövid útmutatónak a forráskódja a [githubon](https://go.microsoft.com/fwlink/?linkid=2089904)érhető el.

## <a name="prerequisites"></a>Előfeltételek

- A [Java&trade; Development Kit (JDK) 7-es](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) vagy újabb verziója.

- A kódtárak importálása a Maven adattárból
    - [JSON a Java-](https://mvnrepository.com/artifact/org.json/json) csomagban
    - [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) -csomag

- Ebben a rövid útmutatóban a jelen rövid útmutatóban szereplő tollvonási adatsorok a [githubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-ink-strokes.json)találhatók.

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

2. Hozzon létre változókat az előfizetési kulcshoz és a végponthoz. Cserélje le az alábbi végpontot a tinta-felismerő erőforráshoz generált egy elemre. Az API-hoz való kapcsolódáshoz fűzze hozzá a tinta felismerő URI-hoz.

    ```java
    // Replace the subscriptionKey string value with your valid subscription key.
    static final String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
    // Replace the dataPath string with a path to the JSON formatted ink stroke data file.
    static final String dataPath = "PATH_TO_INK_STROKE_DATA";
    
    static final String endpoint = "https://<your-custom-subdomain>.cognitiveservices.azure.com";
    static final String inkRecognitionUrl = "/inkrecognizer/v1.0-preview/recognize";
    ```

## <a name="create-a-function-to-send-requests"></a>Függvény létrehozása a kérelmek küldéséhez

1. Hozzon létre egy nevű `sendRequest()` új függvényt, amely a fent létrehozott változókat veszi igénybe. Ezután hajtsa végre a következő lépéseket.

2. Hozzon `CloseableHttpClient` létre egy objektumot, amely küldhet kérelmeket az API-nak. Küldje el a kérést `HttpPut` egy kérelem objektumnak a végpont és a kézírás-felismerő URL-címének kombinálásával.

3. A kérelem `setHeader()` funkciójának használatával állítsa be a `Content-Type` fejlécet `application/json`, és adja hozzá az előfizetési kulcsot `Ocp-Apim-Subscription-Key` a fejléchez.

4. Használja a kérelem `setEntity()` függvényét az elküldeni kívánt adathoz.   

5. A kérelem elküldéséhez használja az ügyfél `execute()` függvényét, és mentse egy `CloseableHttpResponse` objektumba. 

6. Hozzon `HttpEntity` létre egy objektumot a válasz tartalmának tárolására. Szerezze be a tartalmat `getEntity()`a-val. Ha a válasz nem üres, küldje vissza.
    
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

## <a name="send-an-ink-recognition-request"></a>Kézírás-felismerési kérelem küldése

Hozzon létre egy `recognizeInk()` nevű metódust a szabadkézi körvonal adatai felismeréséhez. Hívja meg `sendRequest()` a fent létrehozott metódust a végponttal, az URL-lel, az előfizetési kulccsal és a JSON-adataival. Szerezze be az eredményt, és nyomtassa ki a-konzolra.

```java
static void recognizeInk(String requestData) {
    System.out.println("Sending an ink recognition request");
    String result = sendRequest(inkRecognitionUrl, endpoint, subscriptionKey, requestData);
    System.out.println(result);
}
```

## <a name="load-your-digital-ink-data-and-send-the-request"></a>Töltse be a digitális tinta adatait, és küldje el a kérést

1. Az alkalmazás fő metódusában olvassa el a kérelmekbe felvenni kívánt adatmennyiséget tartalmazó JSON-fájlt.

2. Hívja meg a fent létrehozott Ink-felismerési függvényt.
    
    ```java
    public static void main(String[] args) throws Exception {
        String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "UTF-8");
        recognizeInk(requestData);
    }
    ```

## <a name="run-the-application-and-view-the-response"></a>Futtassa az alkalmazást, és tekintse meg a választ

Futtassa az alkalmazást. A sikeres válaszokat JSON formátumban adja vissza a rendszer. A JSON-választ is megtalálja a [githubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-response.json).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [REST API – referencia](https://go.microsoft.com/fwlink/?linkid=2089907)


Ha szeretné megtudni, hogyan működik a Ink-felismerési API egy digitális, a GitHubon futó alkalmazásban, tekintse meg az alábbi példákat a GitHubon:
* [C# és Univerzális Windows-platform (Universal Windows Platform, UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# és Windows megjelenítési alaprendszer (Windows Presentation Foundation, WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [JavaScript-webböngészőalkalmazás](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java- és Android-mobilalkalmazás](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift- és iOS-mobilalkalmazás](https://go.microsoft.com/fwlink/?linkid=2089805)
