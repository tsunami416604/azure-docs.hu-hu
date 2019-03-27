---
title: 'Gyors útmutató: Rendellenességek észlelése az Anomáliadetektálási detector használatával REST API használatával az idősoros adatokat, és C# |} A Microsoft Docs'
description: Az Anomáliadetektálási detector használatával API segítségével észlelheti a rendellenességeket az adatsorozat egy kötegelt vagy a streamelt adatokon.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 7aa171a49ea03769c3ecbb5d35ae31ac6fae052e
ms.sourcegitcommit: fbfe56f6069cba027b749076926317b254df65e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58473190"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-c"></a>Gyors útmutató: Rendellenességek észlelése az Anomáliadetektálási detector használatával REST API használatával az idősoros adatokat, ésC# 

Ez a rövid útmutató segítségével indítsa el a rendellenességek észlelése az idősoros adatokat az Anomáliadetektálási detector használatával API két észlelési mód használatával. Ez C# alkalmazás két API-kérések tartalmazó JSON-formátumú idősoros adatokat küld, és lekérdezi a válaszokat.

| API-kérelem                                        | Alkalmazás kimenete                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Kötegelt feladatként rendellenességek észlelése                        | Az idősorozat-adatokat, és minden észlelt rendellenességek igazítja adatpontok anomáliadetektálási állapota (és más adatok) tartalmazó JSON-választ. |
| A legújabb adatpont anomáliadetektálási állapotának észlelése | Az anomáliadetektálási állapota (és egyéb adatokat) az idősorozat-adatok a legutóbbi adatok pontját tartalmazó JSON-választ.                                                                                                                                         |

 Bár ez az alkalmazás nyelven van megírva C#, az API egy REST-alapú webszolgáltatás szinte bármelyik programozási nyelvével kompatibilis.

## <a name="prerequisites"></a>Előfeltételek

- A [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) bármely kiadása.
- [Newtonsoft.Json](https://www.newtonsoft.com/json)
    - Telepítése a Newtonsoft.Json NuGet-csomagként a Visual studióban:
        1. Kattintson a jobb gombbal a **megoldás Manager**
        2. Kattintson a **NuGet-csomagok kezelése...**
        3. Keresse meg `Newtonsoft.Json` és telepítse a csomagot
- Linux/MacOS rendszeren használja, ha az alkalmazás képes futtatni használatával [Mono](http://www.mono-project.com/).

- A JSON fájlt tartalmazó idősorozat-adatok mutat. A példaadatokat ebben a rövid útmutatóban található [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

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
    ```

2. Változók létrehozása az előfizetési kulcs és a végpontot. Az alábbiakban az URI-k rendellenességek észlelése is használhat. Ezek hozzá lesznek fűzve a később, hozhat létre az API-Szolgáltatásvégpont URL-címeket.

    |Észlelési módszer  |URI  |
    |---------|---------|
    |Batch-észlelés    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |A legújabb adatok ponton észlelése     | `/anomalydetector/v1.0/timeseries/last/detect`        |
    
    ```csharp
    // Replace the subscriptionKey string value with your valid subscription key.
    const string subscriptionKey = "[YOUR_SUBSCRIPTION_KEY]";
    // Replace the endpoint URL with the correct one for your subscription. 
    // Your endpoint can be found in the Azure portal. For example: https://westus2.api.cognitive.microsoft.com
    const string endpoint = "[YOUR_ENDPOINT_URL]";
    // Replace the dataPath string with a path to the JSON formatted time series data.
    const string dataPath = "[PATH_TO_TIME_SERIES_DATA]";
    const string latestPointDetectionUrl = "/anomalydetector/v1.0/timeseries/last/detect";
    const string batchDetectionUrl = "/anomalydetector/v1.0/timeseries/entire/detect";
    ```

## <a name="create-a-function-to-send-requests"></a>Kérelmek küldése függvény létrehozása

1. Hozzon létre egy új aszinkron függvényt nevű `Request` átkerül a fent létrehozott változókat.

2. Állítsa be az ügyfél biztonsági protokoll és fejléc-információk használata egy `HttpClient` objektum. Az előfizetési kulcs, adja hozzá a `Ocp-Apim-Subscription-Key` fejléc. Ezután hozzon létre egy `StringContent` objektum a kéréshez.
 
3. A kérelem küldése `PostAsync()`. Ha a kérelem sikeres, a választ adja vissza.  

```csharp
static async Task<string> Request(string baseAddress, string endpoint, string subscriptionKey, string requestData){
    using (HttpClient client = new HttpClient { BaseAddress = new Uri(baseAddress) }){
        System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12 | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls;
        client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

        var content = new StringContent(requestData, Encoding.UTF8, "application/json");
        var res = await client.PostAsync(endpoint, content);
        if (res.IsSuccessStatusCode){
            return await res.Content.ReadAsStringAsync();
        }
        else{
            return $"ErrorCode: {res.StatusCode}";
        }
    }
}
```

## <a name="detect-anomalies-as-a-batch"></a>Kötegelt feladatként rendellenességek észlelése

1. Hozzon létre egy új függvényt nevű `detectAnomaliesBatch()`. A kérelem hozhatnak létre, és küldje el meghívásával a `Request()` függvényt a végponthoz, előfizetési kulcs, anomáliadetektálás batch URL-CÍMÉT és az idősorozat-adatok.

2. A JSON-objektum deszerializálása, és jegyezze fel a konzolhoz.

3. A rendellenességek észlelését a pozíciók található adatkészlet. A válasz `isAnomaly` mezőt tartalmaz, amelyek azt jelzi, hogy egy adatpont anomáliát logikai értékek tömbje. Konvertálja az egy karakterlánc-tömbben, a válasz objektummal `ToObject<bool[]>()` függvény.

4. Iteráció a tömbben, és nyomtassa ki az index minden `true` értékeket. Ha találhatók esetleges rendellenes adatpontok index ezeket az értékeket felelnek meg.

```csharp
static void detectAnomaliesBatch(string requestData){
    System.Console.WriteLine("Detecting anomalies as a batch");

    var result = Request(
        endpoint,
        batchDetectionUrl,
        subscriptionKey,
        requestData).Result;

    dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result);
    System.Console.WriteLine(jsonObj);

    bool[] anomalies = jsonObj["isAnomaly"].ToObject<bool[]>();
    System.Console.WriteLine("\n Anomalies detected in the following data positions:");
    for (var i = 0; i < anomalies.Length; i++) {
        if (anomalies[i]) {
            System.Console.Write(i + ", ");
        }
    }
}
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>A legújabb adatpont anomáliadetektálási állapotának észlelése

1. Hozzon létre egy új függvényt nevű `detectAnomaliesLatest()`. A kérelem hozhatnak létre, és küldje el meghívásával a `Request()` függvényt a végponthoz, előfizetési kulcsot, a legújabb pont anomáliadetektálás URL-CÍMÉT és az idősorozat-adatok.

2. A JSON-objektum deszerializálása, és jegyezze fel a konzolhoz. 

```csharp
static void detectAnomaliesLatest(string requestData){
    System.Console.WriteLine("\n\n Determining if latest data point is an anomaly");
    var result = Request(
        endpoint,
        latestPointDetectionUrl,
        subscriptionKey,
        requestData).Result;

    dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result);
    System.Console.WriteLine(jsonObj);
}
```

## <a name="load-your-time-series-data-and-send-the-request"></a>Az idősorozat-adatok betöltése, és a kérés küldése

1. A fő metódus az alkalmazás betöltése a JSON idősorozat-adatokat `File.ReadAllText()`. 

2. Hívja meg a fent létrehozott anomáliadetektálási észlelési funkciók. Használat `System.Console.ReadKey()` megnyitva, a konzolablakban az alkalmazás futtatása után.

```csharp
static void Main(string[] args){

    var requestData = File.ReadAllText(dataPath);

    detectAnomaliesBatch(requestData);
    detectAnomaliesLatest(requestData);

    System.Console.ReadKey();
}
```

### <a name="example-response"></a>Példaválasz

A sikeres válasz JSON formátumban. A JSON-válasz megtekintése a Githubon, az alábbi hivatkozásokra kattintva:
* [A batch észlelési példaválasz](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Legújabb pont észlelési példaválasz](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [REST API – referencia](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)