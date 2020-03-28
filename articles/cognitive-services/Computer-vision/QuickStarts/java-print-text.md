---
title: 'Rövid útmutató: Nyomtatott szöveg kinyerése - REST, Java'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban nyomtatott szöveget fog kinyerni egy képből a Computer Vision API Javával való használatával.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: b976002ee185a87daedf37a93f3d02a166eb6f8a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974597"
---
# <a name="quickstart-extract-printed-text-ocr-using-the-computer-vision-rest-api-and-java"></a>Rövid útmutató: Nyomtatott szöveg (OCR) kinyerése a Computer Vision REST API és a Java használatával

> [!NOTE]
> Ha angol nyelvű szöveget használ, fontolja meg az új [Olvasás művelet használatát.](https://docs.microsoft.com/azure/cognitive-services/computer-vision/concept-recognizing-text) [Java-gyorsútmutató](https://docs.microsoft.com/azure/cognitive-services/computer-vision/quickstarts/java-hand-text) is elérhető. 

Ebben a rövid útmutatóban a számítógép-látás rest API-val optikai karakterfelismeréssel (OCR) rendelkező nyomtatott szöveget nyerhet ki egy képből. Az [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) módszerrel észlelheti a kép nyomtatott szövegét, és kinyerheti a felismert karaktereket egy gép által használható karakterfolyamba.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

- A gépén telepítve kell lennie a [Java&trade;-platformhoz készült Standard Edition Development Kit 7-es vagy 8-as verziójának](https://aka.ms/azure-jdks) (JDK 7 vagy 8).
- Szüksége lesz egy Computer Vision-előfizetői azonosítóra. Ingyenes próbakulcsot a [Cognitive Services kipróbálásával](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision)szerezheti be. Vagy kövesse a [Cognitive Services-fiók létrehozása](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) című részben található utasításokat, hogy előiratkozzon a Computer Vision szolgáltatásra, és bekésezse a kulcsot. Ezután [hozzon létre környezeti változókat](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) a `COMPUTER_VISION_SUBSCRIPTION_KEY` kulcs- és szolgáltatásvégpont-karakterlánchoz, amelyet elnevezett, illetve `COMPUTER_VISION_ENDPOINT`a.

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

1. Cserélje `Main` le a nyilvános osztályt a következő kódra.
1. Ha szeretné, cserélje le az `imageToAnalyze` értéket egy másik olyan kép URL-címére, amelyből nyomtatott szöveget szeretne kinyerni.
1. Mentse a változtatásokat, majd hozza létre a Java-projektet.
1. Ha IDE-t használ, futtassa a `Main` metódust. Egyéb esetben nyisson meg egy parancssori ablakot, majd a `java` paranccsal futtassa a lefordított osztályt. Például: `java Main`.

```java
public class Main {
    // **********************************************
    // *** Update or verify the following values. ***
    // **********************************************

    // Add your Computer Vision subscription key and endpoint to your environment variables.
    // After setting, close and then re-open your command shell or project for the changes to take effect.
    String subscriptionKey = System.getenv("COMPUTER_VISION_SUBSCRIPTION_KEY");
    String endpoint = ("COMPUTER_VISION_ENDPOINT");

    private static final String uriBase = endpoint + 
            "vision/v2.1/ocr";

    private static final String imageToAnalyze =
        "https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/" +
            "Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png";

    public static void main(String[] args) {
        CloseableHttpClient httpClient = HttpClientBuilder.create().build();

        try {
            URIBuilder uriBuilder = new URIBuilder(uriBase);

            uriBuilder.setParameter("language", "unk");
            uriBuilder.setParameter("detectOrientation", "true");

            // Request parameters.
            URI uri = uriBuilder.build();
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
  "orientation": "Up",
  "regions": [{
    "boundingBox": "21,16,304,451",
    "lines": [
      {
        "boundingBox": "28,16,288,41",
        "words": [{
          "boundingBox": "28,16,288,41",
          "text": "NOTHING"
        }]
      },
      {
        "boundingBox": "27,66,283,52",
        "words": [{
          "boundingBox": "27,66,283,52",
          "text": "EXISTS"
        }]
      },
      {
        "boundingBox": "27,128,292,49",
        "words": [{
          "boundingBox": "27,128,292,49",
          "text": "EXCEPT"
        }]
      },
      {
        "boundingBox": "24,188,292,54",
        "words": [{
          "boundingBox": "24,188,292,54",
          "text": "ATOMS"
        }]
      },
      {
        "boundingBox": "22,253,297,32",
        "words": [
          {
            "boundingBox": "22,253,105,32",
            "text": "AND"
          },
          {
            "boundingBox": "144,253,175,32",
            "text": "EMPTY"
          }
        ]
      },
      {
        "boundingBox": "21,298,304,60",
        "words": [{
          "boundingBox": "21,298,304,60",
          "text": "SPACE."
        }]
      },
      {
        "boundingBox": "26,387,294,37",
        "words": [
          {
            "boundingBox": "26,387,210,37",
            "text": "Everything"
          },
          {
            "boundingBox": "249,389,71,27",
            "text": "else"
          }
        ]
      },
      {
        "boundingBox": "127,431,198,36",
        "words": [
          {
            "boundingBox": "127,431,31,29",
            "text": "is"
          },
          {
            "boundingBox": "172,431,153,36",
            "text": "opinion."
          }
        ]
      }
    ]
  }],
  "textAngle": 0,
  "language": "en"
}
```

## <a name="next-steps"></a>További lépések

Ismerjen meg egy Java Swing-alkalmazást, amely a Computer Vision segítségével végez optikai karakterfelismerést (OCR), és amellyel intelligens körbevágású miniatűröket hozhat létre, valamint képek vizuális jellemzőit, például arcokat észlelhet, kategorizálhat, címkézhet és írhat le. A Computer Vision API-val való gyors kísérletezéshez próbálja ki az [Open API-tesztkonzolt](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Computer Vision API – Java-oktatóanyag](../Tutorials/java-tutorial.md)
