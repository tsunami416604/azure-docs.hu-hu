---
title: 'Oktatóanyag: Anomáliadetektálás a JavaScripttel'
titlesuffix: Azure Cognitive Services
description: Megismerkedhet egy JavaScript-webalkalmazással, amely az Anomaly Detection API-t használja. Ha az eredeti adatpontokat elküldi az API-nak, visszakapja a várt értéket és az attól eltérő pontokat.
services: cognitive-services
author: wenya
manager: bix
ms.service: cognitive-services
ms.component: anomaly-detection
ms.topic: tutorial
ms.date: 05/01/2018
ms.author: wenya
ms.openlocfilehash: cd65a5275fac651968197c9000b2d4cd3962693b
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2018
ms.locfileid: "48883703"
---
# <a name="tutorial-anomaly-detection-with-javascript-application"></a>Oktatóanyag: Anomáliadetektálás JavaScript-alkalmazásokkal

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Megismerkedhet egy webalkalmazással, amely az Anomaly Detection REST API használatával észleli a rendellenességeket. A példa elküldi az idősoradatokat az Anomaly Detection API-nak az előfizetési kulccsal, és az API visszaküldi az összes rendellenes pontot és az elvárt értéket.

## <a name="prerequisites"></a>Előfeltételek

### <a name="platform-requirements"></a>Platformkövetelmények

Ezt az oktatóanyagot egy egyszerű szövegszerkesztő használatával állítottuk össze.

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Előfizetés az anomáliadetektálás használatára és előfizetői azonosító beszerzése 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="get-and-use-the-example"></a>A példa letöltése és használata

Ez az oktatóanyag két forgatókönyvet biztosít az idősoradatok anomáliáinak észlelésére. Lássunk neki!

<a name="Step1"></a> 
### <a name="download-the-tutorial-project"></a>Az oktatóprojekt letöltése

Klónozza a [Cognitive Services JavaScript anomáliadetektálási oktatóanyagát](https://github.com/MicrosoftAnomalyDetection/javascript-sample), vagy töltse le a .zip-fájlt, és bontsa ki egy üres könyvtárba.

<a name="Step2"></a>
### <a name="run-the-example"></a>A példa futtatása

A példát két forgatókönyvben is kipróbálhatja.
1. Másolja be az **előfizetői azonosítót** a Subscription Key (Előfizetői azonosító) mezőbe az észlelési függvényben az anomalydetection.html-ben.
2. Másolja be az Anomaly Detection API-végpontot, és ellenőrizze, hogy a megfelelő régió szerepel-e a Subscription Region (Előfizetés régiója) mezőben.
3. Nyissa meg az **anomalydetection.html** fájlt egy webböngészőben.

**1. forgatókönyv: Heti idősorozat-adatok észlelése**
1. A Period (Időtartam) mezőben adja meg a **7** értéket. 
2. Cserélje le a Points (Pontok) mezőben a mintaadatokat a heti idősorozat adatpontjaira (JSON), vagy használja közvetlenül a mintaadatokat.
3. Kattintson az Anomaly Detection (Anomáliadetektálás) gombra, és ellenőrizze az eredményt a Response (Válasz) szövegmezőben.

**2. forgatókönyv: Idősorozat-adatok észlelése időtartam nélkül**
1. Hagyja az időtartamot üresen a Period (Időtartam) mezőben, feltételezve, hogy nem ismeri az idősorozat időtartamát.
2. Használja ugyanazokat az idősorozat-adatokat, mint az 1. forgatókönyvben.
3. Kattintson az Anomaly Detection (Anomáliadetektálás) gombra, és ellenőrizze a Period mezőt a megfelelő a Response (Válasz) szövegmezőben.

<a name="Step3"></a>
### <a name="read-the-result"></a>Az eredmények értelmezése

[!INCLUDE [diagrams](../includes/diagrams.md)]

<a name="Review"></a>
### <a name="review-and-learn"></a>Áttekintés és tanulás

Most tehát rendelkezik egy futó alkalmazással. Tekintsük át, hogyan integrálható a példaalkalmazás a Cognitive Services technológiával. Ez a lépés segítséget nyújt ezen alkalmazás továbbfejlesztéséhez, vagy saját alkalmazások összeállításához a Microsoft Anomaly Detection használatával.
A mintaalkalmazás az Anomaly Detection Restful API-végpontot használja.
A Restful API mintaalkalmazásban való használatának áttekintése érdekében nézzük meg közelebbről az anomalydetection.html egy kódrészletét.
```JavaScript
function anomalyDetection(url, subscriptionKey, points, period) {
    var obj = new Object();
    obj.Points = JSON.parse(points); // this points are read from text box.
    obj.Period = parseInt(period);//period=7 weekly period
    var tsData = JSON.stringify(obj);
    // Perform the REST API call.
    $.ajax({
        url: url, //Anomaly Detection API endpoint
        // Request headers.
        beforeSend: function (xhrObj) {
            xhrObj.setRequestHeader("Content-Type", "application/json");
            xhrObj.setRequestHeader("Ocp-Apim-Subscription-Key", subscriptionKey); // Replace your subscription key
        },
        type: "POST",
        // Request body.
        data: tsData, // json format
        })
        .done(function (data) {
            // Show formatted JSON on webpage.
            $("#responseTextArea").val(JSON.stringify(data, null, 2));
        })
        .fail(function (jqXHR, textStatus, errorThrown) {
            // Display error message.
            var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
            errorString += (jqXHR.responseText === "") ? "" : jQuery.parseJSON(jqXHR.responseText).message;
            $("#responseTextArea").val(errorString);           
        });
}

```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [REST API – referencia](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
