---
title: 'Gyors útmutató: Digitális tinta felismerése a tinta felismerő REST API és C #'
titleSuffix: Azure Cognitive Services
description: Ez a rövid útmutató bemutatja, hogyan használható a tinta-felismerő API és a C# a digitális tollvonások felismerésének elindításához.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 08/24/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: a4bb02f11c7a9a75ddc96e0ee8e9f4b868f8ade5
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89051593"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-c"></a>Gyors útmutató: Digitális tinta felismerése a tinta felismerő REST API és C #

[!INCLUDE [ink-recognizer-deprecation](../includes/deprecation-note.md)]

Ezzel a rövid útmutatóval megkezdheti a digitális tollvonások küldését a tinta-felismerő API-nak. Ez a C#-alkalmazás egy, a JSON-formátumú szabadkézi adatokat tartalmazó API-kérést küld, és lekéri a választ.

Habár ez az alkalmazás C# nyelven íródott, az API egy REST-alapú webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel.

Általában az API-t egy Digital unking-alkalmazásból hívja meg. Ez a rövid útmutató egy JSON-fájlból a következő kézírásos minta adatait küldi el: tollvonási adatok.

![egy kézzel írott szöveg képe](../media/handwriting-sample.jpg)

Ennek a rövid útmutatónak a forráskódja a [githubon](https://go.microsoft.com/fwlink/?linkid=2089502)érhető el.

## <a name="prerequisites"></a>Előfeltételek

- A [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) bármely kiadása.
- [Newtonsoft.Json](https://www.newtonsoft.com/json)
    - Newtonsoft.Jstelepítése NuGet-csomagként a Visual Studióban:
        1. Kattintson a jobb gombbal a **megoldás-kezelőre**
        2. Kattintson a **NuGet-csomagok kezelése...** lehetőségre.
        3. `Newtonsoft.Json`A csomag keresése és telepítése
- Ha Linux/MacOS rendszert használ, az alkalmazás a [mono](https://www.mono-project.com/)használatával futtatható.

- Ebben a rövid útmutatóban a jelen rövid útmutatóban szereplő tollvonási adatsorok a [githubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Vision/InkRecognition/quickstart/example-ink-strokes.json)találhatók.

### <a name="create-an-ink-recognizer-resource"></a>Ink-felismerő erőforrás létrehozása

[!INCLUDE [creating-an-ink-recognizer-resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Új alkalmazás létrehozása

1. A Visual Studióban hozzon létre egy új konzolos megoldást, és adja hozzá a következő csomagokat. 
    
    [!code-csharp[import statements](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=imports)]

2. Hozzon létre változókat az előfizetési kulcshoz és a végponthoz, valamint a JSON-fájlhoz. A végpontot később az `inkRecognitionUrl` API-hoz való hozzáféréshez fogja kombinálni. 

    [!code-csharp[endpoint file and key variables](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Függvény létrehozása a kérelmek küldéséhez

1. Hozzon létre egy nevű új aszinkron függvényt `Request` , amely a fent létrehozott változókat veszi fel.

2. Állítsa be az ügyfél biztonsági protokollját és fejléc-információit egy `HttpClient` objektum használatával. Ügyeljen arra, hogy hozzáadja az előfizetési kulcsot a `Ocp-Apim-Subscription-Key` fejléchez. Ezután hozzon létre egy `StringContent` objektumot a kérelemhez.
 
3. Küldje el a kérelmet a-val `PutAsync()` . Ha a kérelem sikeres, küldje vissza a választ.  
    
    [!code-csharp[request example method](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=request)]

## <a name="send-an-ink-recognition-request"></a>Kézírás-felismerési kérelem küldése

1. Hozzon létre egy nevű új függvényt `recognizeInk()` . Hozza létre a kérést, és küldje el úgy, hogy meghívja a `Request()` függvényt a végponttal, az előfizetési kulccsal, az API URL-címével és a digitális tinta körvonalával kapcsolatos adataival.

2. Deszerializálja a JSON-objektumot, és írja a konzolba. 
    
    [!code-csharp[request to recognize ink data](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=recognize)]

## <a name="load-your-digital-ink-data"></a>A digitális tinta adatai betöltése

Hozzon létre egy nevű függvényt `LoadJson()` a szabadkézi adat JSON-fájljának betöltéséhez. A és a használatával `StreamReader` `JsonTextReader` hozza létre `JObject` és küldje vissza.

[!code-csharp[load the JSON file](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=loadJson)]

## <a name="send-the-api-request"></a>Az API-kérelem elküldése

1. Az alkalmazás fő metódusában töltse be a JSON-adatait a fent létrehozott függvénnyel. 

2. Hívja meg a `recognizeInk()` fent létrehozott függvényt. Ezzel a paranccsal `System.Console.ReadKey()` megtarthatja a konzolablak megnyitását az alkalmazás futtatása után.
    
    [!code-csharp[file main method](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=main)]


## <a name="run-the-application-and-view-the-response"></a>Futtassa az alkalmazást, és tekintse meg a választ

Futtassa az alkalmazást. A sikeres válaszokat JSON formátumban adja vissza a rendszer. A JSON-választ is megtalálja a [githubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Vision/InkRecognition/quickstart/example-response.json).


## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [REST API-referencia](https://go.microsoft.com/fwlink/?linkid=2089907)


Ha szeretné megtudni, hogyan működik a Ink-felismerési API egy digitális, a GitHubon futó alkalmazásban, tekintse meg az alábbi példákat a GitHubon:
* [C# és Univerzális Windows-platform (Universal Windows Platform, UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# és Windows megjelenítési alaprendszer (Windows Presentation Foundation, WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [JavaScript-webböngészőalkalmazás](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java- és Android-mobilalkalmazás](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift- és iOS-mobilalkalmazás](https://go.microsoft.com/fwlink/?linkid=2089805)
