---
title: 'Gyors útmutató: Digitális tinta felismerése a kézírás-felismerő REST API és Javával'
titleSuffix: Azure Cognitive Services
description: A tinta-felismerő API használatával megkezdheti a digitális tollvonások felismerését.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 09/23/2019
ms.author: aahi
ms.openlocfilehash: e8cd6a4acbd1492bba1c9e88b523a7c44a44f009
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996849"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-java"></a>Gyors útmutató: Digitális tinta felismerése a kézírás-felismerő REST API és Javával

Ezzel a rövid útmutatóval megkezdheti a tinta-felismerő API használatát a digitális tollvonásokon. Ez a Java-alkalmazás egy olyan API-kérést küld, amely JSON formátumú szabadkézi adatokat tartalmaz, és lekéri a választ.

Habár ez az alkalmazás Java nyelven íródott, az API egy REST-alapú webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel.

Általában az API-t egy Digital unking-alkalmazásból hívja meg. Ez a rövid útmutató egy JSON-fájlból a következő kézírásos minta adatait küldi el: tollvonási adatok.

![egy kézzel írott szöveg képe](../media/handwriting-sample.jpg)

Ennek a rövid útmutatónak a forráskódja a [githubon](https://go.microsoft.com/fwlink/?linkid=2089904)érhető el.

## <a name="prerequisites"></a>Előfeltételek

- A [Java&trade; Development Kit (JDK) 7-es](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) vagy újabb verziója.

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

1. Hozzon létre egy új, `sendRequest()` nevű függvényt, amely a fent létrehozott változókat veszi igénybe. Ezután hajtsa végre a következő lépéseket.

2. Hozzon létre egy `CloseableHttpClient` objektumot, amely küldhet kérelmeket az API-nak. Küldje el a kérést egy `HttpPut` kérelem objektumnak a végpont és a kézírás-felismerő URL-cím kombinálásával.

3. A kérelem `setHeader()` függvényével állítsa be a `Content-Type` fejlécet `application/json`re, és adja hozzá az előfizetési kulcsot a `Ocp-Apim-Subscription-Key` fejléchez.

4. A kérelem `setEntity()` függvényét használja az elküldeni kívánt adathoz.   

5. A kérelem elküldéséhez használja az ügyfél `execute()` függvényét, és mentse egy `CloseableHttpResponse` objektumba. 

6. Hozzon létre egy `HttpEntity` objektumot a válasz tartalmának tárolására. A tartalom lekérése `getEntity()`sal. Ha a válasz nem üres, küldje vissza.
    
    [!code-java[send a request](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=sendRequest)]

## <a name="send-an-ink-recognition-request"></a>Kézírás-felismerési kérelem küldése

Hozzon létre egy `recognizeInk()` nevű metódust a szabadkézi körvonal adatai felismeréséhez. Hívja meg a fent létrehozott `sendRequest()` metódust a végponttal, az URL-lel, az előfizetési kulccsal és a JSON-adataival. Szerezze be az eredményt, és nyomtassa ki a-konzolra.

[!code-java[recognizeInk](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=recognizeInk)]

## <a name="load-your-digital-ink-data-and-send-the-request"></a>Töltse be a digitális tinta adatait, és küldje el a kérést

1. Az alkalmazás fő metódusában olvassa el a kérelmekbe felvenni kívánt adatmennyiséget tartalmazó JSON-fájlt.

2. Hívja meg a fent létrehozott Ink-felismerési függvényt.
    
    [!code-java[main method](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=main)]


## <a name="run-the-application-and-view-the-response"></a>Futtassa az alkalmazást, és tekintse meg a választ

Futtassa az alkalmazást. A sikeres válaszokat JSON formátumban adja vissza a rendszer. A JSON-választ is megtalálja a [githubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-response.json).

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [REST API – referencia](https://go.microsoft.com/fwlink/?linkid=2089907)


Ha szeretné megtudni, hogyan működik a Ink-felismerési API egy digitális, a GitHubon futó alkalmazásban, tekintse meg az alábbi példákat a GitHubon:
* [C# és Univerzális Windows-platform (Universal Windows Platform, UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# és Windows megjelenítési alaprendszer (Windows Presentation Foundation, WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [JavaScript-webböngészőalkalmazás](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java- és Android-mobilalkalmazás](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift- és iOS-mobilalkalmazás](https://go.microsoft.com/fwlink/?linkid=2089805)
