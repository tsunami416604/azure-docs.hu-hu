---
title: 'Rövid útmutató: Arcok észlelése egy lemezképben az Azure REST API-val és a Java-val'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban az Azure Face REST API-t java-val fogja használni a lemezképek arcok észleléséhez.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 59c121f09858114cecfaeb45e257315d1ab92935
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81403426"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-rest-api-and-java"></a>Rövid útmutató: Arcfelismerés egy képen a REST API és a Java használatával

Ebben a rövid útmutatóban az Azure Face REST API-t java-val fogja használni a lemezképek emberi arcok észleléséhez.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené. 

## <a name="prerequisites"></a>Előfeltételek

- Face-előfizetési kulcs. Ingyenes próba-előfizetési kulcsot a [Cognitive Services kipróbálásával](https://azure.microsoft.com/try/cognitive-services/?api=face-api)szerezhetbe. Vagy kövesse a [Cognitive Services-fiók létrehozása](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) című részben található utasításokat, hogy előiratkozzon a Face szolgáltatásra, és levegye a kulcsot.
- Bármilyen Java IDE az Ön által választott.

## <a name="create-the-java-project"></a>Java-projekt létrehozása

1. Hozzon létre egy új parancssori Java alkalmazást az IDE-ben, és adjon hozzá egy **főosztályt** egy **fő** metódussal.
1. Importálja az alábbi kódtárakat a Java-projektbe. Ha Mavent használ, a Maven-koordináták mindegyik kódtár esetében meg vannak adva.
   - [Apache HTTP-ügyfél](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpclient:4.5.6)
   - [Apache HTTP-mag](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpcore:4.4.10)
   - [JSON-kódtár](https://github.com/stleary/JSON-java) (org.json:json:20180130)
   - [Apache Commons naplózása](https://commons.apache.org/proper/commons-logging/download_logging.cgi) (commons-logging:commons-logging:1.1.2)

## <a name="add-face-detection-code"></a>Arcfelismerő kód hozzáadása

Nyissa meg a projekt fő osztályát. Itt hozzáadja a képek betöltéséhez és az arcok észleléséhez szükséges kódot.

### <a name="import-packages"></a>Csomagok importálása

Adja hozzá `import` a következő állításokat a fájl tetejéhez.

```java
// This sample uses Apache HttpComponents:
// http://hc.apache.org/httpcomponents-core-ga/httpcore/apidocs/
// https://hc.apache.org/httpcomponents-client-ga/httpclient/apidocs/

import java.net.URI;
import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.StringEntity;
import org.apache.http.client.utils.URIBuilder;
import org.apache.http.impl.client.HttpClientBuilder;
import org.apache.http.util.EntityUtils;
import org.json.JSONArray;
import org.json.JSONObject;
```

### <a name="add-essential-fields"></a>Alapvető mezők hozzáadása

Cserélje le a **főosztályt** a következő kódra. Ezek az adatok határozzák meg, hogyan lehet csatlakozni a Face szolgáltatáshoz, és hol lehet beszerezni a bemeneti adatokat. Frissítenie kell a `subscriptionKey` mezőt az előfizetési kulcs értékével, és úgy kell módosítania a `uriBase` karakterláncot, hogy az a megfelelő végponti karakterláncot tartalmazza. Az `imageWithFaces` értéket egy másik képfájlra mutató elérési útra is állíthatja.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

A `faceAttributes` mező egyszerűen csak bizonyos típusú attribútumok listája. Meghatározza, hogy mely adatokat szeretné beolvasni az észlelt arcokról.

```Java
public class Main {
    // Replace <Subscription Key> with your valid subscription key.
    private static final String subscriptionKey = "<Subscription Key>";

    private static final String uriBase =
        "https://<My Endpoint String>.com/face/v1.0/detect";

    private static final String imageWithFaces =
        "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg\"}";

    private static final String faceAttributes =
        "age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise";
```

### <a name="call-the-face-detection-rest-api"></a>Hívja meg az arcfelismerő REST API-t

Adja hozzá a **fő** metódust a következő kóddal. Ez létrehoz egy REST-hívást a Face API-t a `faceAttributes` face információk észlelésére a távoli lemezképben (a karakterlánc határozza meg, hogy melyik arcattribútumokat kell beolvasni). Ezután a kimeneti adatokat egy JSON-karakterláncba írja.

```Java
    public static void main(String[] args) {
        HttpClient httpclient = HttpClientBuilder.create().build();

        try
        {
            URIBuilder builder = new URIBuilder(uriBase);

            // Request parameters. All of them are optional.
            builder.setParameter("returnFaceId", "true");
            builder.setParameter("returnFaceLandmarks", "false");
            builder.setParameter("returnFaceAttributes", faceAttributes);

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

            // Request body.
            StringEntity reqEntity = new StringEntity(imageWithFaces);
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();
```

### <a name="parse-the-json-response"></a>Elemezd a JSON-választ

Közvetlenül az előző kód alatt adja hozzá a következő blokkot, amely a visszaadott JSON-adatokat könnyebben olvasható formátumba konvertálja, mielőtt kinyomtatja a konzolra. Végül zárja le a try-catch blokkot, a **fő** módszert és a **fő** osztályt.

```Java
            if (entity != null)
            {
                // Format and display the JSON response.
                System.out.println("REST Response:\n");

                String jsonString = EntityUtils.toString(entity).trim();
                if (jsonString.charAt(0) == '[') {
                    JSONArray jsonArray = new JSONArray(jsonString);
                    System.out.println(jsonArray.toString(2));
                }
                else if (jsonString.charAt(0) == '{') {
                    JSONObject jsonObject = new JSONObject(jsonString);
                    System.out.println(jsonObject.toString(2));
                } else {
                    System.out.println(jsonString);
                }
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
        }
    }
}
```

## <a name="run-the-app"></a>Az alkalmazás futtatása

Fordítsa le a kódot és futtassa. A sikeres válasz a Face adatokat könnyen olvasható JSON formátumban jeleníti meg a konzolablakban. Például:

```json
[{
  "faceRectangle": {
    "top": 131,
    "left": 177,
    "width": 162,
    "height": 162
  },
  "faceAttributes": {
    "makeup": {
      "eyeMakeup": true,
      "lipMakeup": true
    },
    "facialHair": {
      "sideburns": 0,
      "beard": 0,
      "moustache": 0
    },
    "gender": "female",
    "accessories": [],
    "blur": {
      "blurLevel": "low",
      "value": 0.06
    },
    "headPose": {
      "roll": 0.1,
      "pitch": 0,
      "yaw": -32.9
    },
    "smile": 0,
    "glasses": "NoGlasses",
    "hair": {
      "bald": 0,
      "invisible": false,
      "hairColor": [
        {
          "color": "brown",
          "confidence": 1
        },
        {
          "color": "black",
          "confidence": 0.87
        },
        {
          "color": "other",
          "confidence": 0.51
        },
        {
          "color": "blond",
          "confidence": 0.08
        },
        {
          "color": "red",
          "confidence": 0.08
        },
        {
          "color": "gray",
          "confidence": 0.02
        }
      ]
    },
    "emotion": {
      "contempt": 0,
      "surprise": 0.005,
      "happiness": 0,
      "neutral": 0.986,
      "sadness": 0.009,
      "disgust": 0,
      "anger": 0,
      "fear": 0
    },
    "exposure": {
      "value": 0.67,
      "exposureLevel": "goodExposure"
    },
    "occlusion": {
      "eyeOccluded": false,
      "mouthOccluded": false,
      "foreheadOccluded": false
    },
    "noise": {
      "noiseLevel": "low",
      "value": 0
    },
    "age": 22.9
  },
  "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f"
}]
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy egyszerű Java-konzolalkalmazást, amely REST-hívásokat használ az Azure Face API-hoz a rendszerkép arcok észleléséhez és attribútumaik visszaadásához. Ezután ismerje meg, hogyan tehet többet ezzel a funkcióval egy Android-alkalmazásban.

> [!div class="nextstepaction"]
> [Oktatóanyag: Hozzon létre egy Android-alkalmazást az arcok észlelésére és keretezésére](../Tutorials/FaceAPIinJavaForAndroidTutorial.md)
