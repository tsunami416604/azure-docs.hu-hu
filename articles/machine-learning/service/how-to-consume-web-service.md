---
title: Ügyfél létrehozása a telepített webszolgáltatás használatára
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan használhat olyan webszolgáltatást, amely akkor jött létre, amikor egy modellt Azure Machine Learning modellel telepítettek. A webszolgáltatás REST API tesz elérhetővé. Hozzon létre ügyfeleket ehhez az API-hoz az Ön által választott programozási nyelv használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 08/15/2019
ms.custom: seodec18
ms.openlocfilehash: 5f57be9a1fc5d260ce580b969c8f92c87aea17a8
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70279124"
---
# <a name="consume-an-azure-machine-learning-model-deployed-as-a-web-service"></a>Az Azure Machine Learning-modellek webszolgáltatásként üzembe helyezett felhasználása

REST API-t üzembe helyezése az Azure Machine Learning-modellek webszolgáltatásként hoz létre. Adatokat küldeni az API-t, és a modell által visszaadott az előrejelzést kapni. Ebből a dokumentumból megtudhatja, hogyan hozhat létre ügyfeleket a webszolgáltatáshoz a, a go, a Java és a Python használatával C#.

Webszolgáltatást akkor hozhat létre, amikor lemezképet telepít Azure Container Instances, Azure Kubernetes Service vagy Field-programozható Gate-tömbökbe (FPGA). A lemezképeket regisztrált modellből és pontozási fájlokból hozza létre. A webszolgáltatásokhoz való hozzáféréshez használt URI-t a [Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)-val kéri le. Ha a hitelesítés engedélyezve van, az SDK használatával is beolvashatja a hitelesítési kulcsokat vagy jogkivonatokat.

A Machine learning-webszolgáltatást használó ügyfelek létrehozásának általános munkafolyamata a következő:

1. A kapcsolódási adatok beszerzéséhez használja az SDK-t.
1. Határozza meg a modell által használt kérelem-adattípust.
1. Hozzon létre egy alkalmazást, amely meghívja a webszolgáltatást.

> [!TIP]
> A dokumentumban szereplő példákat manuálisan hozza létre a OpenAPI (hencegés) specifikációk használata nélkül. Ha engedélyezte a OpenAPI-specifikációt a központi telepítéshez, olyan eszközöket használhat, mint például a [hencegés-CODEGEN](https://github.com/swagger-api/swagger-codegen) a szolgáltatáshoz tartozó ügyféloldali kódtárak létrehozásához.

## <a name="connection-information"></a>Kapcsolatadatok

> [!NOTE]
> A webszolgáltatási információk beszerzéséhez használja a Azure Machine Learning SDK-t. Ez a Python SDK-t. A szolgáltatáshoz bármilyen nyelven létrehozhat egy ügyfelet.

A [azureml. Core. webszolgáltatási](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py) osztály a-ügyfél létrehozásához szükséges információkat tartalmazza. Az ügyfélalkalmazások `Webservice` létrehozásához a következő tulajdonságok hasznosak:

* `auth_enabled`– Ha a kulcsos hitelesítés engedélyezve `True`van, `False`egyéb esetben.
* `token_auth_enabled`– Ha engedélyezve van a jogkivonat- `True`hitelesítés, `False`más esetben.
* `scoring_uri` – A REST API-cím.
* `swagger_uri`– A OpenAPI-specifikáció címe. Ez az URI akkor érhető el, ha engedélyezte az automatikus séma létrehozását. További információ: [modellek üzembe helyezése a Azure Machine learning szolgáltatással](how-to-deploy-and-where.md#schema).

Az üzembe helyezett webszolgáltatások ezen információk lekérése egy három módja van:

* Amikor telepít egy modell, egy `Webservice` a szolgáltatással kapcsolatos információkat ad vissza objektumot:

    ```python
    service = Webservice.deploy_from_model(name='myservice',
                                           deployment_config=myconfig,
                                           models=[model],
                                           image_config=image_config,
                                           workspace=ws)
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

* Használhat `Webservice.list` kérdezheti le az üzembe helyezett webszolgáltatások munkaterületét modellek esetén. Visszaadott adatok leszűkítése szűrőt is hozzáadhat. További információ arról, hogy mit lehet szűrni [. a webszolgáltatások listáját](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py) ismertető dokumentációban talál további információt.

    ```python
    services = Webservice.list(ws)
    print(services[0].scoring_uri)
    print(services[0].swagger_uri)
    ```

* Ha ismeri a telepített szolgáltatás nevét, létrehozhat egy új példányt `Webservice`, és paraméterként megadhatja a munkaterület és a szolgáltatás nevét. Az új objektum tartalmazza a telepített szolgáltatással kapcsolatos információkat.

    ```python
    service = Webservice(workspace=ws, name='myservice')
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

### <a name="authentication-for-services"></a>Szolgáltatások hitelesítése

A Azure Machine Learning két módszert biztosít a webszolgáltatásokhoz való hozzáférés vezérlésére.

|Hitelesítési módszer|ACI|AKS|
|---|---|---|
|Kulcs|Alapértelmezés szerint letiltva| Alapértelmezés szerint engedélyezve|
|Jogkivonat| Nem érhető el| Alapértelmezés szerint letiltva |

Egy kulccsal vagy jogkivonattal védett szolgáltatásnak küldött kérelem küldésekor az __engedélyezési__ fejléc használatával adja át a kulcsot vagy a jogkivonatot. A kulcsot vagy jogkivonatot úgy kell `Bearer <key-or-token>`formázni, `<key-or-token>` hogy hol található a kulcs vagy a jogkivonat értéke.

#### <a name="authentication-with-keys"></a>Hitelesítés kulcsokkal

Ha engedélyezi a hitelesítést a központi telepítéshez, automatikusan létrehozza a hitelesítési kulcsokat.

* A hitelesítés alapértelmezés szerint engedélyezve van az Azure Kubernetes Service-ben való üzembe helyezéskor.
* A hitelesítés alapértelmezés szerint le van tiltva, amikor Azure Container Instances telepíti.

A hitelesítés vezérléséhez használja a `auth_enabled` paramétert a központi telepítés létrehozásakor vagy frissítésekor.

Ha a hitelesítés engedélyezve van, használhatja a `get_keys` metódusának segítéségével lekérheti az egy elsődleges és másodlagos hitelesítési kulcs:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Ha a kulcs újragenerálása van szüksége, használja a [ `service.regen_key` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py).

#### <a name="authentication-with-tokens"></a>Hitelesítés jogkivonatokkal

Ha engedélyezi a jogkivonat-hitelesítést egy webszolgáltatáshoz, a felhasználónak meg kell adnia egy Azure Machine Learning JWT tokent a webszolgáltatásnak az eléréséhez. 

* Alapértelmezés szerint a jogkivonat-hitelesítés le van tiltva az Azure Kubernetes Service-ben való üzembe helyezéskor.
* A jogkivonat-hitelesítés nem támogatott a Azure Container Instances való telepítésekor.

A jogkivonat-hitelesítés vezérléséhez használja `token_auth_enabled` a paramétert a központi telepítés létrehozásakor vagy frissítésekor.

Ha engedélyezve van a jogkivonat-hitelesítés, a `get_token` metódussal kérheti le a tulajdonosi jogkivonatot és a jogkivonatok lejárati idejét:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> A jogkivonat `refresh_by` időpontját követően új jogkivonatot kell kérnie. 

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

Például a modell a [Train belül notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) például egy 10 számból álló tömböt vár. A példában szereplő pontozási szkript létrehoz egy NumPy tömböt a kérelemből, és átadja a modellnek. Az alábbi példa bemutatja az adatok, ez a szolgáltatás vár:

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

### <a name="binary-data"></a>Bináris adatok

További információ a szolgáltatásban található bináris adatok támogatásának engedélyezéséről: [bináris adatok](how-to-deploy-and-where.md#binary).

### <a name="cross-origin-resource-sharing-cors"></a>Több eredetű erőforrás-megosztás (CORS)

Az CORS-támogatás szolgáltatásban való engedélyezésével kapcsolatos információkért lásd: a [több eredetű erőforrás-megosztás](how-to-deploy-and-where.md#cors).

## <a name="call-the-service-c"></a>Meghívja a szolgáltatást (C#)

Ez a példa bemutatja, hogyan használható C# a létrehozott webes szolgáltatás hívása a [Train belül notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) példa:

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
            // Set the scoring URI and authentication key or token
            string scoringUri = "<your web service URI>";
            string authKey = "<your key or token>";

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

Ez a példa bemutatja, hogyan használható a Go a létrehozott webszolgáltatás meghívandó a [Train belül notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) példa:

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
// Set to the authentication key or token (if any) for your service
var authKey string = "<your key or token>"

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

Ez a példa bemutatja, hogyan használható a Java meghívni a webszolgáltatás alapján létrehozott a [Train belül notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) példa:

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
        // If using authentication, replace with the auth key or token
        String key = "<your key or token>";
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

Ez a példa bemutatja, hogyan használhatja a Pythont a létrehozott webes szolgáltatás hívása a [Train belül notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) példa:

```python
import requests
import json

# URL for the web service
scoring_uri = '<your web service URI>'
# If the service is authenticated, set the key or token
key = '<your key or token>'

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
headers = {'Content-Type': 'application/json'}
# If authentication is enabled, set the authorization header
headers['Authorization'] = f'Bearer {key}'

# Make the request and display the response
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

A következő JSON-dokumentumot kapott eredmények hasonlók:

```JSON
[217.67978776218715, 224.78937091757172]
```

## <a name="consume-the-service-from-power-bi"></a>A szolgáltatás felhasználása Power BI

A Power BI támogatja a Azure Machine Learning webszolgáltatások felhasználását, hogy az előrejelzésekkel gazdagítsa Power BI az adatmennyiséget. 

Ha olyan webszolgáltatást szeretne előállítani, amelyet a Power BIban való használathoz támogat, a sémának támogatnia kell a Power BI számára szükséges formátumot. [Megtudhatja, hogyan hozhat létre Power bi által támogatott sémát](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where#example-entry-script).

A webszolgáltatás üzembe helyezése után Power BI adatfolyamok. [Megtudhatja, hogyan használhat Azure Machine learning webszolgáltatást Power BIból](https://docs.microsoft.com/power-bi/service-machine-learning-integration).

## <a name="next-steps"></a>További lépések

A Python és a Deep learning modellek valós idejű pontozására szolgáló hivatkozási architektúra megtekintéséhez nyissa meg az [Azure Architecture centert](/azure/architecture/reference-architectures/ai/realtime-scoring-python).
