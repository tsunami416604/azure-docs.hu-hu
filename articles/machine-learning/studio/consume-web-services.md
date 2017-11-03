---
title: "Az Azure Machine Learning Web service felhasználásához hogyan |} Microsoft Docs"
description: "Miután a machine learning szolgáltatás van telepítve, a RESTFul webes szolgáltatás, amely szeretné elérhetővé tenni valós idejű kérés-válasz szolgáltatás, vagy egy kötegelt végrehajtási szolgáltatás képes használni."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 804f8211-9437-4982-98e9-ca841b7edf56
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/02/2017
ms.author: garye
ms.openlocfilehash: 13de6daabf2b6d83cc703ae6b3f0a30a1dfa34d6
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/12/2017
---
# <a name="how-to-consume-an-azure-machine-learning-web-service"></a>Hogyan kell használni az Azure Machine Learning Web service

Miután telepít egy Azure Machine Learning a prediktív modell webszolgáltatásként, használhatja a REST API előrejelzéseket, és elküldi a data. Is küldheti az adatokat, a valós idejű vagy kötegelt módban.

További információ arról, hogyan hozhat létre és telepíthet a Machine Learning Web szolgáltatás Machine Learning Studio itt található:

* A kísérlet létrehozása a Machine Learning Studio oktatóanyag, lásd: [az első kísérlet létrehozása](create-experiment.md).
* Egy webszolgáltatás-bővítmény telepítésével, lásd: [egy Machine Learning webszolgáltatás-bővítmény telepítése](publish-a-machine-learning-web-service.md).
* További információ a gépi tanulás általában, látogasson el a [Machine Learning dokumentációs központban](https://azure.microsoft.com/documentation/services/machine-learning/).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="overview"></a>Áttekintés
Az Azure Machine Learning Web Service külső alkalmazás kommunikál a Machine Learning munkafolyamat pontozási modell valós időben. A Machine Learning webszolgáltatás-hívás a külső alkalmazás előrejelzés eredményeket ad vissza. Ahhoz, hogy egy Machine Learning webszolgáltatás hívja, át előrejelzéshez telepítésekor létrehozott API-kulcs. A Machine Learning webszolgáltatás REST, olyan népszerű architektúrával kapcsolatos választási lehetőség webes projektek programozási alapul.

Az Azure Machine Learning két különböző típusú szolgáltatást tud biztosítani:

* Kérés-válasz szolgáltatás (RR-EKET) – egy kis késés, az állapot nélküli modellekhez létrehozott és telepített felületet biztosít a Machine Learning Studio a magas szinten méretezhető szolgáltatás.
* Kötegelt végrehajtási szolgáltatás (BES) –-egy aszinkron szolgáltatást, hogy a rekordok köteg pontszámait.

További információ a Machine Learning webszolgáltatások: [egy Machine Learning webszolgáltatás-bővítmény telepítése](publish-a-machine-learning-web-service.md).

## <a name="get-an-azure-machine-learning-authorization-key"></a>Azure Machine Learning engedélyezési kulcs beszerzése
Amikor telepíti a kísérletet, API-kulcsokat jönnek létre a webszolgáltatás. A kulcsok kérhetnek le több helyre.

### <a name="from-the-microsoft-azure-machine-learning-web-services-portal"></a>A Microsoft Azure Machine Learning webszolgáltatások portálról
Jelentkezzen be a [Microsoft Azure Machine Learning webszolgáltatások](https://services.azureml.net) portálon.

Egy új Machine Learning webszolgáltatáshoz API-kulcs beolvasása:

1. Az Azure Machine Learning webszolgáltatások portálon kattintson **webszolgáltatások** a felső menüben.
2. Kattintson a webszolgáltatás legyen a kulcs beolvasása.
3. Kattintson a felső menüben **felhasználás**.
4. Másolja ki és mentse a **elsődleges kulcs**.

Az API-kulcs a klasszikus Machine Learning webszolgáltatás beolvasása:

1. Az Azure Machine Learning webszolgáltatások portálon kattintson **klasszikus webszolgáltatások** a felső menüben.
2. Kattintson a webes szolgáltatás, amellyel dolgozik.
3. Kattintson a végpont legyen a kulcs beolvasása.
4. Kattintson a felső menüben **felhasználás**.
5. Másolja ki és mentse a **elsődleges kulcs**.

### <a name="classic-web-service"></a>Klasszikus webszolgáltatás
 A kulcs a klasszikus webszolgáltatáshoz beolvasható a Machine Learning Studio vagy a klasszikus Azure portálon.

#### <a name="machine-learning-studio"></a>Machine Learning Studio
1. A Machine Learning Studióban, kattintson a **WEBSZOLGÁLTATÁSOK** a bal oldalon.
2. Kattintson egy webszolgáltatás-bővítmény. A **API-kulcs** megtalálható a **IRÁNYÍTÓPULT** fülre.

#### <a name="azure-classic-portal"></a>klasszikus Azure portál
1. Kattintson a **MACHINE LEARNING** a bal oldalon.
2. Kattintson a munkaterületen, ahol a webszolgáltatás.
3. Kattintson a **WEBSZOLGÁLTATÁSOK**.
4. Kattintson egy webszolgáltatás-bővítmény.
5. Kattintson egy végpontot. A "API-kulcsot" nem működik az alsó sarokban.

## <a id="connect"></a>Csatlakozás egy Machine Learning webszolgáltatás-bővítmény
A Machine Learning Web service bármely programozási nyelvet, amely támogatja a HTTP-kérelem-válasz kapcsolódhat. A Machine Learning webszolgáltatás súgólapja a példákban a C#, Python, és R tekintheti meg.

**Számítógép-Learning API súgó** Machine Learning API-súgó egy webszolgáltatás-bővítmény telepítésekor létrejön. Lásd: [Azure Machine Learning forgatókönyv - webszolgáltatás telepítése](walkthrough-5-publish-web-service.md).
A Machine Learning API súgó webszolgáltatás előrejelzéshez részleteit tartalmazza.

1. Kattintson a webes szolgáltatás, amellyel dolgozik.
2. Kattintson a végpont, amelynek meg szeretné tekinteni a API Súgóoldalát.
3. Kattintson a felső menüben **felhasználás**.
4. Kattintson a **API súgólap** alatt a kérelem / válasz vagy kötegelt végrehajtási végpontok.

**Az új webszolgáltatás nézet Machine Learning API segítségével**

Az a [Azure Machine Learning Web Services portál](https://services.azureml.net/):

1. Kattintson a **WEBSZOLGÁLTATÁSOK** a felső menüben.
2. Kattintson a webszolgáltatás legyen a kulcs beolvasása.

Kattintson a **használata webszolgáltatás** az URI-azonosítók lekérése a kérelem-Reposonse és kötegelt végrehajtási szolgáltatás és a minta kód a C#, R és Python.

Kattintson a **Swagger API** megszerezni a Swagger-alapú dokumentáció számára az API-k a megadott URI-k hívását.

### <a name="c-sample"></a>C# minta
Csatlakozás egy Machine Learning webszolgáltatáshoz, használjon egy **HttpClient** ScoreData átadásakor. ScoreData FeatureVector, egy numerikus szolgáltatások n dimenziós vektort a ScoreData jelölő tartalmazza. A hitelesítést a Machine Learning szolgáltatás API-kulcs.

Csatlakozás egy Machine Learning webszolgáltatáshoz, a **Microsoft.AspNet.WebApi.Client** NuGet-csomagot kell telepíteni.

**Telepítse a Microsoft.AspNet.WebApi.Client NuGet a Visual Studióban**

1. A letöltési UCI adatkészlet közzététele: felnőtt 2 osztály dataset webszolgáltatáshoz.
2. Kattintson a **Tools** (Eszközök)  > **NuGet Package Manager** (NuGet-csomagkezelő) > **Package Manager Console** (Csomagkezelő konzol) elemre.
3. Válasszon **Install-Package Microsoft.AspNet.WebApi.Client**.

**A kód a minta futtatásához**

1. Közzététele "1. példa: dataset letöltését UCI: felnőtt 2 osztály adatkészlet" kísérlet, a Machine Learning minta gyűjtemény része.
2. Rendelje hozzá a kulccsal apiKey webszolgáltatásból. Lásd: **Azure Machine Learning engedélyezési kulcs beszerzése** felett.
3. A kérelem URI-azonosítójú serviceUri hozzárendelése.

**Ez mit fog megjelenni a teljes kérelem.**
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

                    // Print the headers - they include the requert ID and the timestamp,
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
Csatlakozás egy Machine Learning webszolgáltatáshoz, használja a **urllib2** kódtára a Pythonhoz 2.X és **urllib.request** könyvtár a Python 3.X. ScoreData, amely tartalmaz egy FeatureVector, egy numerikus szolgáltatások n dimenziós vektort a ScoreData jelölő sikeres lesz. A hitelesítést a Machine Learning szolgáltatás API-kulcs.

**A kód a minta futtatásához**

1. Telepítése "minta 1: dataset letöltését UCI: 2 felnőtt osztály dataset" kísérlet, a Machine Learning minta gyűjtemény része.
2. Rendelje hozzá a kulccsal apiKey webszolgáltatásból. Tekintse meg a **Azure Machine Learning engedélyezési kulcs beszerzése** elején című szakaszát.
3. A kérelem URI-azonosítójú serviceUri hozzárendelése.

**Ez mit fog megjelenni a teljes kérelem.**
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

    # Print the headers - they include the requert ID and the timestamp, which are useful for debugging the failure
    print(error.info())
    print(json.loads(error.read())) 
```

### <a name="r-sample"></a>R-minta

A Machine Learning webszolgáltatáshoz csatlakozni, használja a **RCurl** és **rjson** szalagtárak a kérést, és a visszaadott JSON-válasz feldolgozását. ScoreData, amely tartalmaz egy FeatureVector, egy numerikus szolgáltatások n dimenziós vektort a ScoreData jelölő sikeres lesz. A hitelesítést a Machine Learning szolgáltatás API-kulcs.

**Ez mit fog megjelenni a teljes kérelem.**
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

# Print the headers - they include the requert ID and the timestamp, which are useful for debugging the failure
print(headers)
}

print("Result:")
result = h$value()
print(fromJSON(result))
```

### <a name="javascript-sample"></a>JavaScript-minta

A Machine Learning webszolgáltatáshoz csatlakozni, használja a **kérelem** npm csomagot a projektben. Is használhatja a `JSON` objektum a megadott formázása, és elemezni az eredmény. Telepítés használatával `npm install request --save`, vagy adjon hozzá `"request": "*"` a package.json alapján történő `dependencies` , és futtassa `npm install`.

**Ez mit fog megjelenni a teljes kérelem.**
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