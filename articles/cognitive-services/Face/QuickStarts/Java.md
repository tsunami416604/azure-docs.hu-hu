---
title: 'Gyors útmutató: A képet az Azure REST API és a Java arcok észlelése'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid, használatával az Azure Face REST API javával arcok észlelése a képet.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: quickstart
ms.date: 11/09/2018
ms.author: pafarley
ms.openlocfilehash: 0a8a97be89893dbf072942501be51b82d20c1ef4
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2018
ms.locfileid: "51578056"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-rest-api-and-java"></a>Rövid útmutató: Arcfelismerés egy képen a REST API és a Java használatával

Ebben a rövid, használatával az Azure Face REST API a Javával emberi arcok észlelése a képet.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 

## <a name="prerequisites"></a>Előfeltételek

- A Face API előfizetési kulcs. Megjelenik a származó ingyenes próba-előfizetését kulcsok [próbálja meg a Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Másik lehetőségként kövesse a [Cognitive Services-fiók létrehozása](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) a Face API szolgáltatás és a kulcs beszerzése.
- Bármilyen tetszőleges a Java IDE.

## <a name="create-the-java-project"></a>A Java-projekt létrehozása

Hozzon létre egy új parancssori Java-alkalmazást az IDE-ben, és adjon hozzá egy **fő** osztály használatával egy **fő** metódust. Ezután töltse le a következő globális könyvtárak a Maven tárházból való a `lib` könyvtárat a projekthez:
* `org.apache.httpcomponents:httpclient:4.2.4`
* `org.json:json:20170516`

## <a name="add-face-detection-code"></a>Arcok észlelése kód hozzáadása

Nyissa meg a projekt fő osztály. Itt fogja hozzáadni a képeket tölthet be és arcfelismerés szükséges kódot.

### <a name="import-packages"></a>Csomagok importálása

Adja hozzá a következő `import` utasítások a fájl elejéhez.

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
import org.apache.http.impl.client.DefaultHttpClient;
import org.apache.http.util.EntityUtils;
import org.json.JSONArray;
import org.json.JSONObject;
```

### <a name="add-essential-fields"></a>Alapvető mezők hozzáadása

Adja hozzá a következő mezőket a **fő** osztály. Ezeket az adatokat adja meg, hogyan csatlakozhat a Face szolgáltatás és a bemeneti adatok beszerzése. Frissíteni kell a `subscriptionKey` mező értékét az előfizetési kulcs, és előfordulhat, hogy módosítania kell a `uriBase` úgy, hogy a megfelelő régióazonosító tartalmaz. Is célszerű beállítani a `imageWithFaces` érték, egy másik képfájl mutató elérési utat.

A `faceAttributes` mező értéke csak bizonyos típusú attribútumok listáját. Azt határozza meg az észlelt arcok kapcsolatos lekérni kívánt adatokat.

```Java
// Replace <Subscription Key> with your valid subscription key.
private static final String subscriptionKey = "<Subscription Key>";

// NOTE: You must use the same region in your REST call as you used to
// obtain your subscription keys. For example, if you obtained your
// subscription keys from westus, replace "westcentralus" in the URL
// below with "westus".
//
// Free trial subscription keys are generated in the westcentralus region. If you
// use a free trial subscription key, you shouldn't need to change this region.
private static final String uriBase =
    "https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect";

private static final String imageWithFaces =
    "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg\"}";

private static final String faceAttributes =
    "age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise";
```

### <a name="call-the-face-detection-rest-api"></a>Az arcfelismerés is használható REST API meghívása

Adja hozzá a következő metódust a **fő** metódust. Akkor hoz létre REST-hívást a Face API, arcfelismerési kapcsolatos információk észleléséhez a távoli kép (a `faceAttributes` karakterláncot adja meg, melyik face attribútumok beolvasni). Ezután egy JSON-karakterláncot ír a kimeneti adatokat.

```Java
HttpClient httpclient = new DefaultHttpClient();

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

### <a name="parse-the-json-response"></a>A JSON-válasz elemzése

Közvetlenül az előző kód alatt adja hozzá a következő kódblokk, amely a visszaadott JSON-adatokat egy könnyebben olvasható formátumba konvertálja a nyomtatás, a konzol előtt. Végül zárja be a try-catch blokk ki.

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
```

## <a name="run-the-app"></a>Az alkalmazás futtatása

A kód fordításához és futtathatja. A sikeres válasz Arcfelismerési adatokat a konzolablakban könnyen olvasható JSON-formátumban jeleníti meg. Példa:

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

Ebben a rövid útmutatóban létrehozott egy egyszerű Java konzolalkalmazást, amely az Azure Face API REST-hívások használ a kép arcok észlelése és attribútumaik. Ezután megtudhatja, hogyan tehet még többet az ezt a funkciót az Android-alkalmazás.

> [!div class="nextstepaction"]
> [Oktatóanyag: Keret arcok, és Android-alkalmazás létrehozása](../Tutorials/FaceAPIinJavaForAndroidTutorial.md)
