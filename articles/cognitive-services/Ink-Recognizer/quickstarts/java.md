---
title: 'Rövid útmutató: A digitális tinta felismerése a Tintafelismerő REST API-val és a Java-val'
titleSuffix: Azure Cognitive Services
description: A Tintafelismerő API-val elkezdheti felismerni a digitális tintavonásokat ebben a rövid útmutatóban.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: d2cd4e56477ea39587ce318538c9ddd84c51b03b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75448122"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-java"></a>Rövid útmutató: A digitális tinta felismerése a Tintafelismerő REST API-val és a Java-val

Ezzel a rövid útmutatóval elkezdheti használni a Tintafelismerő API-t digitális tintavonásokon. Ez a Java alkalmazás egy JSON-formátumú tollvonási adatokat tartalmazó API-kérelmet küld, és megkapja a választ.

Bár ez az alkalmazás Java nyelven íródott, az API egy RESTful webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel.

Az API-t általában egy digitális szabadkézi alkalmazásból kell meghívni. Ez a rövid útmutató a következő kézzel írt minta tollvonási adatait küldi egy JSON-fájlból.

![kézzel írt szöveg képe](../media/handwriting-sample.jpg)

A rövid útmutató forráskódja a [GitHubon](https://go.microsoft.com/fwlink/?linkid=2089904)található.

## <a name="prerequisites"></a>Előfeltételek

- A [&trade; Java Development Kit(JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) vagy újabb.

- Ezeket a könyvtárakat importálja a Maven-tárházból
    - [JSON Java csomagban](https://mvnrepository.com/artifact/org.json/json)
    - [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) csomag

- A rövid útmutató példa szabadkézi körvonalai a [GitHubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-ink-strokes.json)találhatók.

### <a name="create-an-ink-recognizer-resource"></a>Tintafelismerő erőforrás létrehozása

[!INCLUDE [creating an ink recognizer resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Új alkalmazás létrehozása

1. Hozzon létre egy új Java-projektet a kedvenc IDE-környezetében vagy szerkesztőjében, és importálja az alábbi kódtárakat.
    
    [!code-java[import statements](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=imports)]

2. Hozzon létre változókat az előfizetési kulcshoz, a végponthoz és a JSON-fájlhoz. The endpoint will later be appended to the Ink recognizer URI.

    [!code-java[initial vars](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Kérelemküldési funkció létrehozása

1. Hozzon létre `sendRequest()` egy új függvényt, amely a fent létrehozott változókat veszi figyelembe. Ezután hajtsa végre a következő lépéseket.

2. Hozzon `CloseableHttpClient` létre egy objektumot, amely kéréseket küldhet az API-nak. Küldje el a `HttpPut` kérelmet egy kérelemobjektumnak a végpont és a tintafelismerő URL-címének kombinálásával.

3. A kérelem `setHeader()` függvényében állítsa `Content-Type` be `application/json`a fejlécet a , `Ocp-Apim-Subscription-Key` és adja hozzá az előfizetési kulcsot a fejléchez.

4. Használja a kérelem `setEntity()` funkcióját az elküldendő adatokhoz.   

5. Az ügyfél függvényének `execute()` használatával küldje el a kérelmet, és mentse egy `CloseableHttpResponse` objektumba. 

6. Hozzon `HttpEntity` létre egy objektumot a választartalom tárolásához. A tartalom `getEntity()`beszerezése a segítségével. Ha a válasz nem üres, adja vissza.
    
    [!code-java[send a request](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=sendRequest)]

## <a name="send-an-ink-recognition-request"></a>Tintafelismerési kérelem küldése

Hozzon létre `recognizeInk()` egy módszert, amelynek neve a külső körvonal adatainak felismerésére szolgál. Hívja `sendRequest()` meg a fenti metódust a végpont, url, előfizetési kulcs és json adatokat. Szerezd meg az eredményt, és nyomtassa ki a konzolra.

[!code-java[recognizeInk](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=recognizeInk)]

## <a name="load-your-digital-ink-data-and-send-the-request"></a>A digitális tintaadatok betöltése és a kérelem elküldése

1. Az alkalmazás fő metódusában olvassa el a JSON-fájlt, amely tartalmazza a kérelmekhez hozzáadott adatokat.

2. Hívja meg a fent létrehozott tintafelismerő függvényt.
    
    [!code-java[main method](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=main)]


## <a name="run-the-application-and-view-the-response"></a>Az alkalmazás futtatása és a válasz megtekintése

Futtassa az alkalmazást. A sikeres választ JSON formátumban adja vissza. A JSON-választ a [GitHubon is megtalálhatja.](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-response.json)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [REST API-hivatkozás](https://go.microsoft.com/fwlink/?linkid=2089907)


A szabadkézi elismerésapi digitális szabadkézi alkalmazásokban való működésének megtekintéséhez tekintse meg a githubon található alábbi mintaalkalmazásokat:
* [C# és Univerzális Windows-platform (Universal Windows Platform, UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# és Windows megjelenítési alaprendszer (Windows Presentation Foundation, WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [JavaScript-webböngészőalkalmazás](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java- és Android-mobilalkalmazás](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift- és iOS-mobilalkalmazás](https://go.microsoft.com/fwlink/?linkid=2089805)
