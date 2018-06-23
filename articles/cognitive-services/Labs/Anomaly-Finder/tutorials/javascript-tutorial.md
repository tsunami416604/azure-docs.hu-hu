---
title: Anomáliadetektálás Javascript-alkalmazást – Microsoft kognitív szolgáltatások |} Microsoft Docs
description: Megismerkedhet a Javascript webes alkalmazás, amely az Anomáliadetektálási észlelési API kognitív Microsoft-szolgáltatásokban. Eredeti adatpontok küldeni API, és a várt érték és anomáliadetektálási pontok.
services: cognitive-services
author: wenya
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: wenya
ms.openlocfilehash: 42c3941a05efe8b74f818cd99f3606b3073892a9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348175"
---
# <a name="anomaly-detection-javascript-application"></a>Az anomáliadetektálási észlelési Javascript-alkalmazás

Megismerkedhet a webes alkalmazás, amely az Anomáliadetektálási észlelési REST API egy anomáliadetektálási észleléséhez. A példa az Anomáliadetektálási észlelési API adatsorozat időadatok elküldi az előfizetés kulccsal, majd a anomáliadetektálási pontok és a várt érték az egyes adatpontokban lekérdezi az API-n.

## <a name="prerequisites"></a>Előfeltételek

### <a name="platform-requirements"></a>Platform-követelmények

Ebben az oktatóanyagban egy egyszerű szövegszerkesztőben fejlesztettek ki.

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Az anomáliadetektálás szolgáltatásra, és egy előfizetés kulcs beszerzése 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="get-and-use-the-example"></a>Kérheti le és használja a példát

Ez az oktatóanyag idő adatsorozat adatok anomáliadetektálás két eseteit tartalmazza. Lássunk neki.

<a name="Step1"></a> 
### <a name="download-the-tutorial-project"></a>Az oktatóanyag projekt letöltése

Klónozott a [kognitív szolgáltatások JavaScript Anomáliadetektálási észlelési oktatóanyag](https://github.com/MicrosoftAnomalyDetection/javascript-sample), vagy töltse le a .zip fájlt, és bontsa ki üres könyvtárba.

<a name="Step2"></a>
### <a name="run-the-example"></a>A példa futtatásához

Két esetben megpróbálhatja a példa van.
1. Helyezze a **előfizetés kulcs** az Előfizetés kulcs mezőbe a anomalydetection.html funkció észleléséhez.
2. Az anomáliadetektálási észlelési API-végpont helyezze, és győződjön meg arról, hogy a megfelelő terület előfizetési régióban használunk.
3. Nyissa meg a **anomalydetection.html** fájl egy webböngészőben.

**1. forgatókönyv hibakeresés heti idő adatsorozat adatainak**
1. Időszak mezőben adjon meg időszak **7**. 
2. A mintaadatok lecserélése pontok mezőben a heti idő adatsorozat adatpontjainak (Json), vagy a mintaadatok közvetlenül használható.
3. Az Anomáliadetektálás gombra, és erősítse meg az eredményt a megfelelő választ szövegmezőben.

**2. forgatókönyv Hibakeresés az idő adatsor egy pont nélkül**
1. Üres időszakban időszak bejegyezve, hagyja azt feltételezik, hogy nem tudja a idősorozat időszak.
2. Az azonos idő adatsorozat adatokon az 1. forgatókönyv használatát.
3. Az Anomáliadetektálás gombra, és ellenőrizze a megfelelő választ szövegmezőben időszak mezőben.

<a name="Step3"></a>
### <a name="read-the-result"></a>Az eredmény olvasása

[!INCLUDE [diagrams](../includes/diagrams.md)]

<a name="Review"></a>
### <a name="review-and-learn"></a>Tekintse át, és ismerje meg

Most le a futó alkalmazások. Tekintsük át, hogyan a példa az alkalmazás integrálja az kognitív szolgáltatások technológiával. Ez a lépés megkönnyíti az alkalmazás létrehozása vagy a saját Microsoft Anomáliadetektálás használó alkalmazás fejlesztése.
Ez például az alkalmazás lehetővé teszi az Anomáliadetektálási észlelési Restful API használata végpont.
Tekintse át a Restful API lekérdezi használatáról a mintaalkalmazás, vizsgáljuk meg egy kódrészletet a anomalydetection.html.
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
