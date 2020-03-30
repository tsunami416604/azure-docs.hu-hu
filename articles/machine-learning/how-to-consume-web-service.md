---
title: Webszolgáltatásként telepített modell hez ügyfél létrehozása
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használható fel egy webszolgáltatás, amely akkor jött létre, amikor egy modell tlett üzembe az Azure Machine Learning-modell. A webszolgáltatás elérhetővé teszi a REST API-t. Hozzon létre ügyfeleket ehhez az API-hoz az Ön által választott programozási nyelv használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 01/07/2020
ms.custom: seodec18
ms.openlocfilehash: a86b8ddb59719db9bdaffea44aecd5428ad16834
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282664"
---
# <a name="consume-an-azure-machine-learning-model-deployed-as-a-web-service"></a>Webszolgáltatásként üzembe helyezett Azure Machine Learning-modell felhasználása
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Az Azure Machine Learning-modell webszolgáltatásként történő üzembe helyezése rest API-t hoz létre. Adatokat küldhet erre az API-ra, és fogadhatja a modell által visszaadott előrejelzést. Ebből a dokumentumból megtudhatja, hogyan hozhat létre ügyfeleket a webszolgáltatáshoz a C#, a Go, a Java és a Python használatával.

Hozzon létre egy webszolgáltatást, amikor egy lemezképet üzembe helyez az Azure Container Instances, az Azure Kubernetes service vagy a mező-programozható kaputömbök (FPGA). Képeket hozhat létre a regisztrált modellekből és a pontozási fájlokból. A webszolgáltatás eléréséhez használt URI-t az [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)használatával szerezheti be. Ha a hitelesítés engedélyezve van, az SDK-t is használhatja a hitelesítési kulcsok vagy tokenek lekéréséhez.

A gépi tanulási webszolgáltatást használó ügyfél létrehozásának általános munkafolyamata a következő:

1. Az SDK segítségével lejuthat a kapcsolatadatairól.
1. Határozza meg a modell által használt kérelemadatok típusát.
1. Hozzon létre egy alkalmazást, amely meghívja a webszolgáltatást.

> [!TIP]
> A jelen dokumentumban szereplő példák manuálisan jönnek létre az OpenAPI (Swagger) specifikációk használata nélkül. Ha engedélyezte az OpenAPI-specifikációt a központi telepítéshez, használhat olyan eszközöket, mint például [a swagger-codegen](https://github.com/swagger-api/swagger-codegen) a szolgáltatás ügyfélkódtárak létrehozásához.

## <a name="connection-information"></a>Kapcsolatadatai

> [!NOTE]
> Az Azure Machine Learning SDK segítségével a webszolgáltatás adatait. Ez egy Python SDK. Bármilyen nyelven létrehozhat egy ügyfelet a szolgáltatáshoz.

Az [azureml.core.Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py) osztály biztosítja az ügyfél létrehozásához szükséges információkat. Az `Webservice` ügyfélalkalmazások létrehozásához a következő tulajdonságok hasznosak:

* `auth_enabled`- Ha a kulcshitelesítés engedélyezve van, `True`; ellenkező `False`esetben .
* `token_auth_enabled`- Ha a token `True`hitelesítés engedélyezve van, ; ellenkező `False`esetben .
* `scoring_uri`- A REST API címe.
* `swagger_uri`- Az OpenAPI specifikáció címe. Ez az URI akkor érhető el, ha engedélyezte az automatikus sémagenerálást. További információ: Modellek telepítése az Azure Machine Learning használatával című [témakörben.](how-to-deploy-and-where.md)

Ezt az információt háromféleképpen kérheti le a telepített webszolgáltatásokhoz:

* Amikor telepít egy modellt, a rendszer a szolgáltatással kapcsolatos információkat ad vissza egy `Webservice` objektumnak:

    ```python
    service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
    service.wait_for_deployment(show_output = True)
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

* Segítségével `Webservice.list` lekérheti a munkaterületi modellek üzembe helyezett webszolgáltatásainak listáját. Szűrők hozzáadásával szűkítheti a visszaküldött információk listáját. A szűrésről a [Webservice.list](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py) dokumentációjában olvashat bővebben.

    ```python
    services = Webservice.list(ws)
    print(services[0].scoring_uri)
    print(services[0].swagger_uri)
    ```

* Ha ismeri a telepített szolgáltatás nevét, létrehozhat egy új `Webservice`példányt a ból, és paraméterként megadhatja a munkaterület és a szolgáltatás nevét. Az új objektum az üzembe helyezett szolgáltatással kapcsolatos információkat tartalmaz.

    ```python
    service = Webservice(workspace=ws, name='myservice')
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

### <a name="secured-web-service"></a>Biztonságos webszolgáltatás

Ha az üzembe helyezett webszolgáltatást TLS/SSL-tanúsítvánnyal biztosította, [https használatával](https://en.wikipedia.org/wiki/HTTPS) csatlakozhat a szolgáltatáshoz a pontozási vagy swagger URI használatával. A HTTPS a kettő közötti kommunikáció titkosításával segíti az ügyfél és a webszolgáltatás közötti kommunikáció biztonságát. A titkosítás [a Transport Layer Security (TLS) (Transport Layer Security) (Transport Layer Security) (Transport Layer Security) (Transport Layer Security) (TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security) A TLS-t néha még mindig Secure Sockets Layer (SSL) néven is *emlegetik,* amely a TLS elődje volt.

> [!IMPORTANT]
> Az Azure Machine Learning által telepített webszolgáltatások csak a TLS 1.2-es verzióját támogatják. Ügyfélalkalmazás létrehozásakor győződjön meg arról, hogy támogatja ezt a verziót.

További információ: [A TLS használata webszolgáltatás védelméhez az Azure Machine Learningen keresztül.](how-to-secure-web-service.md)

### <a name="authentication-for-services"></a>Szolgáltatások hitelesítése

Az Azure Machine Learning kétféleképpen szabályozhatja a webes szolgáltatásokhoz való hozzáférést.

|Hitelesítési módszer|Aci|AKS|
|---|---|---|
|Kulcs|Alapértelmezés szerint letiltva| Alapértelmezés szerint engedélyezett|
|Jogkivonat| Nem érhető el| Alapértelmezés szerint letiltva |

Amikor egy kulccsal vagy jogkivonattal védett szolgáltatásnak küld egy kérést, az __engedélyezési__ fejléc használatával adja át a kulcsot vagy a jogkivonatot. A kulcsot vagy jogkivonatot `Bearer <key-or-token>`a `<key-or-token>` , where is a kulcs vagy a token értéke formátumban kell formázni.

#### <a name="authentication-with-keys"></a>Hitelesítés kulcsokkal

Ha engedélyezi a hitelesítést egy központi telepítéshez, automatikusan létrehozza a hitelesítési kulcsokat.

* A hitelesítés alapértelmezés szerint engedélyezve van, amikor az Azure Kubernetes szolgáltatásra telepíti.
* A hitelesítés alapértelmezés szerint le van tiltva, amikor az Azure Container Instances-ra telepíti.

A hitelesítés vezérléséhez `auth_enabled` használja a paramétert központi telepítés létrehozásakor vagy frissítésekor.

Ha a hitelesítés engedélyezve `get_keys` van, a módszer segítségével lekérheti az elsődleges és másodlagos hitelesítési kulcsot:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Ha újra kell generálnia egy [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)kulcsot, használja a használatát.

#### <a name="authentication-with-tokens"></a>Hitelesítés jogkivonatokkal

Ha engedélyezi a token hitelesítésegy webszolgáltatás, a felhasználónak biztosítania kell egy Azure Machine Learning JWT-jogkivonatot a webszolgáltatás eléréséhez. 

* A jogkivonat-hitelesítés alapértelmezés szerint le van tiltva, amikor az Azure Kubernetes szolgáltatásra telepít.
* A tokenhitelesítés nem támogatott, ha az Azure Container Instances üzembe helyezésekor.

A tokenhitelesítés vezérléséhez `token_auth_enabled` használja a paramétert központi telepítés létrehozásakor vagy frissítésekor.

Ha a tokenhitelesítés engedélyezve `get_token` van, a metódus segítségével lekérheti a tulajdonosi jogkivonatot, és amely a jogkivonatok lejárati idejét:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> A jogkivonat ideje után új jogkivonatot kell kérnie. `refresh_by` 

## <a name="request-data"></a>Adatok kérése

A REST API a kérelem törzsét json-dokumentumnak számítja, amely a következő struktúrával van eltakarva:

```json
{
    "data":
        [
            <model-specific-data-structure>
        ]
}
```

> [!IMPORTANT]
> Az adatok szerkezetének meg kell egyeznie azzal, amit a pontozási parancsfájl és a modell a szolgáltatásban elvár. A pontozási parancsfájl módosíthatja az adatokat, mielőtt átadná a modellnek.

Például a modell a [vonat a jegyzetfüzeten belül](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) példa 10 számtömböt vár. A példához a pontozási parancsfájl létrehoz egy Numpy tömböt a kérelemből, és átadja azt a modellnek. A következő példa a szolgáltatás által elvárt adatokat mutatja be:

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

A webszolgáltatás egy kérelemben több adathalmazt is elfogadhat. Egy választömböt tartalmazó JSON-dokumentumot ad vissza.

### <a name="binary-data"></a>Bináris adatok

A bináris adatok szolgáltatásban való támogatásának engedélyezéséről a [Bináris adatok](how-to-deploy-and-where.md#binary)című témakörben talál további információt.

### <a name="cross-origin-resource-sharing-cors"></a>Több forrásból származó erőforrás-megosztás (CORS)

A CORS-támogatás szolgáltatásban való engedélyezéséről a [Több forrásból származó erőforrások megosztásáról](how-to-deploy-and-where.md#cors)talál.

## <a name="call-the-service-c"></a>Hívja a szolgáltatást (C#)

Ez a példa bemutatja, hogyan használhatja a C# használatával a vonatból létrehozott webszolgáltatás hívására a [jegyzetfüzeten belül](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) például:

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

A visszaadott eredmények hasonlóak a következő JSON-dokumentumhoz:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-go"></a>Hívja a szolgáltatást (Go)

Ez a példa bemutatja, hogyan használhatja az Ugrás t a Vonat ból létrehozott webszolgáltatás hívására a [jegyzetfüzeten belül](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) például:

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

A visszaadott eredmények hasonlóak a következő JSON-dokumentumhoz:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-java"></a>Hívja a szolgáltatást (Java)

Ez a példa bemutatja, hogyan használhatja a Java-t a Train-ből létrehozott webszolgáltatás hívására a [jegyzetfüzeten belül](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) például:

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

A visszaadott eredmények hasonlóak a következő JSON-dokumentumhoz:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-python"></a>Hívja a szolgáltatást (Python)

Ez a példa bemutatja, hogyan használhatja a Python t a [jegyzetfüzeten belüli trainből](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) létrehozott webszolgáltatás hívására:

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

A visszaadott eredmények hasonlóak a következő JSON-dokumentumhoz:

```JSON
[217.67978776218715, 224.78937091757172]
```

## <a name="consume-the-service-from-power-bi"></a>Szolgáltatás felhasználása a Power BI-ból

A Power BI támogatja az Azure Machine Learning webes szolgáltatásainak felhasználását, hogy a Power BI-ban lévő adatokat előrejelzésekkel gazdagítsa. 

A Power BI-ban való használatra támogatott webszolgáltatás létrehozásához a sémának támogatnia kell a Power BI által igényelt formátumot. [Megtudhatja, hogy miként hozhat létre Power BI által támogatott sémát.](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where#example-entry-script)

A webszolgáltatás üzembe helyezése után a Power BI-adatfolyamokból fogyókúrák. [Megtudhatja, hogyan használhatja fel az Azure Machine Learning webszolgáltatását a Power BI-ból.](https://docs.microsoft.com/power-bi/service-machine-learning-integration)

## <a name="next-steps"></a>További lépések

A Python és a deep learning modellek valós idejű pontozásához hivatkozási architektúrát szeretne megtekinteni, látogasson el az [Azure architektúraközpontba.](/azure/architecture/reference-architectures/ai/realtime-scoring-python)
