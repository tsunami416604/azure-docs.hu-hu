---
title: 'Rövid útmutató: Távoli kép elemzése – REST, Java – Computer Vision'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban egy képet fog elemezni a Computer Vision API és a Java segítségével.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 901fb2c592d78bf26e36e0ecd0417ee995bc5771
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2018
ms.locfileid: "45631089"
---
# <a name="quickstart-analyze-a-remote-image-using-the-rest-api-and-java-in-computer-vision"></a>Rövid útmutató: Távoli kép elemzése a REST API és a Java használatával a Computer Visionben

Ebben a rövid útmutatóban egy távol tárolt képet fog elemezni vizuális jellemzők kinyeréséhez a Computer Vision REST API-jával. Az [Analyze Image metódussal](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) vizuális jellemzőket nyerhet ki a képek tartalma alapján.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

- A gépén telepítve kell lennie a [Java&trade;-platformhoz készült Standard Edition Development Kit 7-es vagy 8-as verziójának](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) (JDK 7 vagy 8).
- Szüksége lesz egy Computer Vision-előfizetői azonosítóra. Az előfizetői azonosító beszerzéséhez lásd az [előfizetői azonosítók beszerzéséről](../Vision-API-How-to-Topics/HowToSubscribe.md) szóló témakört.

## <a name="create-and-run-the-sample-application"></a>A mintaalkalmazás létrehozása és futtatása

A minta létrehozásához és futtatásához az alábbi lépéseket kell végrehajtania:

1. Hozzon létre egy új Java-projektet a kedvenc IDE-környezetében vagy szerkesztőjében. Ha lehetséges, a Java-projektet egy parancssori alkalmazás sablonjából hozza létre.
1. Importálja az alábbi kódtárakat a Java-projektbe. Ha Mavent használ, a Maven-koordináták mindegyik kódtár esetében meg vannak adva.
   - [Apache HTTP-ügyfél](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpclient:4.5.5)
   - [Apache HTTP-mag](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpcore:4.4.9)
   - [JSON-kódtár](https://github.com/stleary/JSON-java) (org.json:json:20180130)
1. Adja hozzá az alábbi `import` utasításokat ahhoz a fájlhoz, amely a projekt `Main` nyilvános osztályát tartalmazza.  

   ```java
   import java.net.URI;
   import org.apache.http.HttpEntity;
   import org.apache.http.HttpResponse;
   import org.apache.http.client.methods.HttpPost;
   import org.apache.http.entity.StringEntity;
   import org.apache.http.client.utils.URIBuilder;
   import org.apache.http.impl.client.CloseableHttpClient;
   import org.apache.http.impl.client.HttpClientBuilder;
   import org.apache.http.util.EntityUtils;
   import org.json.JSONObject;
   ```

1. Cserélje le a `Main` nyilvános osztály teljes tartalmát az alábbi kódra, majd hajtsa végre a következő változtatásokat, ahol szükséges:
   1. Cserélje le a `subscriptionKey` értéket az előfizetői azonosítóra.
   1. Ha szükséges, cserélje le az `uriBase` értéket azon Azure-régió [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) metódusának végponti URL-címére, ahol az előfizetői azonosítókat beszerezte.
   1. Ha szeretné, cserélje le az `imageToAnalyze` értéket egy másik elemzendő kép URL-címére.
1. Mentse a változtatásokat, majd hozza létre a Java-projektet.
1. Ha IDE-t használ, futtassa a `Main` metódust. Egyéb esetben nyisson meg egy parancssori ablakot, majd a `java` paranccsal futtassa a lefordított osztályt. Például: `java Main`.

```java
public class Main {
    // **********************************************
    // *** Update or verify the following values. ***
    // **********************************************

    // Replace <Subscription Key> with your valid subscription key.
    private static final String subscriptionKey = "<Subscription Key>";

    // You must use the same Azure region in your REST API method as you used to
    // get your subscription keys. For example, if you got your subscription keys
    // from the West US region, replace "westcentralus" in the URL
    // below with "westus".
    //
    // Free trial subscription keys are generated in the West Central US region.
    // If you use a free trial subscription key, you shouldn't need to change
    // this region.
    private static final String uriBase =
            "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze";

    private static final String imageToAnalyze =
            "https://upload.wikimedia.org/wikipedia/commons/" +
                    "1/12/Broadway_and_Times_Square_by_night.jpg";

    public static void main(String[] args) {
        CloseableHttpClient httpClient = HttpClientBuilder.create().build();

        try {
            URIBuilder builder = new URIBuilder(uriBase);

            // Request parameters. All of them are optional.
            builder.setParameter("visualFeatures", "Categories,Description,Color");
            builder.setParameter("language", "en");

            // Prepare the URI for the REST API method.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

            // Request body.
            StringEntity requestEntity =
                    new StringEntity("{\"url\":\"" + imageToAnalyze + "\"}");
            request.setEntity(requestEntity);

            // Call the REST API method and get the response entity.
            HttpResponse response = httpClient.execute(request);
            HttpEntity entity = response.getEntity();

            if (entity != null) {
                // Format and display the JSON response.
                String jsonString = EntityUtils.toString(entity);
                JSONObject json = new JSONObject(jsonString);
                System.out.println("REST Response:\n");
                System.out.println(json.toString(2));
            }
        } catch (Exception e) {
            // Display error message.
            System.out.println(e.getMessage());
        }
    }
}
```

## <a name="examine-the-response"></a>A válasz vizsgálata

A rendszer JSON formátumban adja vissza a sikeres választ. A mintaalkalmazás elemzi és megjeleníti a sikeres választ a konzolablakban, a következő példához hasonló módon:

```json
REST Response:

{
  "metadata": {
    "width": 1826,
    "format": "Jpeg",
    "height": 2436
  },
  "color": {
    "dominantColorForeground": "Brown",
    "isBWImg": false,
    "accentColor": "B74314",
    "dominantColorBackground": "Brown",
    "dominantColors": ["Brown"]
  },
  "requestId": "bbffe1a1-4fa3-4a6b-a4d5-a4964c58a811",
  "description": {
    "captions": [{
      "confidence": 0.8241405091548035,
      "text": "a group of people on a city street filled with traffic at night"
    }],
    "tags": [
      "outdoor",
      "building",
      "street",
      "city",
      "busy",
      "people",
      "filled",
      "traffic",
      "many",
      "table",
      "car",
      "group",
      "walking",
      "bunch",
      "crowded",
      "large",
      "night",
      "light",
      "standing",
      "man",
      "tall",
      "umbrella",
      "riding",
      "sign",
      "crowd"
    ]
  },
  "categories": [{
    "score": 0.625,
    "name": "outdoor_street"
  }]
}
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a Java-projektet a lefordított osztállyal és az importált kódtárakkal együtt.

## <a name="next-steps"></a>További lépések

Ismerjen meg egy Java Swing-alkalmazást, amely a Computer Vision segítségével végez optikai karakterfelismerést (OCR), és amellyel intelligens körbevágású miniatűröket hozhat létre, valamint képek vizuális jellemzőit, például arcokat észlelhet, kategorizálhat, címkézhet és írhat le. A Computer Vision API-val való gyors kísérletezéshez próbálja ki az [Open API-tesztkonzolt](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Computer Vision API – Java-oktatóanyag](../Tutorials/java-tutorial.md)
