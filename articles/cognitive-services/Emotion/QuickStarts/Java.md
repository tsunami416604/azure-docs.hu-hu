---
title: Android – első lépések a érzelemfelismerési API Java |} Microsoft Docs
description: Get információkat és a segítségével gyorsan példakód az első lépéseiben a Érzelemfelismerési API Java Android kognitív szolgáltatásban.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 05/23/2017
ms.author: anroth
ms.openlocfilehash: 64a5c4e761653748c4328e310f9a399fe62f9149
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347547"
---
# <a name="emotion-api-java-for-android-quick-start"></a>Android – első lépések a érzelemfelismerési API Java

> [!IMPORTANT]
> Villámnézet API a 2017. október 30 véget ér. Kipróbálhatja az új [videó indexelő API előnézete](https://azure.microsoft.com/services/cognitive-services/video-indexer/) insights könnyen kibontani videók, és tartalom felderítési lép, például a keresési eredmények, növelje a szóbeli szavakat, a lapok, a karakterek és a érzelmek észlelésével. [További információk](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

Ez a cikk tájékoztatást ad és a kód a minta segítségével gyorsan megismerkedhet a [Érzelemfelismerési ismeri fel metódus](https://dev.projectoxford.ai/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) a Érzelemfelismerési API Android ügyfél könyvtárban. A minta bemutatja, hogyan lehet Java ismeri fel a érzelmek személyek kifejezve. 

## <a name="prerequisites"></a>Előfeltételek
* A Érzelemfelismerési API Java lekérése Android SDK [Itt](https://github.com/Microsoft/Cognitive-emotion-android)
* Az ingyenes előfizetés kulcs lekérése [Itt](https://azure.microsoft.com/try/cognitive-services/)

## <a name="recognize-emotions-java-for-android-example-request"></a>Android példa egy kérelem érzelmek Java felismerni

```java
// // This sample uses the Apache HTTP client from HTTP Components (http://hc.apache.org/httpcomponents-client-ga/)
import java.net.URI;
import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.StringEntity;
import org.apache.http.client.utils.URIBuilder;
import org.apache.http.impl.client.DefaultHttpClient;
import org.apache.http.util.EntityUtils;

public class Main
{
    public static void main(String[] args)
    {
        HttpClient httpClient = new DefaultHttpClient();

        try
        {
            // NOTE: You must use the same region in your REST call as you used to obtain your subscription keys.
            //   For example, if you obtained your subscription keys from westcentralus, replace "westus" in the 
            //   URL below with "westcentralus".
            URIBuilder uriBuilder = new URIBuilder("https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize");

            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers. Replace the example key below with your valid subscription key.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", "13hc77781f7e4b19b5fcdd72a8df7156");

            // Request body. Replace the example URL below with the URL of the image you want to analyze.
            StringEntity reqEntity = new StringEntity("{ \"url\": \"http://example.com/images/test.jpg\" }");
            request.setEntity(reqEntity);

            HttpResponse response = httpClient.execute(request);
            HttpEntity entity = response.getEntity();

            if (entity != null)
            {
                System.out.println(EntityUtils.toString(entity));
            }
        }
        catch (Exception e)
        {
            System.out.println(e.getMessage());
        }
    }
}
```

## <a name="recognize-emotions-sample-response"></a>Ismeri fel a érzelmek Mintaválasz
Sikeres meghívását arcfelismerési bejegyzések tömbje és a kapcsolódó érzelemfelismerési eredményeiket, arc téglalap mérete csökkenő sorrendben szerinti sorrendben adja vissza. Üres választ, hogy nincs lapok észlelt. Érzelemfelismerési bejegyzés a következő mezőket tartalmazza:
* faceRectangle - felületen a kép helyének.
* pontszámok - Érzelemfelismerési pontszámok minden lap a lemezképben. 

```json
application/json 
[
  {
    "faceRectangle": {
      "left": 68,
      "top": 97,
      "width": 64,
      "height": 97
    },
    "scores": {
      "anger": 0.00300731952,
      "contempt": 5.14648448E-08,
      "disgust": 9.180124E-06,
      "fear": 0.0001912825,
      "happiness": 0.9875571,
      "neutral": 0.0009861537,
      "sadness": 1.889955E-05,
      "surprise": 0.008229999
    }
  }
]
