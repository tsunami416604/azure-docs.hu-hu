---
title: Anomáliadetektálás Javascript-alkalmazás – a Microsoft Cognitive Services |} A Microsoft Docs
description: Ismerje meg, amely az Anomáliadetektáló API-t használja a Microsoft Cognitive Services Javascript webes alkalmazás. Küldés eredeti adatpontok API-hoz és a várt érték és anomáliadetektálási pontokat.
services: cognitive-services
author: wenya
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: wenya
ms.openlocfilehash: 42c3941a05efe8b74f818cd99f3606b3073892a9
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38453169"
---
# <a name="anomaly-detection-javascript-application"></a>Rendellenességek észlelése Javascript-alkalmazását

Ismerje meg egy webalkalmazást, amely az Anomáliadetektáló REST API-t használja egy rendellenességek észlelésére. A példa az idősoros adatainak az Anomáliadetektáló API-t, elküldi az előfizetési kulccsal végzett, majd minden anomáliadetektálási pontok és a várt érték az egyes lekérdezi az API-ból.

## <a name="prerequisites"></a>Előfeltételek

### <a name="platform-requirements"></a>Platform-követelmények

Ebben az oktatóanyagban egy egyszerű szövegszerkesztő használatával fejlesztettek ki.

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Fizessen elő a rendellenességek észlelése és a egy előfizetési kulcs lekérése 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="get-and-use-the-example"></a>És a példában a

Ez az oktatóanyag két forgatókönyvet time series rendellenesség-észlelést biztosít. Első lépések.

<a name="Step1"></a> 
### <a name="download-the-tutorial-project"></a>Az oktatóanyag projektjének letöltése

Klónozás a [Cognitive Services JavaScript rendellenességek észlelése oktatóanyagban](https://github.com/MicrosoftAnomalyDetection/javascript-sample), vagy töltse le a .zip-fájlt, majd kicsomagolhatja azt egy üres könyvtárra.

<a name="Step2"></a>
### <a name="run-the-example"></a>A példa Futtatás

Próbálja meg a példában két forgatókönyv közül választhat.
1. Helyezze a **előfizetési kulcs** az előfizetési kulcs mezőbe a funkciót, anomalydetection.html észlelése.
2. Helyezze a rendellenességek észlelése API végpont, és győződjön meg arról, hogy a megfelelő régiót használ előfizetés régióban.
3. Nyissa meg a **anomalydetection.html** fájl egy webböngészőben.

**1. forgatókönyv hibakeresés heti idősorozat-adatok**
1. Időszak mezőben adjon meg időszak **7**. 
2. Cserélje le a mintaadatokat a pontok mezőben a heti idő adatsorozat adatpontjainak (Json), vagy közvetlenül a mintaadatok használatával.
3. A rendellenességek észlelése gombra, és erősítse meg a megfelelő választ szövegmezőbe írja az eredményt.

**2. forgatókönyv észlelése az idősorozat-adatok pont nélkül**
1. Az üres időszakban időszak mezőjénél, hagyja azt feltételezik, hogy nem tudja azt az időszakot, az idősorozat.
2. Az azonos idősoros adatainak használatával az 1. forgatókönyv szerint.
3. A rendellenességek észlelése gombra, és ellenőrizze a megfelelő választ szövegmezőbe az időszak mezőben.

<a name="Step3"></a>
### <a name="read-the-result"></a>Olvassa el az eredmény

[!INCLUDE [diagrams](../includes/diagrams.md)]

<a name="Review"></a>
### <a name="review-and-learn"></a>Tekintse át, és ismerje meg

Mostantól kap egy futó alkalmazás. Tekintsük át, hogyan integrálható a a PéldaAlkalmazás Cognitive Services-technológiát. Ebben a lépésben könnyebben továbbra is használhatja ezt az alkalmazást, vagy a saját Microsoft Anomáliadetektálás használatával alkalmazás fejlesztése.
Ez a példa az alkalmazás lehetővé teszi, hogy használja az Anomáliadetektáló Restful API-végpont.
Tekintse át, hogyan lekérdezi a Restful API-t használja a mintaalkalmazás, tekintsük át egy kódrészletet a anomalydetection.html.
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
