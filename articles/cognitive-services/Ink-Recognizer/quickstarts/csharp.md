---
title: 'Rövid útmutató: A digitális tinta felismerése a Tintafelismerő REST API-val és a C #'
titleSuffix: Azure Cognitive Services
description: Ez a rövid útmutató bemutatja, hogyan használhatja a Tintafelismerő API-t a digitális tintavonások felismerésére.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: c24d055f1904453d2f512a278f00e23c6fea1d9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371378"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-c"></a>Rövid útmutató: A digitális tinta felismerése a Tintafelismerő REST API-val és a C #

Ezzel a rövid útmutatóval megkezdheti a digitális tintavonások küldését a Tintafelismerő API-ba. Ez a C# alkalmazás egy JSON-formátumú tollvonási adatokat tartalmazó API-kérelmet küld, és lekéri a választ.

Bár ez az alkalmazás C#-ban íródott, az API egy RESTful webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel.

Az API-t általában egy digitális szabadkézi alkalmazásból kell meghívni. Ez a rövid útmutató a következő kézzel írt minta tollvonási adatait küldi egy JSON-fájlból.

![kézzel írt szöveg képe](../media/handwriting-sample.jpg)

A rövid útmutató forráskódja a [GitHubon](https://go.microsoft.com/fwlink/?linkid=2089502)található.

## <a name="prerequisites"></a>Előfeltételek

- A [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) bármely kiadása.
- [Newtonsoft.Json](https://www.newtonsoft.com/json)
    - A Newtonsoft.Json telepítése NuGet csomagként a Visual stúdióban:
        1. Kattintson a jobb gombbal a **Megoldáskezelőre**
        2. Kattintson **a NuGet-csomagok kezelése lehetőségre...**
        3. A `Newtonsoft.Json` csomag keresése és telepítése
- Ha Linux/MacOS-t használ, ez az alkalmazás a [Mono](https://www.mono-project.com/)használatával futtatható.

- A rövid útmutató példa szabadkézi körvonalai a [GitHubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Vision/InkRecognition/quickstart/example-ink-strokes.json)találhatók.

### <a name="create-an-ink-recognizer-resource"></a>Tintafelismerő erőforrás létrehozása

[!INCLUDE [creating-an-ink-recognizer-resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Új alkalmazás létrehozása

1. A Visual Studio-ban hozzon létre egy új konzolmegoldást, és adja hozzá a következő csomagokat. 
    
    [!code-csharp[import statements](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=imports)]

2. Hozzon létre változókat az előfizetési kulcshoz és végponthoz, valamint a példa JSON-fájlhoz. A végpont később kombinálva `inkRecognitionUrl` az API eléréséhez. 

    [!code-csharp[endpoint file and key variables](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Kérelemküldési funkció létrehozása

1. Hozzon létre egy `Request` új aszinkron függvényt, amely a fent létrehozott változókat veszi figyelembe.

2. Állítsa be az ügyfél biztonsági protokollját `HttpClient` és fejlécadatait egy objektum használatával. Ügyeljen arra, hogy az `Ocp-Apim-Subscription-Key` előfizetési kulcsot hozzáadja a fejléchez. Ezután `StringContent` hozzon létre egy objektumot a kérelemhez.
 
3. Küldje el `PutAsync()`a kérést a segítségével. Ha a kérelem sikeres, adja vissza a választ.  
    
    [!code-csharp[request example method](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=request)]

## <a name="send-an-ink-recognition-request"></a>Tintafelismerési kérelem küldése

1. Hozzon létre `recognizeInk()`egy új függvényt, amelynek neve . A kérés tág anamforát hozhatja létre, és küldje el a függvényt a `Request()` végpont, az előfizetési kulcs, az API URL-címe és a digitális tollvonási adatok hívásával.

2. Deszerializálja a JSON-objektumot, és írja azt a konzolra. 
    
    [!code-csharp[request to recognize ink data](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=recognize)]

## <a name="load-your-digital-ink-data"></a>A digitális tintaadatok betöltése

Hozzon létre `LoadJson()` egy függvényt, amelynek célja a tintaadatok JSON-fájljának betöltése. Használja `StreamReader` a, `JsonTextReader` és `JObject` hozzon létre egy, és vissza.

[!code-csharp[load the JSON file](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=loadJson)]

## <a name="send-the-api-request"></a>Az API-kérelem elküldése

1. Az alkalmazás fő módszerében töltse be a JSON-adatokat a fent létrehozott függvénnyel. 

2. Hívja `recognizeInk()` meg a fent létrehozott függvényt. Az `System.Console.ReadKey()` alkalmazás futtatása után tartsa nyitva a konzolablakot.
    
    [!code-csharp[file main method](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=main)]


## <a name="run-the-application-and-view-the-response"></a>Az alkalmazás futtatása és a válasz megtekintése

Futtassa az alkalmazást. A sikeres választ JSON formátumban adja vissza. A JSON-választ a [GitHubon is megtalálhatja.](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Vision/InkRecognition/quickstart/example-response.json)


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [REST API-hivatkozás](https://go.microsoft.com/fwlink/?linkid=2089907)


A szabadkézi elismerésapi digitális szabadkézi alkalmazásokban való működésének megtekintéséhez tekintse meg a githubon található alábbi mintaalkalmazásokat:
* [C# és Univerzális Windows-platform (Universal Windows Platform, UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# és Windows megjelenítési alaprendszer (Windows Presentation Foundation, WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [JavaScript-webböngészőalkalmazás](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java- és Android-mobilalkalmazás](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift- és iOS-mobilalkalmazás](https://go.microsoft.com/fwlink/?linkid=2089805)
