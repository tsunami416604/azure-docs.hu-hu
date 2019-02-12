---
title: Webszolgáltatás felhasználása
titleSuffix: Azure Machine Learning Studio
description: Machine learning-szolgáltatás üzembe helyezése az Azure Machine Learning Studióban, után a webes RESTFul szolgáltatás kötegelt végrehajtási szolgáltatásként vagy a valós idejű kérés-válasz szolgáltatás képes használni.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 06/02/2017
ms.openlocfilehash: 4af7af6616b5415cd13abacd6aca13bd412d4ad8
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "55998083"
---
# <a name="how-to-consume-an-azure-machine-learning-studio-web-service"></a>Az Azure Machine Learning Studio webszolgáltatás használata

Miután telepít egy Azure Machine Learning prediktív modellt webszolgáltatásként, egy REST API segítségével küldje el az adatokat és kaphatnak előjelzéseket. Elküldheti az adatok valós idejű és kötegelt módban.

További információ az hozhat létre és telepíthet egy Machine Learning Web service, a Machine Learning Studio IDE használatával találja meg:

* A kísérlet létrehozása a Machine Learning Studióban oktatóanyagért lásd: [az első kísérlet létrehozása](create-experiment.md).
* További információ a webszolgáltatás üzembe helyezése: [egy Machine Learning Web Service szolgáltatásának telepítése](publish-a-machine-learning-web-service.md).
* További információ a Machine Learning általában látogasson el a [Machine Learning dokumentációs központban](https://azure.microsoft.com/documentation/services/machine-learning/).



## <a name="overview"></a>Áttekintés
Az Azure Machine Learning Web Service egy külső alkalmazás a Machine Learning munkafolyamatának pontozási modelljével valós időben kommunikál. A Machine Learning webszolgáltatás-hívások visszaadják az előrejelzés eredményét a külső alkalmazásnak. Ahhoz, hogy egy Machine Learning webszolgáltatás felé irányuló hívások, amikor telepít egy előrejelzési API-kulcs adja át. A Machine Learning webszolgáltatás a webprogramozási projektekben népszerű architektúra választott REST alapul.

Az Azure Machine Learning két különböző típusú szolgáltatást tud biztosítani:

* Kérés-válasz szolgáltatás (RRS) – egy alacsony késleltetésű, nagy mértékben skálázható szolgáltatás, amely a létrehozott és üzembe helyezett állapot nélküli modellekhez felületet biztosít a Machine Learning Studio.
* Kötegelt végrehajtási szolgáltatás (BES) –-aszinkron szolgáltatás, amely adatrekordok pontozza.

Machine Learning webszolgáltatásokkal kapcsolatos további információkért lásd: [egy Machine Learning Web Service szolgáltatásának telepítése](publish-a-machine-learning-web-service.md).

## <a name="get-an-azure-machine-learning-authorization-key"></a>Az Azure Machine Learning hitelesítési kulcs beszerzése
A kísérlet során történő telepítésekor a webszolgáltatás API-kulcsok jönnek létre. A kulcsok több helyről lehet lekérdezni.

### <a name="from-the-microsoft-azure-machine-learning-web-services-portal"></a>A Microsoft Azure Machine Learning Web Services portálon
Jelentkezzen be a [a Microsoft Azure Machine Learning webszolgáltatások](https://services.azureml.net) portálon.

Az új Machine Learning webszolgáltatás API-kulcs lekéréséhez:

1. Az Azure Machine Learning Web Services portálon kattintson a **webszolgáltatások** a felső menüben.
2. Kattintson a webes szolgáltatás, amelynek meg szeretné lekérni a kulcsot.
3. A felső menüben kattintson a **felhasználás**.
4. Másolja ki és mentse a **elsődleges kulcs**.

A klasszikus Machine Learning webszolgáltatás API-kulcs lekéréséhez:

1. Az Azure Machine Learning Web Services portálon kattintson a **klasszikus webszolgáltatások** a felső menüben.
2. Kattintson a webes szolgáltatás, amellyel dolgozik.
3. Kattintson a végpontra, amelynek meg szeretné lekérni a kulcsot.
4. A felső menüben kattintson a **felhasználás**.
5. Másolja ki és mentse a **elsődleges kulcs**.

### <a name="classic-web-service"></a>Klasszikus webszolgáltatás
 A Machine Learning Studio is lekérhet egy kulcsot a klasszikus webszolgáltatások.

#### <a name="machine-learning-studio"></a>Machine Learning Studio
1. A Machine Learning Studióban, kattintson a **WEBSZOLGÁLTATÁSOK** a bal oldalon.
2. Kattintson egy webszolgáltatás. A **API-kulcs** be van kapcsolva a **IRÁNYÍTÓPULT** fülre.

## <a id="connect"></a>Csatlakozás egy Machine Learning webszolgáltatás
A Machine Learning Web service bármilyen programozási nyelvet, amely támogatja a HTTP-kérés-válasz csatlakozhat. Megtekintheti a szereplő példák C#, Python és az R, Machine Learning Web service Súgó oldal.

**A Machine Learning API help** Machine Learning API-súgó egy webszolgáltatás telepítésekor jön létre. Lásd: [3. oktatóanyag: Kredit kockázati modell üzembe helyezése](tutorial-part3-credit-risk-deploy.md).
Machine Learning API segítségével előrejelzési webszolgáltatást részleteit tartalmazza.

1. Kattintson a webes szolgáltatás, amellyel dolgozik.
2. Kattintson a végpontra, amelynek meg szeretné tekinteni az API-súgóoldalon.
3. A felső menüben kattintson a **felhasználás**.
4. Kattintson a **API súgóoldalt** a kérés-válasz vagy a kötegelt végrehajtás végpontok alatt.

**Új webszolgáltatás megtekintése a Machine Learning API segítségével**

Az a [az Azure Machine Learning Web Services portál](https://services.azureml.net/):

1. Kattintson a **WEBSZOLGÁLTATÁSOK** felső menüjében.
2. Kattintson a webes szolgáltatás, amelynek meg szeretné lekérni a kulcsot.

Kattintson a **használata webszolgáltatás** beolvasni az URI-k kérés-válasz és a kötegelt végrehajtási szolgáltatásként és a minta kódja C#, R és Python nyelven.

Kattintson a **Swagger API** Swagger lekérése-alapú dokumentáció esetében az API-k a megadott URI-k hívását.

### <a name="c-sample"></a>C#-minta
A Machine Learning webszolgáltatás csatlakozni, használja az **HttpClient** ScoreData átadásával. ScoreData tartalmaz egy FeatureVector egy numerikus funkciók n dimenziós vektor, amely a ScoreData jelöli. A hitelesítést, a Machine Learning szolgáltatás API-kulcs.

Csatlakozás egy Machine Learning Web Service a **Microsoft.AspNet.WebApi.Client** NuGet-csomagot kell telepíteni.

**Telepítse a Microsoft.AspNet.WebApi.Client NuGet a Visual Studióban**

1. A UCI letöltési adatkészlet közzététele: Felnőtt tartalom 2 osztály adatkészlet webszolgáltatáshoz.
2. Kattintson a **Tools** (Eszközök)  > **NuGet Package Manager** (NuGet-csomagkezelő) > **Package Manager Console** (Csomagkezelő konzol) elemre.
3. Válasszon **Install-Package Microsoft.AspNet.WebApi.Client**.

**A mintakód futtatásához**

1. Közzététele "1 mintát: Töltse le a dataset igénybecslést Szemlélteti: Felnőtt tartalom 2 osztály adatkészlet"kísérlet, a Machine Learning minta gyűjtemény része.
2. Rendelje hozzá a kulccsal apikey tulajdonsággal végzett tesztelése egy webszolgáltatásból. Lásd: **Azure Machine Learning hitelesítési kulcs beszerzése** felett.
3. Rendelje hozzá a kérelem URI-azonosítójú serviceUri.

**Íme egy teljes kérést fog kinézni.**
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
                // if you are calling this code from the UI thread of an ASP.Net application.
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
A Machine Learning webszolgáltatás csatlakozni, használja a **urllib2** kódtára a Pythonhoz 2.X és **urllib.request** kódtára a Pythonhoz 3.X. Akkor továbbítja a ScoreData, amely tartalmaz egy FeatureVector egy numerikus funkciók n dimenziós vektor, amely a ScoreData jelöli. A hitelesítést, a Machine Learning szolgáltatás API-kulcs.

**A mintakód futtatásához**

1. Üzembe helyezése "1 mintát: Töltse le a dataset igénybecslést Szemlélteti: Felnőtt tartalom 2 osztály adatkészlet"kísérlet, a Machine Learning minta gyűjtemény része.
2. Rendelje hozzá a kulccsal apikey tulajdonsággal végzett tesztelése egy webszolgáltatásból. Tekintse meg a **Azure Machine Learning hitelesítési kulcs beszerzése** mellékmondatokat című szakaszát.
3. Rendelje hozzá a kérelem URI-azonosítójú serviceUri.

**Íme egy teljes kérést fog kinézni.**
```python
import urllib2 # urllib.request for Python 3.X
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

# "urllib.request.Request(uri, body, headers)" for Python 3.X
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

A Machine Learning Web Service csatlakozni, használja a **RCurl** és **rjson** kódtárakat, a kérést, és a visszaadott JSON-válasz feldolgozása. Akkor továbbítja a ScoreData, amely tartalmaz egy FeatureVector egy numerikus funkciók n dimenziós vektor, amely a ScoreData jelöli. A hitelesítést, a Machine Learning szolgáltatás API-kulcs.

**Íme egy teljes kérést fog kinézni.**
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

A Machine Learning Web Service csatlakozni, használja a **kérelem** npm-csomagot a projektben. Emellett használhatja a `JSON` objektumot a bemeneti formázása és elemezheti az eredményt. Telepítés használatával `npm install request --save`, vagy adjon hozzá `"request": "*"` , a package.json alatt `dependencies` , és futtassa `npm install`.

**Íme egy teljes kérést fog kinézni.**
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
