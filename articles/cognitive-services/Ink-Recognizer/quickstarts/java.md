---
title: 'Gyors útmutató: Digitális tinta felismerése a kézírás-felismerő REST API és Javával'
titleSuffix: Azure Cognitive Services
description: A szabadkézi felismerő API-val megkezdheti a digitális tollvonások felismerését ebben a rövid útmutatóban.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 06/30/2020
ms.author: aahi
ms.custom: devx-track-java
ms.openlocfilehash: 902d8a592893a70b8a694ab888bb3c22c6c9670a
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87371549"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-java"></a>Gyors útmutató: Digitális tinta felismerése a kézírás-felismerő REST API és Javával

Ezzel a rövid útmutatóval megkezdheti a tinta-felismerő API használatát a digitális tollvonásokon. Ez a Java-alkalmazás egy olyan API-kérést küld, amely JSON formátumú szabadkézi adatokat tartalmaz, és lekéri a választ.

Habár ez az alkalmazás Java nyelven íródott, az API egy REST-alapú webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel.

Általában az API-t egy Digital unking-alkalmazásból hívja meg. Ez a rövid útmutató egy JSON-fájlból a következő kézírásos minta adatait küldi el: tollvonási adatok.

![egy kézzel írott szöveg képe](../media/handwriting-sample.jpg)

Ennek a rövid útmutatónak a forráskódja a [githubon](https://go.microsoft.com/fwlink/?linkid=2089904)érhető el.

## <a name="prerequisites"></a>Előfeltételek

- A [Java &trade; Development Kit (JDK) 7-es](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) vagy újabb verziója.

- A kódtárak importálása a Maven adattárból
    - [JSON a Java-](https://mvnrepository.com/artifact/org.json/json) csomagban
    - [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) -csomag

- Ebben a rövid útmutatóban a jelen rövid útmutatóban szereplő tollvonási adatsorok a [githubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-ink-strokes.json)találhatók.

### <a name="create-an-ink-recognizer-resource"></a>Ink-felismerő erőforrás létrehozása

[!INCLUDE [creating an ink recognizer resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Új alkalmazás létrehozása

1. Hozzon létre egy új Java-projektet a kedvenc IDE-környezetében vagy szerkesztőjében, és importálja az alábbi kódtárakat.
    
    [!code-java[import statements](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=imports)]

2. Hozzon létre változókat az előfizetési kulcshoz, a végponthoz és a JSON-fájlhoz. A rendszer később hozzáfűzi a végpontot a tinta-felismerő URI-hoz.

    [!code-java[initial vars](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Függvény létrehozása a kérelmek küldéséhez

1. Hozzon létre egy nevű új függvényt `sendRequest()` , amely a fent létrehozott változókat veszi igénybe. Ezután hajtsa végre a következő lépéseket.

2. Hozzon létre egy `CloseableHttpClient` objektumot, amely küldhet kérelmeket az API-nak. Küldje el a kérést egy `HttpPut` kérelem objektumnak a végpont és a kézírás-felismerő URL-címének kombinálásával.

3. A kérelem `setHeader()` funkciójának használatával állítsa be a `Content-Type` fejlécet `application/json` , és adja hozzá az előfizetési kulcsot a `Ocp-Apim-Subscription-Key` fejléchez.

4. Használja a kérelem `setEntity()` függvényét az elküldeni kívánt adathoz.   

5. A `execute()` kérelem elküldéséhez használja az ügyfél függvényét, és mentse egy `CloseableHttpResponse` objektumba. 

6. Hozzon létre egy `HttpEntity` objektumot a válasz tartalmának tárolására. Szerezze be a tartalmat a-val `getEntity()` . Ha a válasz nem üres, küldje vissza.
    
    [!code-java[send a request](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=sendRequest)]

## <a name="send-an-ink-recognition-request"></a>Kézírás-felismerési kérelem küldése

Hozzon létre egy nevű metódust a `recognizeInk()` szabadkézi körvonal adatai felismeréséhez. Hívja `sendRequest()` meg a fent létrehozott metódust a végponttal, az URL-lel, az előfizetési kulccsal és a JSON-adataival. Szerezze be az eredményt, és nyomtassa ki a-konzolra.

[!code-java[recognizeInk](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=recognizeInk)]

## <a name="load-your-digital-ink-data-and-send-the-request"></a>Töltse be a digitális tinta adatait, és küldje el a kérést

1. Az alkalmazás fő metódusában olvassa el a kérelmekbe felvenni kívánt adatmennyiséget tartalmazó JSON-fájlt.

2. Hívja meg a fent létrehozott Ink-felismerési függvényt.
    
    [!code-java[main method](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=main)]


## <a name="run-the-application-and-view-the-response"></a>Futtassa az alkalmazást, és tekintse meg a választ

Futtassa az alkalmazást. A sikeres válaszokat JSON formátumban adja vissza a rendszer. A JSON-választ is megtalálja a [githubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-response.json).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [REST API-referencia](https://go.microsoft.com/fwlink/?linkid=2089907)


Ha szeretné megtudni, hogyan működik a Ink-felismerési API egy digitális, a GitHubon futó alkalmazásban, tekintse meg az alábbi példákat a GitHubon:
* [C# és Univerzális Windows-platform (Universal Windows Platform, UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# és Windows megjelenítési alaprendszer (Windows Presentation Foundation, WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [JavaScript-webböngészőalkalmazás](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java- és Android-mobilalkalmazás](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift- és iOS-mobilalkalmazás](https://go.microsoft.com/fwlink/?linkid=2089805)
