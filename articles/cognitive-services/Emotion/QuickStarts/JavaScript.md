---
title: 'Gyors útmutató: Az arcok a képen – Emotion API, a JavaScript érzelmek felismerése'
titlesuffix: Azure Cognitive Services
description: Információk és kódminták segítségével ismerkedhet meg az Emotion API JavaScripttel való használatának első lépéseivel.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.subservice: emotion-api
ms.topic: quickstart
ms.date: 05/23/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: 3c6e5b0d759aeb8aa0b06e359f1f48eaf6c44520
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55208949"
---
# <a name="quickstart-build-an-app-to-recognize-emotions-on-faces-in-an-image"></a>Gyors útmutató: Érzelemfelismerés az arcok a képen az alkalmazás létrehozása.

> [!IMPORTANT]
> Az Emotion API 2019. február 15-ével elavulttá válik. Az érzelemfelismerési képesség mostantól a [Face API](https://docs.microsoft.com/azure/cognitive-services/face/) részeként általánosan elérhető. 

Ez a cikk információkkal és kódmintákkal szolgál, amelyeken keresztül gyorsan elsajátíthatja, hogyan ismerheti fel a képeken szereplő egy vagy több személy által kifejezett érzelmeket az [Emotion API Recognize metódusa](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) és a JavaScript használatával.

## <a name="prerequisite"></a>Előfeltétel
* Ingyenes előfizetői azonosítóját [itt](https://azure.microsoft.com/try/cognitive-services/) szerezheti be, illetve ha már rendelkezik Azure-előfizetéssel, hozzon létre egy Emotion API-erőforrást, ahonnan beszerezheti az előfizetői azonosítót és a végpontot.

![Emotion API-erőforrás létrehozása](../Images/create-resource.png)

## <a name="recognize-emotions-javascript-example-request"></a>Érzelemfelismerési JavaScript-kérésminta

Másolja és mentse egy fájlba az alábbi szöveget, például `test.html` néven. Változtassa meg a kérésben szereplő `url` értékét arra a címre, ahonnan beszerezte az előfizetői azonosítókat, majd cserélje le az „Ocp-Apim-Subscription-Key” értékét az érvényes előfizetői azonosítójára. Ezek az Azure Portalon, az Emotion API-erőforrás Áttekintés és Kulcsok szakaszában találhatók meg.

![API-végpont](../Images/api-url.png)

![API-előfizetői azonosító](../Images/keys.png)

Módosítsa a kérés törzsét a használni kívánt kép helyére. A minta futtatásához húzza a fájlt a böngészőbe.

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

## <a name="recognize-emotions-sample-response"></a>Érzelemfelismerési válaszminta
A sikeres hívás egy, az arcrekordokat és a hozzájuk tartozó érzelempontszámokat tartalmazó tömböt ad vissza, amely az adatokat az arcot jelölő téglalap mérete szerinti csökkenő sorrendben listázza. Az üres válasz azt jelzi, hogy a rendszer nem észlelt arcot. Az érzelemrekord a következő mezőket foglalja magában:
* faceRectangle – Az arcot jelölő téglalap helye a képen.
* scores – A képen szereplő egyes arcokhoz tartozó érzelempontszámok.

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
