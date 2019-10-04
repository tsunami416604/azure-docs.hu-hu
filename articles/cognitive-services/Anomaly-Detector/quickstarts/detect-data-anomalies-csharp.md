---
title: 'Gyors útmutató: Az idősoros adataiban észlelt rendellenességek észlelése az anomália-detektor REST API ésC#'
titleSuffix: Azure Cognitive Services
description: A rendellenesség-Kiderítő API használatával az adatsorozatban lévő rendellenességeket kötegként vagy adatfolyamként lehet érzékelni.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 07/26/2019
ms.author: aahi
ms.openlocfilehash: 97efa5cd91646809178d685ca51e29ef2fda7c0d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564736"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-c"></a>Gyors útmutató: Az idősoros adataiban észlelt rendellenességek észlelése az anomália-detektor REST API ésC# 

Ezzel a rövid útmutatóval megkezdheti a anomáliák-Kiderítő API két észlelési módjának használatát az idősorozat-adataiban észlelt rendellenességek észlelésére. Ez C# az alkalmazás két, JSON-formátumú idősorozat-adatokat tartalmazó API-kérelmet küld, és lekéri a válaszokat.

| API-kérelem                                        | Alkalmazás kimenete                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Rendellenességek észlelése kötegként                        | Az idősorozat-adatpontokhoz tartozó anomália-állapotot (és az egyéb adatmennyiségeket) tartalmazó JSON-válasz, valamint az észlelt rendellenességek helyei. |
| A legutóbbi adatpont anomália állapotának észlelése | Az idősorozat-adatként a legutóbbi adatponthoz tartozó anomália-állapotot (és egyéb adatértékeket) tartalmazó JSON-válasz.                                                                                                                                         |

 Az alkalmazás beírásakor az C#API egy REST-alapú webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel.

## <a name="prerequisites"></a>Előfeltételek

- A [Visual Studio 2017-es vagy újabb](https://visualstudio.microsoft.com/downloads/)verziójának bármely kiadása,

- A [Json.NET](https://www.newtonsoft.com/json) keretrendszer, amely NuGet-csomagként letölthető. A Newtonsoft. JSON telepítése NuGet-csomagként a Visual Studióban:
    
    1. Kattintson a jobb gombbal a projektre **megoldáskezelő**.
    2. Válassza a **NuGet-csomagok kezelése**lehetőséget.
    3. Keresse meg a *Newtonsoft. JSON* fájlt, és telepítse a csomagot.

- Ha Linux/MacOS rendszert használ, akkor az alkalmazás a [mono](https://www.mono-project.com/)használatával futtatható.

- Idősorozat-adatpontokat tartalmazó JSON-fájl. A rövid útmutatóhoz tartozó példa a githubon érhető [](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json)el.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

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
    ```

2. Hozzon létre változókat az előfizetési kulcshoz és a végponthoz. Az alábbi URI-k használhatók a anomáliák észleléséhez. Az API-kérelmek URL-címeinek létrehozásához ezeket a rendszer később hozzáfűzi a szolgáltatási végponthoz.

    |Észlelési módszer  |URI  |
    |---------|---------|
    |Kötegelt észlelés    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Észlelés a legújabb adatponton     | `/anomalydetector/v1.0/timeseries/last/detect`        |
    
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

## <a name="create-a-function-to-send-requests"></a>Függvény létrehozása a kérelmek küldéséhez

1. Hozzon létre egy nevű `Request` új aszinkron függvényt, amely a fent létrehozott változókat veszi fel.

2. Állítsa be az ügyfél biztonsági protokollját és fejléc-információit egy `HttpClient` objektum használatával. Ügyeljen arra, hogy hozzáadja az előfizetési kulcsot `Ocp-Apim-Subscription-Key` a fejléchez. Ezután hozzon `StringContent` létre egy objektumot a kérelemhez.

3. Küldje el a kérelmet `PostAsync()`a szolgáltatással, majd adja vissza a választ.

```csharp
static async Task<string> Request(string apiAddress, string endpoint, string subscriptionKey, string requestData){
    using (HttpClient client = new HttpClient { BaseAddress = new Uri(apiAddress) }){
        System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12 | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls;
        client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

        var content = new StringContent(requestData, Encoding.UTF8, "application/json");
        var res = await client.PostAsync(endpoint, content);
        return await res.Content.ReadAsStringAsync();
    }
}
```

## <a name="detect-anomalies-as-a-batch"></a>Rendellenességek észlelése kötegként

1. Hozzon létre egy nevű `detectAnomaliesBatch()`új függvényt. Hozza létre a kérést, és küldje el `Request()` úgy, hogy meghívja a függvényt a végponttal, az előfizetési kulccsal, a köteg anomália észlelésének URL-címével és az idősorozat adataival.

2. Deszerializálja a JSON-objektumot, és írja a konzolba.

3. Ha a válasz tartalmaz `code` mezőt, nyomtassa ki a hibakódot és a hibaüzenetet. 

4. Ellenkező esetben keresse meg a rendellenességek pozícióit az adatkészletben. A válasz `isAnomaly` mezője logikai értékeket tartalmaz, amelyek mindegyike azt jelzi, hogy az adatpont egy anomália-e. Alakítsa át ezt a karakterlánc-tömbre a Response objektum `ToObject<bool[]>()` függvényével. Ismételje meg a tömböt, és nyomtassa ki bármelyik `true` érték indexét. Ezek az értékek a rendellenes adatpontok indexének felelnek meg, ha vannak ilyenek.

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

    if (jsonObj["code"] != null){
        System.Console.WriteLine($"Detection failed. ErrorCode:{jsonObj["code"]}, ErrorMessage:{jsonObj["message"]}");
    }
    else{
        bool[] anomalies = jsonObj["isAnomaly"].ToObject<bool[]>();
        System.Console.WriteLine("\nAnomalies detected in the following data positions:");
        for (var i = 0; i < anomalies.Length; i++){
            if (anomalies[i])
            {
                System.Console.Write(i + ", ");
            }
        }
    }
}
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>A legutóbbi adatpont anomália állapotának észlelése

1. Hozzon létre egy nevű `detectAnomaliesLatest()`új függvényt. Hozza létre a kérést, és küldje el `Request()` úgy, hogy meghívja a függvényt a végponttal, az előfizetési kulccsal, a legutóbbi pont anomália-észlelésének URL-címével és az idősorozat adataival

2. Deszerializálja a JSON-objektumot, és írja a konzolba.

```csharp
static void detectAnomaliesLatest(string requestData){
    System.Console.WriteLine("\n\nDetermining if latest data point is an anomaly");
    var result = Request(
        endpoint,
        latestPointDetectionUrl,
        subscriptionKey,
        requestData).Result;

    dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result);
    System.Console.WriteLine(jsonObj);
}
```

## <a name="load-your-time-series-data-and-send-the-request"></a>Töltse be az idősorozat adatait, és küldje el a kérést

1. Az alkalmazás fő metódusában töltse be a JSON idősorozat-adatait az- `File.ReadAllText()`val. 

2. Hívja meg a fent létrehozott anomália-észlelési funkciókat. Ezzel `System.Console.ReadKey()` a paranccsal megtarthatja a konzolablak megnyitását az alkalmazás futtatása után.

```csharp
static void Main(string[] args){

    var requestData = File.ReadAllText(dataPath);

    detectAnomaliesBatch(requestData);
    detectAnomaliesLatest(requestData);

    System.Console.ReadKey();
}
```

### <a name="example-response"></a>Példaválasz

A sikeres válaszokat JSON formátumban adja vissza a rendszer. Az alábbi hivatkozásokra kattintva megtekintheti a JSON-választ a GitHubon:
* [Példa a Batch észlelési válaszára](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Példa a legutóbbi pont észlelési válaszára](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [REST API – referencia](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)
