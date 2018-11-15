---
title: Webszolgáltatások üzembe helyezéséhez – az Azure Machine Learning használata
description: Ismerje meg, hogyan üzembe helyezése az Azure Machine Learning-modell által létrehozott webszolgáltatások használata. Az Azure Machine Learning-modellek üzembe helyezése egy webszolgáltatás, amely elérhetővé teszi a REST API-t hoz létre. Az ügyfelek számára az API-t az Ön által választott programozási nyelvet is létrehozhat. Ebből a dokumentumból megtudhatja, hogyan érheti el az API-ban a Python használatával és C#.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: raymondl
author: raymondlaghaeian
ms.reviewer: larryfr
ms.date: 10/30/2018
ms.openlocfilehash: 75faf344c64dc330a98b836a8852b42531645c49
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51685174"
---
# <a name="consume-an-azure-machine-learning-model-deployed-as-a-web-service"></a>Az Azure Machine Learning-modellek webszolgáltatásként üzembe helyezett felhasználása

REST API-t üzembe helyezése az Azure Machine Learning-modellek webszolgáltatásként hoz létre. Adatokat küldeni az API-t, és a modell által visszaadott az előrejelzést kapni. Ebből a dokumentumból megtudhatja, hogyan hozhat létre webes szolgáltatást használó ügyfelek C#, Go, a Java és Python.

Webszolgáltatás-rendszerképet egy Azure-Tárolópéldányon, az Azure Kubernetes Service-ben vagy a Project Brainwave (mező programmable gate arrays) központi telepítésekor jön létre. A regisztrált modellek és a pontozófájlt lemezképek jönnek létre. Egy webes szolgáltatás eléréséhez használt URI-ja használatával lehet beolvasni a [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Ha engedélyezve van a hitelesítés, az SDK-t is használhatja a hitelesítési kulcsok beolvasása.

Az általános munkafolyamatát, amikor egy Machine Learning webszolgáltatás használó ügyfél létrehozása a következő:

1. A kapcsolati adatok lekéréséhez az SDK használatával
1. A modell által használt kérelem adatok típusának meghatározása
1. Hozzon létre egy alkalmazást, amely meghívja a webszolgáltatást

## <a name="connection-information"></a>Kapcsolatadatok

> [!NOTE]
> Az Azure Machine Learning SDK kéri le a webes adatait. Ez a Python SDK-t. A webes szolgáltatások adatainak beolvasásához használatos, miközben használhatja bármilyen nyelven létrehozni az ügyfelet a szolgáltatás.

A kapcsolat webalkalmazás adatait az Azure Machine Learning SDK használatával lekérhetők. A [azureml.core.Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py) osztály egy ügyfél létrehozásához szükséges információkat biztosít. A következő `Webservice` tulajdonságok, amelyek hasznosak lehetnek, amikor egy ügyfélalkalmazás létrehozása:

* `auth_enabled` – Ha engedélyezve van a hitelesítés, `True`; ellenkező esetben `False`.
* `scoring_uri` – A REST API-cím.

Az üzembe helyezett webszolgáltatások ezen információk lekérése egy három módja van:

* Amikor telepít egy modell, egy `Webservice` a szolgáltatással kapcsolatos információkat ad vissza objektumot:

    ```python
    service = Webservice.deploy_from_model(name='myservice',
                                           deployment_config=myconfig,
                                           models=[model],
                                           image_config=image_config,
                                           workspace=ws)
    print(service.scoring_uri)
    ```

* Használhat `Webservice.list` kérdezheti le az üzembe helyezett webszolgáltatások munkaterületét modellek esetén. Visszaadott adatok leszűkítése szűrőt is hozzáadhat. További információ a szűrhetik, lásd: a [Webservice.list](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py#list) referenciák dokumentációiba.

    ```python
    services = Webservice.list(ws)
    print(services[0].scoring_uri)
    ```

* Ha ismeri a telepített szolgáltatás nevére, létrehozhat egy új példányát `Webservice` , és adja meg a munkaterület és a szolgáltatás nevét meg paraméterként. Az új objektum tartalmazza a telepített szolgáltatással kapcsolatos információkat.

    ```python
    service = Webservice(workspace=ws, name='myservice')
    print(service.scoring_uri)
    ```

### <a name="authentication-key"></a>Hitelesítési kulcs

Hitelesítési kulcsok automatikusan jönnek létre, amikor a hitelesítés engedélyezve van a központi telepítés.

* Hitelesítés __alapértelmezés szerint engedélyezett__ való üzembe helyezés esetén __Azure Kubernetes Service__.
* Hitelesítés __alapértelmezés szerint le van tiltva__ való üzembe helyezés esetén __az Azure container Instances__.

Hitelesítés beállításához használja a `auth_enabled` paraméter létrehozásakor vagy frissítésekor egy központi telepítést.

Ha a hitelesítés engedélyezve van, használhatja a `get_keys` metódusának segítéségével lekérheti az egy elsődleges és másodlagos hitelesítési kulcs:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Ha a kulcs újragenerálása van szüksége, használja a [ `service.regen_key` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#regen-key).

## <a name="request-data"></a>Kérelem adatai

A REST API-t vár egy JSON-dokumentumot, az alábbi struktúra használatával kell a kérés törzse:

```json
{
    "data":
        [
            <model-specific-data-structure>
        ]
}
```

> [!IMPORTANT]
> Az adatok struktúráját milyen a pontozó szkript és a szolgáltatás várt modellek egyeznie kell. A pontozó szkript előfordulhat, hogy módosíthatja az adatokat a modell való továbbítás előtt.

Például a modell a [Train belül notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/01.getting-started/01.train-within-notebook) például egy 10 számból álló tömböt vár. Ebben a példában a pontozó szkript létrehoz egy Numpy tömböt a kérelemből, és átadja azokat a modellbe. Az alábbi példa bemutatja az adatok, ez a szolgáltatás vár:

```json
{
    "data": 
        [
            [
                0.0199132141783263, 
                0.0506801187398187, 
                0.104808689473925, 
                0.0700725447072635, 
                -0.0359677812752396, 
                -0.0266789028311707, 
                -0.0249926566315915, 
                -0.00259226199818282, 
                0.00371173823343597, 
                0.0403433716478807
            ]
        ]
}
``` 

A webszolgáltatás egy kérelem több adatkészletek tud fogadni. Egy JSON-dokumentum, amely egy tömböt adott vissza.

## <a name="call-the-service-c"></a>Meghívja a szolgáltatást (C#)

Ez a példa bemutatja, hogyan használható C# a létrehozott webes szolgáltatás hívása a [Train belül notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/01.getting-started/01.train-within-notebook) példa:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net.Http;
using System.Net.Http.Headers;
using Newtonsoft.Json;

namespace MLWebServiceClient
{
    // The data structure expected by the service
    internal class InputData
    {
        [JsonProperty("data")]
        // The service used by this example expects an array containing
        //   one or more arrays of doubles
        internal double[,] data;
    }
    class Program
    {
        static void Main(string[] args)
        {
            // Set the scoring URI and authentication key
            string scoringUri = "<your web service URI>";
            string authKey = "<your key>";

            // Set the data to be sent to the service.
            // In this case, we are sending two sets of data to be scored.
            InputData payload = new InputData();
            payload.data = new double[,] {
                {
                    0.0199132141783263,
                    0.0506801187398187,
                    0.104808689473925,
                    0.0700725447072635,
                    -0.0359677812752396,
                    -0.0266789028311707,
                    -0.0249926566315915,
                    -0.00259226199818282,
                    0.00371173823343597,
                    0.0403433716478807
                },
                {
                    -0.0127796318808497, 
                    -0.044641636506989, 
                    0.0606183944448076, 
                    0.0528581912385822, 
                    0.0479653430750293, 
                    0.0293746718291555, 
                    -0.0176293810234174, 
                    0.0343088588777263, 
                    0.0702112981933102, 
                    0.00720651632920303
                }
            };

            // Create the HTTP client
            HttpClient client = new HttpClient();
            // Set the auth header. Only needed if the web service requires authentication.
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authKey);

            // Make the request
            try {
                var request = new HttpRequestMessage(HttpMethod.Post, new Uri(scoringUri));
                request.Content = new StringContent(JsonConvert.SerializeObject(payload));
                request.Content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                var response = client.SendAsync(request).Result;
                // Display the response from the web service
                Console.WriteLine(response.Content.ReadAsStringAsync().Result);
            }
            catch (Exception e)
            {
                Console.Out.WriteLine(e.Message);
            }
        }
    }
}
```

A következő JSON-dokumentumot kapott eredmények hasonlók:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-go"></a>Meghívja a szolgáltatást (Futtatás)

Ez a példa bemutatja, hogyan használható a Go a létrehozott webszolgáltatás meghívandó a [Train belül notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/01.getting-started/01.train-within-notebook) példa:

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
)

// Features for this model are an array of decimal values
type Features []float64

// The web service input can accept multiple sets of values for scoring
type InputData struct {
    Data []Features `json:"data",omitempty`
}

// Define some example data
var exampleData = []Features{
    []float64{
        0.0199132141783263, 
        0.0506801187398187, 
        0.104808689473925, 
        0.0700725447072635, 
        -0.0359677812752396, 
        -0.0266789028311707, 
        -0.0249926566315915, 
        -0.00259226199818282, 
        0.00371173823343597, 
        0.0403433716478807,
    },
    []float64{
        -0.0127796318808497, 
        -0.044641636506989, 
        0.0606183944448076, 
        0.0528581912385822, 
        0.0479653430750293, 
        0.0293746718291555, 
        -0.0176293810234174, 
        0.0343088588777263, 
        0.0702112981933102, 
        0.00720651632920303,
    },
}

// Set to the URI for your service
var serviceUri string = "<your web service URI>"
// Set to the authentication key (if any) for your service
var authKey string = "<your key>"

func main() {
    // Create the input data from example data
    jsonData := InputData{
        Data: exampleData,
    }
    // Create JSON from it and create the body for the HTTP request
    jsonValue, _ := json.Marshal(jsonData)
    body := bytes.NewBuffer(jsonValue)

    // Create the HTTP request
    client := &http.Client{}
    request, err := http.NewRequest("POST", serviceUri, body)
    request.Header.Add("Content-Type", "application/json")

    // These next two are only needed if using an authentication key
    bearer := fmt.Sprintf("Bearer %v", authKey)
    request.Header.Add("Authorization", bearer)

    // Send the request to the web service
    resp, err := client.Do(request)
    if err != nil {
        fmt.Println("Failure: ", err)
    }

    // Display the response received
    respBody, _ := ioutil.ReadAll(resp.Body)
    fmt.Println(string(respBody))
}
```

A következő JSON-dokumentumot kapott eredmények hasonlók:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-java"></a>Meghívja a szolgáltatást (Java)

Ez a példa bemutatja, hogyan használható a Java meghívni a webszolgáltatás alapján létrehozott a [Train belül notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/01.getting-started/01.train-within-notebook) példa:

```java
import java.io.IOException;
import org.apache.http.client.fluent.*;
import org.apache.http.entity.ContentType;
import org.json.simple.JSONArray;
import org.json.simple.JSONObject;

public class App {
    // Handle making the request
    public static void sendRequest(String data) {
        // Replace with the scoring_uri of your service
        String uri = "<your web service URI>";
        // If using authentication, replace with the auth key
        String key = "<your key>";
        try {
            // Create the request
            Content content = Request.Post(uri)
            .addHeader("Content-Type", "application/json")
            // Only needed if using authentication
            .addHeader("Authorization", "Bearer " + key)
            // Set the JSON data as the body
            .bodyString(data, ContentType.APPLICATION_JSON)
            // Make the request and display the response.
            .execute().returnContent();
            System.out.println(content);
        }
        catch (IOException e) {
            System.out.println(e);
        }
    }
    public static void main(String[] args) {
        // Create the data to send to the service
        JSONObject obj = new JSONObject();
        // In this case, it's an array of arrays
        JSONArray dataItems = new JSONArray();
        // Inner array has 10 elements
        JSONArray item1 = new JSONArray();
        item1.add(0.0199132141783263);
        item1.add(0.0506801187398187);
        item1.add(0.104808689473925);
        item1.add(0.0700725447072635);
        item1.add(-0.0359677812752396);
        item1.add(-0.0266789028311707);
        item1.add(-0.0249926566315915);
        item1.add(-0.00259226199818282);
        item1.add(0.00371173823343597);
        item1.add(0.0403433716478807);
        // Add the first set of data to be scored
        dataItems.add(item1);
        // Create and add the second set
        JSONArray item2 = new JSONArray();
        item2.add(-0.0127796318808497);
        item2.add(-0.044641636506989);
        item2.add(0.0606183944448076);
        item2.add(0.0528581912385822);
        item2.add(0.0479653430750293);
        item2.add(0.0293746718291555);
        item2.add(-0.0176293810234174);
        item2.add(0.0343088588777263);
        item2.add(0.0702112981933102);
        item2.add(0.00720651632920303);
        dataItems.add(item2);
        obj.put("data", dataItems);

        // Make the request using the JSON document string
        sendRequest(obj.toJSONString());
    }
}
```

A következő JSON-dokumentumot kapott eredmények hasonlók:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-python"></a>Meghívja a szolgáltatást (Python)

Ez a példa bemutatja, hogyan használhatja a Pythont a létrehozott webes szolgáltatás hívása a [Train belül notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/01.getting-started/01.train-within-notebook) példa:

```python
import requests
import requests
import json

# URL for the web service
scoring_uri = '<your web service URI>'
# If the service is authenticated, set the key
key = '<your key>'

# Two sets of data to score, so we get two results back
data = {"data": 
            [
                [
                    0.0199132141783263, 
                    0.0506801187398187, 
                    0.104808689473925, 
                    0.0700725447072635, 
                    -0.0359677812752396, 
                    -0.0266789028311707, 
                    -0.0249926566315915, 
                    -0.00259226199818282, 
                    0.00371173823343597, 
                    0.0403433716478807
                ],
                [
                    -0.0127796318808497, 
                    -0.044641636506989, 
                    0.0606183944448076, 
                    0.0528581912385822, 
                    0.0479653430750293, 
                    0.0293746718291555, 
                    -0.0176293810234174, 
                    0.0343088588777263, 
                    0.0702112981933102, 
                    0.00720651632920303]
            ]
        }
# Convert to JSON string
input_data = json.dumps(data)

# Set the content type
headers = { 'Content-Type':'application/json' }
# If authentication is enabled, set the authorization header
headers['Authorization']=f'Bearer {key}'

# Make the request and display the response
resp = requests.post(scoring_uri, input_data, headers = headers)
print(resp.text)

```

A következő JSON-dokumentumot kapott eredmények hasonlók:

```JSON
[217.67978776218715, 224.78937091757172]
```

## <a name="next-steps"></a>További lépések

Most, hogy megtanulhatta, hogyan hozhat létre egy ügyfél egy üzembe helyezett modell, megtudhatja, hogyan [modell üzembe helyezése az IoT Edge-eszköz](how-to-deploy-to-iot.md).
