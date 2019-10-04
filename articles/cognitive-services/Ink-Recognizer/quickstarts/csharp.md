---
title: 'Gyors útmutató: Digitális tinta felismerése a kézírás-felismerő REST API ésC#'
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
ms.openlocfilehash: 86e69d75c067159a4daa637984a392a393dc46fa
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71211785"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-c"></a>Gyors útmutató: Digitális tinta felismerése a kézírás-felismerő REST API ésC#

Ezzel a rövid útmutatóval megkezdheti a digitális tollvonások küldését a tinta-felismerő API-nak. Ez C# az alkalmazás egy olyan API-kérést küld, amely JSON formátumú szabadkézi adatokat tartalmaz, és lekéri a választ.

Az alkalmazás beírásakor az C#API egy REST-alapú webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel.

Általában az API-t egy Digital unking-alkalmazásból hívja meg. Ez a rövid útmutató egy JSON-fájlból a következő kézírásos minta adatait küldi el: tollvonási adatok.

![egy kézzel írott szöveg képe](../media/handwriting-sample.jpg)

Ennek a rövid útmutatónak a forráskódja a [githubon](https://go.microsoft.com/fwlink/?linkid=2089502)érhető el.

## <a name="prerequisites"></a>Előfeltételek

- A [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) bármely kiadása.
- [Newtonsoft.Json](https://www.newtonsoft.com/json)
    - A Newtonsoft. JSON telepítése NuGet-csomagként a Visual Studióban:
        1. Kattintson a jobb gombbal a **megoldás-kezelőre**
        2. Kattintson a **NuGet-csomagok kezelése...** lehetőségre.
        3. A csomag keresése és telepítése `Newtonsoft.Json`
- Ha Linux/MacOS rendszert használ, az alkalmazás a [mono](https://www.mono-project.com/)használatával futtatható.

- Ebben a rövid útmutatóban a jelen rövid útmutatóban szereplő tollvonási adatsorok a [githubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-ink-strokes.json)találhatók.

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]


## <a name="create-a-new-application"></a>Új alkalmazás létrehozása

1. A Visual Studióban hozzon létre egy új konzolos megoldást, és adja hozzá a következő csomagokat. 

    ```csharp
    using System;
    using System.IO;
    using System.Net;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    ```

2. Hozzon létre változókat az előfizetési kulcshoz és a végponthoz. Cserélje le az alábbi végpontot a tinta-felismerő erőforráshoz generált egy elemre. Az API-hoz való kapcsolódáshoz fűzze hozzá a tinta felismerő URI-hoz.

    ```csharp
    // Replace the subscriptionKey string with your valid subscription key.
    const string subscriptionKey = "YOUR_SUBSCRIPTION_KEY";

    // Replace the dataPath string with a path to the JSON formatted ink stroke data.
    const string dataPath = @"PATH-TO-INK-STROKE-DATA"; 

    // URI information for ink recognition:
    const string endpoint = "https://<your-custom-subdomain>.cognitiveservices.azure.com";
    const string inkRecognitionUrl = "/inkrecognizer/v1.0-preview/recognize";
    ```

## <a name="create-a-function-to-send-requests"></a>Függvény létrehozása a kérelmek küldéséhez

1. Hozzon létre egy nevű `Request` új aszinkron függvényt, amely a fent létrehozott változókat veszi fel.

2. Állítsa be az ügyfél biztonsági protokollját és fejléc-információit egy `HttpClient` objektum használatával. Ügyeljen arra, hogy hozzáadja az előfizetési kulcsot `Ocp-Apim-Subscription-Key` a fejléchez. Ezután hozzon `StringContent` létre egy objektumot a kérelemhez.
 
3. Küldje el a kérelmet `PutAsync()`a-val. Ha a kérelem sikeres, küldje vissza a választ.  
    
    ```csharp
    static async Task<string> Request(string apiAddress, string endpoint, string subscriptionKey, string requestData){
        
        using (HttpClient client = new HttpClient { BaseAddress = new Uri(apiAddress) }){
            System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12 | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls;
            client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

            var content = new StringContent(requestData, Encoding.UTF8, "application/json");
            var res = await client.PutAsync(endpoint, content);
            if (res.IsSuccessStatusCode){
                return await res.Content.ReadAsStringAsync();
            }
            else{
                return $"ErrorCode: {res.StatusCode}";
            }
        }
    }
    ```

## <a name="send-an-ink-recognition-request"></a>Kézírás-felismerési kérelem küldése

1. Hozzon létre egy nevű `recognizeInk()`új függvényt. Hozza létre a kérést, és küldje el `Request()` úgy, hogy meghívja a függvényt a végponttal, az előfizetési kulccsal, az API URL-címével és a digitális tinta körvonalával kapcsolatos adataival.

2. Deszerializálja a JSON-objektumot, és írja a konzolba. 
    
    ```csharp
    static void recognizeInk(string requestData){

        //construct the request
        var result = Request(
            endpoint,
            inkRecognitionUrl,
            subscriptionKey,
            requestData).Result;

        dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result);
        System.Console.WriteLine(jsonObj);
    }
    ```

## <a name="load-your-digital-ink-data"></a>A digitális tinta adatai betöltése

Hozzon létre egy `LoadJson()` nevű függvényt a szabadkézi adat JSON-fájljának betöltéséhez. `StreamReader` A és `JsonTextReader` a használatával hozza létre `JObject` és küldje vissza.
    
```csharp
public static JObject LoadJson(string fileLocation){

    var jsonObj = new JObject();

    using (StreamReader file = File.OpenText(fileLocation))
    using (JsonTextReader reader = new JsonTextReader(file)){
        jsonObj = (JObject)JToken.ReadFrom(reader);
    }
    return jsonObj;
}
```

## <a name="send-the-api-request"></a>Az API-kérelem elküldése

1. Az alkalmazás fő metódusában töltse be a JSON-adatait a fent létrehozott függvénnyel. 

2. Hívja meg `recognizeInk()` a fent létrehozott függvényt. Ezzel `System.Console.ReadKey()` a paranccsal megtarthatja a konzolablak megnyitását az alkalmazás futtatása után.
    
    ```csharp
    static void Main(string[] args){

        var requestData = LoadJson(dataPath);
        string requestString = requestData.ToString(Newtonsoft.Json.Formatting.None);
        recognizeInk(requestString);
        System.Console.WriteLine("\nPress any key to exit ");
        System.Console.ReadKey();
        }
    ```

## <a name="run-the-application-and-view-the-response"></a>Futtassa az alkalmazást, és tekintse meg a választ

Futtassa az alkalmazást. A sikeres válaszokat JSON formátumban adja vissza a rendszer. A JSON-választ is megtalálja a [githubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-response.json).


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [REST API – referencia](https://go.microsoft.com/fwlink/?linkid=2089907)


Ha szeretné megtudni, hogyan működik a Ink-felismerési API egy digitális, a GitHubon futó alkalmazásban, tekintse meg az alábbi példákat a GitHubon:
* [C# és Univerzális Windows-platform (Universal Windows Platform, UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# és Windows megjelenítési alaprendszer (Windows Presentation Foundation, WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [JavaScript-webböngészőalkalmazás](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java- és Android-mobilalkalmazás](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift- és iOS-mobilalkalmazás](https://go.microsoft.com/fwlink/?linkid=2089805)
