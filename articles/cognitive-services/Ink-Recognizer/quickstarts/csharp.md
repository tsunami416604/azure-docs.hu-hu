---
title: 'Gyors útmutató: Digitális tinta felismerése a kézírás-felismerővel REST API ésC#'
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
ms.openlocfilehash: 6ef58d1444eb0ec7b2b8891f944b428a613edf3a
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72515611"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-c"></a>Gyors útmutató: Digitális tinta felismerése a kézírás-felismerővel REST API ésC#

Ezzel a rövid útmutatóval megkezdheti a digitális tollvonások küldését a tinta-felismerő API-nak. Ez C# az alkalmazás egy olyan API-kérést küld, amely JSON formátumú szabadkézi adatokat tartalmaz, és lekéri a választ.

Az alkalmazás beírásakor az C#API egy REST-alapú webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel.

Általában az API-t egy Digital unking-alkalmazásból hívja meg. Ez a rövid útmutató egy JSON-fájlból a következő kézírásos minta adatait küldi el: tollvonási adatok.

![egy kézzel írott szöveg képe](../media/handwriting-sample.jpg)

Ennek a rövid útmutatónak a forráskódja a [githubon](https://go.microsoft.com/fwlink/?linkid=2089502)érhető el.

## <a name="prerequisites"></a>Előfeltételek

- A [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) bármely kiadása.
- [Newtonsoft. JSON](https://www.newtonsoft.com/json)
    - A Newtonsoft. JSON telepítése NuGet-csomagként a Visual Studióban:
        1. Kattintson a jobb gombbal a **megoldás-kezelőre**
        2. Kattintson a **NuGet-csomagok kezelése...** lehetőségre.
        3. @No__t_0 keresése és a csomag telepítése
- Ha Linux/MacOS rendszert használ, az alkalmazás a [mono](https://www.mono-project.com/)használatával futtatható.

- Ebben a rövid útmutatóban a jelen rövid útmutatóban szereplő tollvonási adatsorok a [githubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-ink-strokes.json)találhatók.

### <a name="create-an-ink-recognizer-resource"></a>Ink-felismerő erőforrás létrehozása

[!INCLUDE [creating-an-ink-recognizer-resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Új alkalmazás létrehozása

1. A Visual Studióban hozzon létre egy új konzolos megoldást, és adja hozzá a következő csomagokat. 
    
    [!code-csharp[import statements](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=imports)]

2. Hozzon létre változókat az előfizetési kulcshoz és a végponthoz, valamint a JSON-fájlhoz. A végpontot később a `inkRecognitionUrl` együtt kell egyesíteni az API-hoz való hozzáféréshez. 

    [!code-csharp[endpoint file and key variables](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Függvény létrehozása a kérelmek küldéséhez

1. Hozzon létre egy új, `Request` nevű aszinkron függvényt, amely a fent létrehozott változókat veszi igénybe.

2. Állítsa be az ügyfél biztonsági protokollját és fejlécének adatait egy `HttpClient` objektum használatával. Ügyeljen arra, hogy hozzáadja az előfizetési kulcsot a `Ocp-Apim-Subscription-Key` fejléchez. Ezután hozzon létre egy `StringContent` objektumot a kérelemhez.
 
3. Küldje el a kérelmet `PutAsync()`. Ha a kérelem sikeres, küldje vissza a választ.  
    
    [!code-csharp[request example method](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=request)]

## <a name="send-an-ink-recognition-request"></a>Kézírás-felismerési kérelem küldése

1. Hozzon létre egy új, `recognizeInk()` nevű függvényt. Hozza létre a kérést, és küldje el úgy, hogy meghívja a `Request()` függvényt a végponttal, az előfizetés kulcsával, az API URL-címével és a digitális tinta körvonalának adataival.

2. Deszerializálja a JSON-objektumot, és írja a konzolba. 
    
    [!code-csharp[request to recognize ink data](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=recognize)]

## <a name="load-your-digital-ink-data"></a>A digitális tinta adatai betöltése

Hozzon létre egy `LoadJson()` nevű függvényt a szabadkézi adat JSON-fájljának betöltéséhez. @No__t_0 és `JsonTextReader` használatával hozzon létre egy `JObject`, majd küldje vissza.

[!code-csharp[load the JSON file](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=loadJson)]

## <a name="send-the-api-request"></a>Az API-kérelem elküldése

1. Az alkalmazás fő metódusában töltse be a JSON-adatait a fent létrehozott függvénnyel. 

2. Hívja meg a fent létrehozott `recognizeInk()` függvényt. A `System.Console.ReadKey()` használatával megtarthatja a konzolablak megnyitását az alkalmazás futtatása után.
    
    [!code-csharp[file main method](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=main)]


## <a name="run-the-application-and-view-the-response"></a>Futtassa az alkalmazást, és tekintse meg a választ

Futtassa az alkalmazást. A sikeres válaszokat JSON formátumban adja vissza a rendszer. A JSON-választ is megtalálja a [githubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-response.json).


## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [REST API – referencia](https://go.microsoft.com/fwlink/?linkid=2089907)


Ha szeretné megtudni, hogyan működik a Ink-felismerési API egy digitális, a GitHubon futó alkalmazásban, tekintse meg az alábbi példákat a GitHubon:
* [C# és Univerzális Windows-platform (Universal Windows Platform, UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# és Windows megjelenítési alaprendszer (Windows Presentation Foundation, WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [JavaScript-webböngészőalkalmazás](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java- és Android-mobilalkalmazás](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift- és iOS-mobilalkalmazás](https://go.microsoft.com/fwlink/?linkid=2089805)
