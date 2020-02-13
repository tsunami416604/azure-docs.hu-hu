---
title: Webszolgáltatás felhasználása
titleSuffix: ML Studio (classic) - Azure
description: Ha a gépi tanulási szolgáltatás központi telepítése Azure Machine Learning Studio (klasszikus), a REST-alapú webszolgáltatás akár valós idejű kérelem-válasz szolgáltatásként, akár batch-végrehajtási szolgáltatásként is felhasználható.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 06/02/2017
ms.openlocfilehash: 6ecf5ba01b03ab899fec96b8ecd282a29e5ce3e0
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77168898"
---
# <a name="how-to-consume-an-azure-machine-learning-studio-classic-web-service"></a>Azure Machine Learning Studio (klasszikus) webszolgáltatás felhasználása

Miután telepített egy Azure Machine Learning Studio (klasszikus) prediktív modellt webszolgáltatásként, használhat egy REST API az adatküldés és az előrejelzések beszerzése érdekében. Elküldheti az adatok valós idejű és kötegelt módban.

A Machine Learning webszolgáltatások Machine Learning Studio (klasszikus) használatával történő létrehozásáról és üzembe helyezéséről további információt itt talál:

* Az Machine Learning Studio (klasszikus) kísérlet létrehozásával kapcsolatos oktatóanyagért lásd: [az első kísérlet létrehozása](create-experiment.md).
* A webszolgáltatások üzembe helyezésével kapcsolatos részletekért lásd: [Machine learning webszolgáltatás üzembe](deploy-a-machine-learning-web-service.md)helyezése.
* Az általános Machine Learning kapcsolatos további információkért látogasson el a [Machine learning dokumentációs központba](https://azure.microsoft.com/documentation/services/machine-learning/).



## <a name="overview"></a>Áttekintés
Az Azure Machine Learning Web Service egy külső alkalmazás a Machine Learning munkafolyamatának pontozási modelljével valós időben kommunikál. A Machine Learning webszolgáltatás-hívások visszaadják az előrejelzés eredményét a külső alkalmazásnak. Ahhoz, hogy egy Machine Learning webszolgáltatás felé irányuló hívások, amikor telepít egy előrejelzési API-kulcs adja át. A Machine Learning webszolgáltatás a webprogramozási projektekben népszerű architektúra választott REST alapul.

A Azure Machine Learning Studio (klasszikus) két típusú szolgáltatást tartalmaz:

* Kérelem-válasz szolgáltatás (RR) – alacsony késésű, rugalmasan méretezhető szolgáltatás, amely felületet biztosít a Machine Learning Studio (klasszikus) által létrehozott és telepített állapot nélküli modellekhez.
* Kötegelt végrehajtási szolgáltatás (BES) –-aszinkron szolgáltatás, amely adatrekordok pontozza.

A Machine Learning webszolgáltatásokkal kapcsolatos további információkért lásd: [Machine learning webszolgáltatás üzembe helyezése](deploy-a-machine-learning-web-service.md).

## <a name="get-an-authorization-key"></a>Engedélyezési kulcs beszerzése
A kísérlet során történő telepítésekor a webszolgáltatás API-kulcsok jönnek létre. A kulcsok több helyről lehet lekérdezni.

### <a name="from-the-microsoft-azure-machine-learning-web-services-portal"></a>A Microsoft Azure Machine Learning Web Services portálon
Jelentkezzen be a [Microsoft Azure Machine learning Web Services](https://services.azureml.net) portálra.

Az új Machine Learning webszolgáltatás API-kulcs lekéréséhez:

1. A Azure Machine Learning webszolgáltatások portálján kattintson a **webszolgáltatások** lehetőségre a felső menüben.
2. Kattintson a webes szolgáltatás, amelynek meg szeretné lekérni a kulcsot.
3. A felső **menüben kattintson a**felhasználás elemre.
4. Másolja és mentse az **elsődleges kulcsot**.

A klasszikus Machine Learning webszolgáltatás API-kulcs lekéréséhez:

1. A Azure Machine Learning webszolgáltatások portálján kattintson a **klasszikus webes szolgáltatások** elemre a felső menüben.
2. Kattintson a webes szolgáltatás, amellyel dolgozik.
3. Kattintson a végpontra, amelynek meg szeretné lekérni a kulcsot.
4. A felső **menüben kattintson a**felhasználás elemre.
5. Másolja és mentse az **elsődleges kulcsot**.

### <a name="classic-web-service"></a>Klasszikus webszolgáltatás
 A klasszikus webszolgáltatások kulcsát a Machine Learning Studio (klasszikus) webhelyről is lekérheti.

#### <a name="machine-learning-studio-classic"></a>Machine Learning Studio (klasszikus)
1. Machine Learning Studio (klasszikus) területen kattintson a bal oldali **WEBszolgáltatások** elemre.
2. Kattintson egy webszolgáltatás. Az **API-kulcs** az **irányítópult** lapon található.

## <a id="connect"></a>Kapcsolódás Machine Learning webszolgáltatáshoz
A Machine Learning Web service bármilyen programozási nyelvet, amely támogatja a HTTP-kérés-válasz csatlakozhat. Megtekintheti a szereplő példák C#, Python és az R, Machine Learning Web service Súgó oldal.

**Machine learning API Súgó** Webszolgáltatások telepítésekor a rendszer Machine Learning API súgóját hozza létre. Lásd [: 3. Oktatóanyag: hitelkockázat-modell üzembe helyezése](tutorial-part3-credit-risk-deploy.md).
Machine Learning API segítségével előrejelzési webszolgáltatást részleteit tartalmazza.

1. Kattintson a webes szolgáltatás, amellyel dolgozik.
2. Kattintson a végpontra, amelynek meg szeretné tekinteni az API-súgóoldalon.
3. A felső **menüben kattintson a**felhasználás elemre.
4. Az **API-Súgó lapon** kattintson a kérelem-válasz vagy a Batch-végrehajtási végpontok lehetőségre.

**Új webszolgáltatás Machine Learning API-súgójának megtekintése**

A [Azure Machine learning Web Services portálon](https://services.azureml.net/):

1. A felső menüben kattintson a **Web Services** elemre.
2. Kattintson a webes szolgáltatás, amelynek meg szeretné lekérni a kulcsot.

Kattintson a **webszolgáltatások használata** lehetőségre az URI-k beszerzéséhez a kérelem-válasz és a C#batch-végrehajtási szolgáltatások és a mintakód számára a, az R és a Python alkalmazásban.

Kattintson a **hencegés API** lehetőségre a megadott URI-k által hívott API-k hencegő dokumentációjának beszerzéséhez.

### <a name="c-sample"></a>C#-minta
Machine Learning webszolgáltatáshoz való kapcsolódáshoz használjon egy **HttpClient** Passing ScoreData. ScoreData tartalmaz egy FeatureVector egy numerikus funkciók n dimenziós vektor, amely a ScoreData jelöli. A hitelesítést, a Machine Learning szolgáltatás API-kulcs.

Machine Learning webszolgáltatáshoz való kapcsolódáshoz telepíteni kell a **Microsoft. AspNet. WebApi. Client** NuGet-csomagot.

**A Microsoft. AspNet. WebApi. Client NuGet telepítése a Visual Studióban**

1. A UCI letöltési adatkészlet közzététele: felnőtt 2 osztály adatkészlet webszolgáltatáshoz.
2. Kattintson a **Tools** (Eszközök)  > **NuGet Package Manager** (NuGet-csomagkezelő) > **Package Manager Console** (Csomagkezelő konzol) elemre.
3. Válassza a **Microsoft. AspNet. WebApi. Client csomag telepítése**lehetőséget.

**A mintakód futtatása**

1. Közzététele "1. példa: adatkészlet UCI töltheti le: felnőtt 2 osztály adatkészlet" kísérlet, a Machine Learning minta gyűjtemény része.
2. Rendelje hozzá a kulccsal apikey tulajdonsággal végzett tesztelése egy webszolgáltatásból. Lásd **a fenti engedélyezési kulcs beszerzése** című témakört.
3. Rendelje hozzá a kérelem URI-azonosítójú serviceUri.

**A teljes kérelem így fog kinézni.**
```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net.Http;
using System.Net.Http.Formatting;
using System.Net.Http.Headers;
using System.Text;
using System.Threading.Tasks;

namespace CallRequestResponseService
{
    class Program
    {
        static void Main(string[] args)
        {
            InvokeRequestResponseService().Wait();
        }

        static async Task InvokeRequestResponseService()
        {
            using (var client = new HttpClient())
            {
                var scoreRequest = new
                {
                    Inputs = new Dictionary<string, List<Dictionary<string, string>>> () {
                        {
                            "input1",
                            // Replace columns labels with those used in your dataset
                            new List<Dictionary<string, string>>(){new Dictionary<string, string>(){
                                    {
                                        "column1", "value1"
                                    },
                                    {
                                        "column2", "value2"
                                    },
                                    {
                                        "column3", "value3"
                                    }
                                }
                            }
                        },
                    },
                    GlobalParameters = new Dictionary<string, string>() {}
                };

                // Replace these values with your API key and URI found on https://services.azureml.net/
                const string apiKey = "<your-api-key>"; 
                const string apiUri = "<your-api-uri>";
                
                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue( "Bearer", apiKey);
                client.BaseAddress = new Uri(apiUri);

                // WARNING: The 'await' statement below can result in a deadlock
                // if you are calling this code from the UI thread of an ASP.NET application.
                // One way to address this would be to call ConfigureAwait(false)
                // so that the execution does not attempt to resume on the original context.
                // For instance, replace code such as:
                //      result = await DoSomeTask()
                // with the following:
                //      result = await DoSomeTask().ConfigureAwait(false)

                HttpResponseMessage response = await client.PostAsJsonAsync("", scoreRequest);

                if (response.IsSuccessStatusCode)
                {
                    string result = await response.Content.ReadAsStringAsync();
                    Console.WriteLine("Result: {0}", result);
                }
                else
                {
                    Console.WriteLine(string.Format("The request failed with status code: {0}", response.StatusCode));

                    // Print the headers - they include the request ID and the timestamp,
                    // which are useful for debugging the failure
                    Console.WriteLine(response.Headers.ToString());

                    string responseContent = await response.Content.ReadAsStringAsync();
                    Console.WriteLine(responseContent);
                }
            }
        }
    }
}
```

### <a name="python-sample"></a>Python-minta
Ha Machine Learning webszolgáltatáshoz szeretne csatlakozni, használja a Python 2. X és a **urllib.** **urllib2** függvénytárát a Python 3. x verzióhoz. Akkor továbbítja a ScoreData, amely tartalmaz egy FeatureVector egy numerikus funkciók n dimenziós vektor, amely a ScoreData jelöli. A hitelesítést, a Machine Learning szolgáltatás API-kulcs.

**A mintakód futtatása**

1. Üzembe helyezése "1. példa: adatkészlet UCI töltheti le: felnőtt 2 osztály adatkészlet" kísérlet, a Machine Learning minta gyűjtemény része.
2. Rendelje hozzá a kulccsal apikey tulajdonsággal végzett tesztelése egy webszolgáltatásból. Tekintse meg a jelen cikk elején található **engedélyezési kulcs beszerzése** című szakaszt.
3. Rendelje hozzá a kérelem URI-azonosítójú serviceUri.

**A teljes kérelem így fog kinézni.**
```python
import urllib2 # urllib.request and urllib.error for Python 3.X
import json

data = {
    "Inputs": {
        "input1":
        [
            {
                'column1': "value1",   
                'column2': "value2",   
                'column3': "value3"
            }
        ],
    },
    "GlobalParameters":  {}
}

body = str.encode(json.dumps(data))

# Replace this with the URI and API Key for your web service
url = '<your-api-uri>'
api_key = '<your-api-key>'
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}

# "urllib.request.Request(url, body, headers)" for Python 3.X
req = urllib2.Request(url, body, headers)

try:
    # "urllib.request.urlopen(req)" for Python 3.X
    response = urllib2.urlopen(req)

    result = response.read()
    print(result)
# "urllib.error.HTTPError as error" for Python 3.X
except urllib2.HTTPError, error: 
    print("The request failed with status code: " + str(error.code))

    # Print the headers - they include the request ID and the timestamp, which are useful for debugging the failure
    print(error.info())
    print(json.loads(error.read())) 
```

### <a name="r-sample"></a>R-minta

Machine Learning webszolgáltatáshoz való kapcsolódáshoz használja a **RCurl** és a **rjson** kódtárat a kérelem és a visszaadott JSON-válasz feldolgozásához. Akkor továbbítja a ScoreData, amely tartalmaz egy FeatureVector egy numerikus funkciók n dimenziós vektor, amely a ScoreData jelöli. A hitelesítést, a Machine Learning szolgáltatás API-kulcs.

**A teljes kérelem így fog kinézni.**
```r
library("RCurl")
library("rjson")

# Accept SSL certificates issued by public Certificate Authorities
options(RCurlOptions = list(cainfo = system.file("CurlSSL", "cacert.pem", package = "RCurl")))

h = basicTextGatherer()
hdr = basicHeaderGatherer()

req = list(
    Inputs = list(
            "input1" = list(
                list(
                        'column1' = "value1",
                        'column2' = "value2",
                        'column3' = "value3"
                    )
            )
        ),
        GlobalParameters = setNames(fromJSON('{}'), character(0))
)

body = enc2utf8(toJSON(req))
api_key = "<your-api-key>" # Replace this with the API key for the web service
authz_hdr = paste('Bearer', api_key, sep=' ')

h$reset()
curlPerform(url = "<your-api-uri>",
httpheader=c('Content-Type' = "application/json", 'Authorization' = authz_hdr),
postfields=body,
writefunction = h$update,
headerfunction = hdr$update,
verbose = TRUE
)

headers = hdr$value()
httpStatus = headers["status"]
if (httpStatus >= 400)
{
print(paste("The request failed with status code:", httpStatus, sep=" "))

# Print the headers - they include the request ID and the timestamp, which are useful for debugging the failure
print(headers)
}

print("Result:")
result = h$value()
print(fromJSON(result))
```

### <a name="javascript-sample"></a>JavaScript-minta

Ha Machine Learning webszolgáltatáshoz szeretne csatlakozni, használja a **NPM-csomagot a** projektben. Emellett a `JSON` objektumot is használhatja a bevitel formázásához és az eredmény elemzéséhez. Telepítse a `npm install request --save`használatával, vagy vegyen fel `"request": "*"` a Package. JSON fájlba `dependencies` és futtassa `npm install`.

**A teljes kérelem így fog kinézni.**
```js
let req = require("request");

const uri = "<your-api-uri>";
const apiKey = "<your-api-key>";

let data = {
    "Inputs": {
        "input1":
        [
            {
                'column1': "value1",
                'column2': "value2",
                'column3': "value3"
            }
        ],
    },
    "GlobalParameters": {}
}

const options = {
    uri: uri,
    method: "POST",
    headers: {
        "Content-Type": "application/json",
        "Authorization": "Bearer " + apiKey,
    },
    body: JSON.stringify(data)
}

req(options, (err, res, body) => {
    if (!err && res.statusCode == 200) {
        console.log(body);
    } else {
        console.log("The request failed with status code: " + res.statusCode);
    }
});
```
