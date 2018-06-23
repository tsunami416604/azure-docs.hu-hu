---
title: Érzelemfelismerési API a JavaScript – első lépések |} Microsoft Docs
description: Get információkat és a kód minták segítségével gyorsan használatának megkezdésében a Érzelemfelismerési API JavaScript kognitív szolgáltatásban.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 05/23/2017
ms.author: anroth
ms.openlocfilehash: 2578b0212f9b4a6483402074d7c9eff73e51ca6b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347551"
---
# <a name="emotion-api-javascript-quick-start"></a>Érzelemfelismerési API a JavaScript – első lépések

> [!IMPORTANT]
> Villámnézet API a 2017. október 30 véget ér. Kipróbálhatja az új [videó indexelő API előnézete](https://azure.microsoft.com/services/cognitive-services/video-indexer/) insights könnyen kibontani videók, és tartalom felderítési lép, például a keresési eredmények, növelje a szóbeli szavakat, a lapok, a karakterek és a érzelmek észlelésével. [További információk](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

Ez a cikk és a segítségével gyorsan kódmintákat az első lépéseiben a [Érzelemfelismerési API-t ismeri fel a metódust](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) ismeri fel a kép egy vagy több személy által kifejezett érzelmek JavaScript.

## <a name="prerequisite"></a>Előfeltétel
* Az ingyenes előfizetés kulcs lekérése [Itt](https://azure.microsoft.com/try/cognitive-services/), vagy ha Azure-előfizetéssel rendelkezik Érzelemfelismerési API-erőforrás létrehozása és érheti el az Előfizetés kulcs és a végpont.

![Érzelemfelismerési API-erőforrás létrehozása](../Images/create-resource.png)

## <a name="recognize-emotions-javascript-example-request"></a>Ismeri fel a érzelmek JavaScript példa egy kérelem

A következő másolja ki és mentse azt egy fájlba, mint `test.html`. A változáskérés `url` használhatja azt a helyet, ahol az előfizetés kulcsok szerezte be, és az "Ocp-Apim-előfizetés-kulcsot" értéket lecseréli a érvényes előfizetés-kulcs. Ezek található Azure-portálon a áttekintése és a kulcsok szakaszokban a Érzelemfelismerési API-erőforrás, illetve. 

![API-végpont](../Images/api-url.png)

![Előfizetés az API-kulcs](../Images/keys.png)

és módosítsa a kérés törzsében a használni kívánt lemezkép helyét. A minta fogd és vidd a fájl futtatásához a böngészőbe.

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
