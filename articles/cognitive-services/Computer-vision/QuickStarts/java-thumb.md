---
title: 'Rövid útmutató: Miniatűr létrehozása – REST, Java – Computer Vision'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban miniatűrt hozhat létre egy képből a Computer Vision API és Java használatával.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: pafarley
ms.openlocfilehash: a29fd161e788a0c10e5fcb5cd5cd735e1f2cf1b4
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2018
ms.locfileid: "49343577"
---
# <a name="quickstart-generate-a-thumbnail-using-the-rest-api-and-java-in-computer-vision"></a>Rövid útmutató: Miniatűr létrehozása a REST API és Java használatával a Computer Visionben

Ebben a rövid útmutatóban miniatűrt hozhat létre egy képből a Computer Vision REST API-jának segítségével. A [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) metódussal létrehozhatja egy kép miniatűrjét. Megadhatja a magasságát és a szélességét, amely eltérhet a bemeneti kép oldalarányától. A Computer Vision intelligens körbevágással azonosítja a releváns régiót, amely alapján létrehozza a vágási koordinátákat.

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
   import java.awt.*;
   import javax.swing.*;
   import java.net.URI;
   import java.io.InputStream;
   import javax.imageio.ImageIO;
   import java.awt.image.BufferedImage;
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
   1. Ha szükséges, cserélje le az `uriBase` értéket azon Azure-régió [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) metódusának végponti URL-címére, ahol az előfizetői azonosítókat beszerezte.
   1. Ha szeretné, cserélje le az `imageToAnalyze` értéket annak a képnek az URL-címére, amelyhez miniatűrt szeretne létrehozni.
1. Mentse a változtatásokat, majd hozza létre a Java-projektet.
1. Ha IDE-t használ, futtassa a `Main` metódust. Egyéb esetben nyisson meg egy parancssori ablakot, majd a `java` paranccsal futtassa a lefordított osztályt. Például: `java Main`.

```java
// This sample uses the following libraries:
//  - Apache HTTP client (org.apache.httpcomponents:httpclient:4.5.5)
//  - Apache HTTP core (org.apache.httpcomponents:httpccore:4.4.9)
//  - JSON library (org.json:json:20180130).


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
        "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/generateThumbnail";

    private static final String imageToAnalyze =
        "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg";

    public static void main(String[] args) {
        CloseableHttpClient httpClient = HttpClientBuilder.create().build();

        try {
            URIBuilder uriBuilder = new URIBuilder(uriBase);

            // Request parameters.
            uriBuilder.setParameter("width", "100");
            uriBuilder.setParameter("height", "150");
            uriBuilder.setParameter("smartCropping", "true");

            // Prepare the URI for the REST API method.
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

            // Check for success.
            if (response.getStatusLine().getStatusCode() == 200) {
                // Display the thumbnail.
                System.out.println("\nDisplaying thumbnail.\n");
                displayImage(entity.getContent());
            } else {
                // Format and display the JSON error message.
                String jsonString = EntityUtils.toString(entity);
                JSONObject json = new JSONObject(jsonString);
                System.out.println("Error:\n");
                System.out.println(json.toString(2));
            }
        } catch (Exception e) {
            System.out.println(e.getMessage());
        }
    }

    // Displays the given input stream as an image.
    private static void displayImage(InputStream inputStream) {
        try {
            BufferedImage bufferedImage = ImageIO.read(inputStream);

            ImageIcon imageIcon = new ImageIcon(bufferedImage);

            JLabel jLabel = new JLabel();
            jLabel.setIcon(imageIcon);

            JFrame jFrame = new JFrame();
            jFrame.setLayout(new FlowLayout());
            jFrame.setSize(100, 150);

            jFrame.add(jLabel);
            jFrame.setVisible(true);
            jFrame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        } catch (Exception e) {
            System.out.println(e.getMessage());
        }
    }
}
```

## <a name="examine-the-response"></a>A válasz vizsgálata

A rendszer bináris formátumban adja vissza a sikeres választ, ahol a bináris adatok a miniatűr képadatainak felelnek meg. Sikeres kérés esetén a miniatűr a válaszban levő bináris adatokból lesz összeállítva, és a mintaalkalmazás által létrehozott külön ablakban jelenik meg. Ha a kérés meghiúsul, a válasz a konzolablakban jelenik meg. A meghiúsult kérés válasza tartalmaz egy hibakódot és egy üzenetet, amely segít meghatározni a hiba okát.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a Java-projektet a lefordított osztállyal és az importált kódtárakkal együtt.

## <a name="next-steps"></a>További lépések

Ismerjen meg egy Java Swing-alkalmazást, amely a Computer Vision segítségével végez optikai karakterfelismerést (OCR), és amellyel intelligens körbevágású miniatűröket hozhat létre, valamint képek vizuális jellemzőit, például arcokat észlelhet, kategorizálhat, címkézhet és írhat le. A Computer Vision API-val való gyors kísérletezéshez próbálja ki az [Open API-tesztkonzolt](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Computer Vision API – Java-oktatóanyag](../Tutorials/java-tutorial.md)
