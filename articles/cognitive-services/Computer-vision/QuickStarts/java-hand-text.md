---
title: 'Gyors útmutató: Kézzel írt szöveg - REST, Java kinyerése'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban kézzel írt szöveget fog kinyerni egy képből a Computer Vision Javával történő használatával.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: fb039f58a9a13cf3241c50d6cf31e777a654dcdb
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57435150"
---
# <a name="quickstart-extract-handwritten-text-using-the-rest-api-and-java-in-computer-vision"></a>Gyors útmutató: A REST API-t és a Java használatával a Computer Vision kézzel írt szöveg kinyerése

Ebben a rövid útmutatóban kézzel írt szöveget fog kinyerni egy képből a Computer Vision REST API-jának használatával. Az a [kötegelt olvasási](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) API és a [olvasási művelet eredményének](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/5be108e7498a4f9ed20bf96d) API-t, elvégezheti a szöveg felismerése képekből a képet, majd a kivonat felismert karakterek egy számítógép felhasználható karakter streambe.

> [!IMPORTANT]
> Ellentétben a [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) metódus, a [kötegelt olvasási](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) metódus aszinkron módon fut. Ez a metódus nem adja vissza információt a sikeres válaszok törzsében. Ehelyett a kötegelt olvasási metódus adja vissza egy URI értékét a `Operation-Content` válasz fejléce mező. Ezt követően meghívhatja az URI, amely a [olvasási művelet eredményének](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/5be108e7498a4f9ed20bf96d) módszer annak érdekében, hogy ellenőrizze az állapotát, és a kötegelt olvasási metódus meghívása eredményét adja vissza.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

- A gépén telepítve kell lennie a [Java&trade;-platformhoz készült Standard Edition Development Kit 7-es vagy 8-as verziójának](https://aka.ms/azure-jdks) (JDK 7 vagy 8).
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
   import org.apache.http.client.methods.HttpGet;
   import org.apache.http.client.methods.HttpPost;
   import org.apache.http.client.utils.URIBuilder;
   import org.apache.http.entity.StringEntity;
   import org.apache.http.impl.client.CloseableHttpClient;
   import org.apache.http.impl.client.HttpClientBuilder;
   import org.apache.http.util.EntityUtils;
   import org.apache.http.Header;
   import org.json.JSONObject;
   ```

1. Cserélje le a `Main` nyilvános osztály teljes tartalmát az alábbi kódra, majd hajtsa végre a következő változtatásokat, ahol szükséges:
   1. Cserélje le a `subscriptionKey` értéket az előfizetői azonosítóra.
   1. Értékét cserélje `uriBase` a végpont URL-CÍMÉT a [kötegelt olvasási](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) metódus, ahol szerezte be az előfizetési kulcsok szükség esetén az Azure-régiókba.
   1. Ha szeretné, cserélje le az `imageToAnalyze` értéket egy másik olyan kép URL-címére, amelyből kézzel írt szöveget szeretne kinyerni.
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
    // Free trial subscription keys are generated in the "westus" region.
    // If you use a free trial subscription key, you shouldn't need to change
    // this region.
    private static final String uriBase =
        "https://westus.api.cognitive.microsoft.com/vision/v2.0/read/core/asyncBatchAnalyze";

    private static final String imageToAnalyze =
        "https://upload.wikimedia.org/wikipedia/commons/thumb/d/dd/" +
        "Cursive_Writing_on_Notebook_paper.jpg/800px-Cursive_Writing_on_Notebook_paper.jpg";

    public static void main(String[] args) {
        CloseableHttpClient httpTextClient = HttpClientBuilder.create().build();
        CloseableHttpClient httpResultClient = HttpClientBuilder.create().build();;

        try {
            // This operation requires two REST API calls. One to submit the image
            // for processing, the other to retrieve the text found in the image.

            URIBuilder builder = new URIBuilder(uriBase);

            // Request parameter.
            builder.setParameter("mode", "Handwritten");

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

            // Two REST API methods are required to extract handwritten text.
            // One method to submit the image for processing, the other method
            // to retrieve the text found in the image.

            // Call the first REST API method to detect the text.
            HttpResponse response = httpTextClient.execute(request);

            // Check for success.
            if (response.getStatusLine().getStatusCode() != 202) {
                // Format and display the JSON error message.
                HttpEntity entity = response.getEntity();
                String jsonString = EntityUtils.toString(entity);
                JSONObject json = new JSONObject(jsonString);
                System.out.println("Error:\n");
                System.out.println(json.toString(2));
                return;
            }

            // Store the URI of the second REST API method.
            // This URI is where you can get the results of the first REST API method.
            String operationLocation = null;

            // The 'Operation-Location' response header value contains the URI for
            // the second REST API method.
            Header[] responseHeaders = response.getAllHeaders();
            for (Header header : responseHeaders) {
                if (header.getName().equals("Operation-Location")) {
                    operationLocation = header.getValue();
                    break;
                }
            }

            if (operationLocation == null) {
                System.out.println("\nError retrieving Operation-Location.\nExiting.");
                System.exit(1);
            }

            // If the first REST API method completes successfully, the second
            // REST API method retrieves the text written in the image.
            //
            // Note: The response may not be immediately available. Handwriting
            // recognition is an asynchronous operation that can take a variable
            // amount of time depending on the length of the handwritten text.
            // You may need to wait or retry this operation.

            System.out.println("\nHandwritten text submitted.\n" +
                    "Waiting 10 seconds to retrieve the recognized text.\n");
            Thread.sleep(10000);

            // Call the second REST API method and get the response.
            HttpGet resultRequest = new HttpGet(operationLocation);
            resultRequest.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

            HttpResponse resultResponse = httpResultClient.execute(resultRequest);
            HttpEntity responseEntity = resultResponse.getEntity();

            if (responseEntity != null) {
                // Format and display the JSON response.
                String jsonString = EntityUtils.toString(responseEntity);
                JSONObject json = new JSONObject(jsonString);
                System.out.println("Text recognition result response: \n");
                System.out.println(json.toString(2));
            }
        } catch (Exception e) {
            System.out.println(e.getMessage());
        }
    }
}
```

## <a name="examine-the-response"></a>A válasz vizsgálata

A rendszer JSON formátumban adja vissza a sikeres választ. A mintaalkalmazás elemzi és megjeleníti a sikeres választ a konzolablakban, a következő példához hasonló módon:

```json
Handwritten text submitted. Waiting 10 seconds to retrieve the recognized text.

Text recognition result response:

{
  "status": "Succeeded",
  "recognitionResults": [
    {
      "page": 1,
      "clockwiseOrientation": 349.59,
      "width": 3200,
      "height": 3200,
      "unit": "pixel",
      "lines": [
        {
          "boundingBox": [202,618,2047,643,2046,840,200,813],
          "text": "Our greatest glory is not",
          "words": [
            {
              "boundingBox": [204,627,481,628,481,830,204,829],
              "text": "Our"
            },
            {
              "boundingBox": [519,628,1057,630,1057,832,518,830],
              "text": "greatest"
            },
            {
              "boundingBox": [1114,630,1549,631,1548,833,1114,832],
              "text": "glory"
            },
            {
              "boundingBox": [1586,631,1785,632,1784,834,1586,833],
              "text": "is"
            },
            {
              "boundingBox": [1822,632,2115,633,2115,835,1822,834],
              "text": "not"
            }
          ]
        },
        {
          "boundingBox": [420,1273,2954,1250,2958,1488,422,1511],
          "text": "but in rising every time we fall",
          "words": [
            {
              "boundingBox": [423,1269,634,1268,635,1507,424,1508],
              "text": "but"
            },
            {
              "boundingBox": [667,1268,808,1268,809,1506,668,1507],
              "text": "in"
            },
            {
              "boundingBox": [874,1267,1289,1265,1290,1504,875,1506],
              "text": "rising"
            },
            {
              "boundingBox": [1331,1265,1771,1263,1772,1502,1332,1504],
              "text": "every"
            },
            {
              "boundingBox": [1812, 1263, 2178, 1261, 2179, 1500, 1813, 1502],
              "text": "time"
            },
            {
              "boundingBox": [2219, 1261, 2510, 1260, 2511, 1498, 2220, 1500],
              "text": "we"
            },
            {
              "boundingBox": [2551, 1260, 3016, 1258, 3017, 1496, 2552, 1498],
              "text": "fall"
            }
          ]
        },
        {
          "boundingBox": [1612, 903, 2744, 935, 2738, 1139, 1607, 1107],
          "text": "in never failing ,",
          "words": [
            {
              "boundingBox": [1611, 934, 1707, 933, 1708, 1147, 1613, 1147],
              "text": "in"
            },
            {
              "boundingBox": [1753, 933, 2132, 930, 2133, 1144, 1754, 1146],
              "text": "never"
            },
            {
              "boundingBox": [2162, 930, 2673, 927, 2674, 1140, 2164, 1144],
              "text": "failing"
            },
            {
              "boundingBox": [2703, 926, 2788, 926, 2790, 1139, 2705, 1140],
              "text": ",",
              "confidence": "Low"
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a Java-projektet a lefordított osztállyal és az importált kódtárakkal együtt.

## <a name="next-steps"></a>További lépések

Ismerjen meg egy Java Swing-alkalmazást, amely a Computer Vision segítségével végez optikai karakterfelismerést (OCR), és amellyel intelligens körbevágású miniatűröket hozhat létre, valamint képek vizuális jellemzőit, például arcokat észlelhet, kategorizálhat, címkézhet és írhat le. A Computer Vision API-val való gyors kísérletezéshez próbálja ki az [Open API-tesztkonzolt](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Computer Vision API – Java-oktatóanyag](../Tutorials/java-tutorial.md)
