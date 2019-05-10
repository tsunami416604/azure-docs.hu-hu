---
title: 'Gyors útmutató: Ismeri fel a digitális ink a szabadkézi felismerő REST API-val és aC#'
description: A tinta felismerő API használatával indítsa el a digitális tollvonások felismerve.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: article
ms.date: 05/02/2019
ms.author: aahi
ms.openlocfilehash: 9bb9c23cc1f807cae1d0d22f1652e8f4408f1f91
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2019
ms.locfileid: "65518673"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-c"></a>Gyors útmutató: Ismeri fel a digitális ink a szabadkézi felismerő REST API-val és aC#

Ez a rövid útmutató segítségével megkezdheti a digitális tollvonások küldését a szabadkézi felismerő API-t. Ez C# alkalmazás egy JSON-formátumú ink körvonal adatokat tartalmazó API-kérést küld, és lekéri a válaszban.

Bár ez az alkalmazás nyelven van megírva C#, az API egy REST-alapú webszolgáltatás szinte bármelyik programozási nyelvével kompatibilis.

Általában az API-t kellene hívása egy digitális szabadkézi alkalmazásból. Ez a rövid útmutató a következő kézzel írt mintát ink körvonal adatokat küld a JSON-fájlból.

![írt szöveg felismerése képekből képe](../media/handwriting-sample.jpg)

Ez a rövid útmutató forráskódja találhatók [GitHub](https://go.microsoft.com/fwlink/?linkid=2089502).

## <a name="prerequisites"></a>Előfeltételek

- A [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) bármely kiadása.
- [Newtonsoft.Json](https://www.newtonsoft.com/json)
    - Telepítése a Newtonsoft.Json NuGet-csomagként a Visual studióban:
        1. Kattintson a jobb gombbal a **megoldás Manager**
        2. Kattintson a **NuGet-csomagok kezelése...**
        3. Keresse meg `Newtonsoft.Json` és telepítse a csomagot
- Linux/MacOS rendszeren használja, ha az alkalmazás képes futtatni használatával [Mono](https://www.mono-project.com/).

- Ez a rövid útmutató példa ink körvonal adatai találhatók [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-ink-strokes.json).

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]


## <a name="create-a-new-application"></a>Új alkalmazás létrehozása

1. A Visual Studióban hozzon létre egy új konzol megoldást, és adja hozzá a következő csomagokat. 

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

2. Változók létrehozása az előfizetési kulcs és a végpontot. Alább az URI-t is használhatja az ink-felismerés van. Azt hozzá lesznek fűzve a szolgáltatásvégpont később az API-hoz létre a kérelem URL-címe.

    ```csharp
    // Replace the subscriptionKey string with your valid subscription key.
    const string subscriptionKey = "YOUR_SUBSCRIPTION_KEY";

    // Replace the dataPath string with a path to the JSON formatted ink stroke data.
    const string dataPath = @"PATH-TO-INK-STROKE-DATA"; 

    // URI information for ink recognition:
    const string endpoint = "https://api.cognitive.microsoft.com";
    const string inkRecognitionUrl = "/inkrecognizer/v1.0-preview/recognize";
    ```

## <a name="create-a-function-to-send-requests"></a>Kérelmek küldése függvény létrehozása

1. Hozzon létre egy új aszinkron függvényt nevű `Request` átkerül a fent létrehozott változókat.

2. Állítsa be az ügyfél biztonsági protokoll és fejléc-információk használata egy `HttpClient` objektum. Az előfizetési kulcs, adja hozzá a `Ocp-Apim-Subscription-Key` fejléc. Ezután hozzon létre egy `StringContent` objektum a kéréshez.
 
3. A kérelem küldése `PutAsync()`. Ha a kérelem sikeres, a választ adja vissza.  
    
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

## <a name="send-an-ink-recognition-request"></a>-Ink felismerés kérés küldése

1. Hozzon létre egy új függvényt nevű `recognizeInk()`. A kérelem hozhatnak létre, és küldje el meghívásával a `Request()` függvény a végponthoz, előfizetési kulcsot, az API-t, és a digitális ink körvonal URL-CÍMÉT.

2. A JSON-objektum deszerializálása, és jegyezze fel a konzolhoz. 
    
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

## <a name="load-your-digital-ink-data"></a>A digitális ink-adatok betöltése

Hozzon létre egy függvényt, nevű `LoadJson()` betölteni a szabadkézi JSON-fájlt. Használja a `StreamReader` és `JsonTextReader` hozhat létre egy `JObject` és küldje vissza.
    
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

## <a name="send-the-api-request"></a>Az API-kérelem küldése

1. A fő metódus az alkalmazás betöltése függvény a fent létrehozott a JSON-adatokat. 

2. Hívja a `recognizeInk()` fent létrehozott függvényt. Használat `System.Console.ReadKey()` megnyitva, a konzolablakban az alkalmazás futtatása után.
    
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

Futtassa az alkalmazást. A sikeres válasz JSON formátumban. A JSON-válasz is megtalálhatja a [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-response.json).


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [REST API – referencia](https://go.microsoft.com/fwlink/?linkid=2089907)


A tinta Recognition API működését egy digitális szabadkézi alkalmazás megtekintéséhez tekintse meg a következő minta alkalmazásokat a Githubon:
* [C# és Univerzális Windows-platform (Universal Windows Platform, UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# és Windows megjelenítési alaprendszer (Windows Presentation Foundation, WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [JavaScript-webböngészőalkalmazás](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java- és Android-mobilalkalmazás](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift- és iOS-mobilalkalmazás](https://go.microsoft.com/fwlink/?linkid=2089805)
