---
title: Az Emotion API a JavaScript gyors üzembe helyezési |} A Microsoft Docs
description: Get information és kód minták segítségével gyorsan Ismerkedjen meg az Emotion API-val, a Cognitive Services JavaScript.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 05/23/2017
ms.author: anroth
ms.openlocfilehash: fb9cc2335582c4ec75ec45635e519346d65d7e08
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39072092"
---
# <a name="emotion-api-javascript-quick-start"></a>Az Emotion API a JavaScript gyors üzembe helyezési

> [!IMPORTANT]
> Video API előzetes verziója 2017. október 30-én megszűnik. Próbálja ki az új [Video Indexer API előzetes verzióját](https://azure.microsoft.com/services/cognitive-services/video-indexer/) könnyedén kiértékelheti a videókkal és javíthatja a tartalomkeresés élményét, például a keresési eredmények között, mely képes észlelni a videóban elhangzó szövegre keressenek, arcok, karakterek és érzelmeket. [További információk](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

Ez a cikk nyújt információt, és kódminták segítségével gyorsan használatának első lépései a [metódus az Emotion API-t ismerik fel](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) JavaScript, a kép egy vagy több személynek által kifejezett érzelemfelismerés.

## <a name="prerequisite"></a>Előfeltétel
* Az ingyenes előfizetési kulcs lekérése [Itt](https://azure.microsoft.com/try/cognitive-services/), ha rendelkezik Azure-előfizetéssel, vagy az Emotion API-erőforrás létrehozásához és az előfizetési kulcs és a végpont érheti el.

![Az Emotion API-erőforrás létrehozása](../Images/create-resource.png)

## <a name="recognize-emotions-javascript-example-request"></a>Érzelmek JavaScript példa kérelem felismerése

Másolja a következőket, és mentse azt egy fájlba például `test.html`. A változáskérés `url` használhatja azt a helyet, ahol az előfizetési kulcsok szerezte be, és az "Ocp-Apim-Subscription-Key" értékét cserélje le az érvényes előfizetési kulcs. Találhatók az Azure Portalon, az Emotion API erőforrás áttekintése és a kulcsok szakaszában jelölik. 

![API-végpont](../Images/api-url.png)

![API-előfizetési kulcs](../Images/keys.png)

Módosítsa a kérés törzse egy használni kívánt lemezkép helyét. A futtassa a mintát, és húzza a fájlt a böngészőbe.

```html
<!DOCTYPE html>
<html>
<head>
    <title>JSSample</title>
    <script src="http://ajax.googleapis.com/ajax/libs/jquery/1.9.0/jquery.min.js"></script>
</head>

<h2>Face Rectangle</h2>
<ul id="faceRectangle">
<!-- Will populate list with response content -->
</ul>

<h2>Emotions</h2>
<ul id="scores">
<!-- Will populate list with response content -->
</ul>

<body>

<script type="text/javascript">
    $(function() {
        // No query string parameters for this API call.
        var params = { };
      
        $.ajax({
            // NOTE: You must use the same location in your REST call as you used to obtain your subscription keys.
            //   For example, if you obtained your subscription keys from westcentralus, replace "westus" in the 
            //   URL below with "westcentralus".
            url: "https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize?" + $.param(params),
            beforeSend: function(xhrObj){
                // Request headers, also supports "application/octet-stream"
                xhrObj.setRequestHeader("Content-Type","application/json");

                // NOTE: Replace the "Ocp-Apim-Subscription-Key" value with a valid subscription key.
                xhrObj.setRequestHeader("Ocp-Apim-Subscription-Key","<your subscription key>");
            },
            type: "POST",
            // Request body
            data: '{"url": "<your image url>"}',
        }).done(function(data) {
            // Get face rectangle dimensions
            var faceRectangle = data[0].faceRectangle;
            var faceRectangleList = $('#faceRectangle');

            // Append to DOM
            for (var prop in faceRectangle) {
                faceRectangleList.append("<li> " + prop + ": " + faceRectangle[prop] + "</li>");
            }
            
            // Get emotion confidence scores
            var scores = data[0].scores;
            var scoresList = $('#scores');

            // Append to DOM
            for(var prop in scores) {
                scoresList.append("<li> " + prop + ": " + scores[prop] + "</li>")
            }
        }).fail(function(err) {
            alert("Error: " + JSON.stringify(err));
        });
    });
</script>
</body>
</html>
```

## <a name="recognize-emotions-sample-response"></a>Mintaválasz érzelmek felismerése
Egy sikeres meghívásához face bejegyzések tömbjét és társított érzelemfelismerési eredményeiket, csökkenő sorrendben face téglalap méret szerint rangsorolva, amelyekről adja vissza. Üres választ, hogy nincs arcok észlelt. Érzelemfelismerési bejegyzés a következő mezőket tartalmazzák:
* faceRectangle - archoz téglalap helyét.
* pontszámok – Emotion pontszámok minden lap a képen. 

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
