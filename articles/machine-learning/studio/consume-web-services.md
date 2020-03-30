---
title: Webszolgáltatás felhasználása
titleSuffix: ML Studio (classic) - Azure
description: Miután egy gépi tanulási szolgáltatás üzembe helyezése az Azure Machine Learning Studio (klasszikus), a RESTFul webszolgáltatás lehet használni akár valós idejű kérelem-válasz szolgáltatás, vagy egy kötegelt végrehajtási szolgáltatás.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 06/02/2017
ms.openlocfilehash: de395f7167f0ab7d7c6429c14d5efce46831b576
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218236"
---
# <a name="how-to-consume-an-azure-machine-learning-studio-classic-web-service"></a>Azure Machine Learning Studio (klasszikus) webszolgáltatás felhasználása

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Miután egy Azure Machine Learning Studio (klasszikus) prediktív modell webszolgáltatásként üzembe helyezése, egy REST API-t használhat az adatok küldéséhez és az előrejelzések betekintéséhez. Az adatokat valós időben vagy kötegelt módban is elküldheti.

A Machine Learning Webszolgáltatások Machine Learning Studio (klasszikus) használatával történő létrehozásáról és központi telepítéséről itt olvashat bővebben:

* A Machine Learning Studio (klasszikus) programban végzett kísérletek létrehozásáról az [Első kísérlet létrehozása című](create-experiment.md)témakörben található.
* A webszolgáltatások központi telepítéséről a [Machine Learning webszolgáltatás telepítése (Központi) szolgáltatás telepítése (Központi) témakörben](deploy-a-machine-learning-web-service.md)talál.
* A Machine Learningről általában a [Machine Learning dokumentációs központjában](https://azure.microsoft.com/documentation/services/machine-learning/)olvashat bővebben.



## <a name="overview"></a>Áttekintés
Az Azure Machine Learning webszolgáltatással egy külső alkalmazás valós időben kommunikál a Machine Learning munkafolyamat-pontozási modelljével. A Machine Learning webszolgáltatás-hívás előrejelzési eredményeket ad vissza egy külső alkalmazásnak. Machine Learning webszolgáltatás-hívás, adja át egy API-kulcs, amely akkor jön létre, amikor egy előrejelzés t. A Machine Learning webszolgáltatás a REST-en alapul, amely a webes programozási projektek népszerű architektúrája.

Az Azure Machine Learning Studio (klasszikus) kétféle szolgáltatást kínál:

* Kérés-válasz szolgáltatás (RRS) – Alacsony késésű, jól méretezhető szolgáltatás, amely felületet biztosít a Machine Learning Studio (klasszikus) állapot nélküli modellekhez.
* Batch Execution Service (BES) – egy aszinkron szolgáltatás, amely egy köteget szerez az adatrekordokhoz.

A Machine Learning webszolgáltatásokról a [Machine Learning webszolgáltatás telepítése](deploy-a-machine-learning-web-service.md)című témakörben talál további információt.

## <a name="get-an-authorization-key"></a>Engedélyezési kulcs beszerezni
A kísérlet telepítésekor API-kulcsok jönnek létre a webszolgáltatáshoz. A kulcsokat több helyről is lekérheti.

### <a name="from-the-microsoft-azure-machine-learning-web-services-portal"></a>A Microsoft Azure Machine Learning Web Services portálról
Jelentkezzen be a [Microsoft Azure Machine Learning Web Services](https://services.azureml.net) portálra.

Új Gépi tanulási webszolgáltatás API-kulcsának beolvasása:

1. Az Azure Machine Learning Web Services portálon kattintson a **Webservices** parancsra a felső menüben.
2. Kattintson arra a webszolgáltatásra, amelynek a kulcsát be szeretné olvasni.
3. A felső menüben kattintson a **Fogyasztatás gombra.**
4. Másolja és mentse az **elsődleges kulcsot**.

Klasszikus Machine Learning webszolgáltatás API-kulcsának beolvasása:

1. Az Azure Machine Learning Web Services portálon kattintson a **klasszikus webszolgáltatások** a felső menüben.
2. Kattintson arra a webszolgáltatásra, amellyel dolgozik.
3. Kattintson arra a végpontra, amelynek a kulcsát be szeretné olvasni.
4. A felső menüben kattintson a **Fogyasztatás gombra.**
5. Másolja és mentse az **elsődleges kulcsot**.

### <a name="classic-web-service"></a>Klasszikus webszolgáltatás
 A Klasszikus webszolgáltatás kulcsait a Machine Learning Studio (klasszikus) szolgáltatásból is lekérheti.

#### <a name="machine-learning-studio-classic"></a>Machine Learning Studio (klasszikus)
1. A Machine Learning Studio (klasszikus) alkalmazásban kattintson a bal oldali **WEBSERVICES** elemre.
2. Kattintson egy webszolgáltatásra. Az **API-kulcs** a **DASHBOARD** lapon található.

## <a name="connect-to-a-machine-learning-web-service"></a><a id="connect"></a>Csatlakozás Machine Learning webszolgáltatáshoz
A Machine Learning webszolgáltatásokhoz bármilyen olyan programozási nyelven csatlakozhat, amely támogatja a HTTP-kéréseket és válaszokat. A Példákat C#, Python és R nyelven tekintheti meg a Machine Learning webszolgáltatás súgólapján.

**Machine Learning API súgó** A Machine Learning API-súgója egy webszolgáltatás üzembe helyezésekor jön létre. Lásd [3.](tutorial-part3-credit-risk-deploy.md)
A Machine Learning API-súgó egy előrejelzési webszolgáltatás részleteit tartalmazza.

1. Kattintson arra a webszolgáltatásra, amellyel dolgozik.
2. Kattintson arra a végpontra, amelynek az API súgóoldalát meg szeretné tekinteni.
3. A felső menüben kattintson a **Fogyasztatás gombra.**
4. Kattintson az **API súgólapjára** a Kérelem-válasz vagy a Kötegelt végrehajtás végpontok alatt.

**Machine Learning API-súgó megtekintése egy új webszolgáltatáshoz**

Az [Azure Machine Learning webszolgáltatási portálon:](https://services.azureml.net/)

1. Kattintson a felső menü **WEB SERVICES** parancsára.
2. Kattintson arra a webszolgáltatásra, amelynek a kulcsát be szeretné olvasni.

Kattintson a **Webszolgáltatás használata** a kérelem-válasz és a kötegelt végrehajtási szolgáltatások URI-k és a mintakód C#, R és Python.

Kattintson **a Swagger API-ra** a Swagger-alapú dokumentáció bekéréséhez a megadott URI-kból megnevezett API-khoz.

### <a name="c-sample"></a>C# minta
Ha egy Machine Learning webszolgáltatáshoz szeretne csatlakozni, használjon egy ScoreData-t átadva **http-ügyfélt.** A ScoreData egy FeatureVector-t tartalmaz, egy n-dimenziós numerikus funkciókat tartalmazó vektort, amely a ScoreData-t jelöli. A Machine Learning szolgáltatás api-kulcsával hitelesíti magát.

A Machine Learning webszolgáltatáshoz való csatlakozáshoz telepíteni kell a **Microsoft.AspNet.WebApi.Client** NuGet csomagot.

**A Microsoft.AspNet.WebApi.Client NuGet telepítése a Visual Studióban**

1. Tegye közzé az adatbázis letöltése az UCI: Adult 2 osztályú adatkészlet webszolgáltatásból.
2. Kattintson **az Eszközök** > **NuGet Csomagkezelő** > **konzol ra.**
3. Válassza **a Microsoft.AspNet.WebApi.Client telepítése**lehetőséget.

**A kódminta futtatása**

1. Tegye közzé a "1. minta: Adatkészlet letöltése az UCI:Adult 2 osztályú adatkészletből" kísérletet, amely a Machine Learning mintagyűjtemény része.
2. Rendelje hozzá az apiKey kulcsot egy webszolgáltatásból. Lásd: **Engedélyezési kulcs beszerezni** e fenti.
3. Rendelje hozzá a serviceUri-t a kérelem URI-jával.

**Itt van, amit a teljes kérelem fog kinézni.**
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
A Machine Learning webszolgáltatáshoz való csatlakozáshoz használja a Python 2.X **urllib2 könyvtárát** és a Python 3.X **urllib.request** könyvtárát. Át fogja adni a ScoreData-t, amely egy FeatureVector-t, egy numerikus funkciók n-dimenziós vektorját tartalmazza, amely a ScoreData-t jelöli. A Machine Learning szolgáltatás api-kulcsával hitelesíti magát.

**A kódminta futtatása**

1. Telepítse a "1. minta: Adatkészlet letöltése az UCI:Adult 2 osztályú adatkészletből" kísérlet, amely a Machine Learning-mintagyűjtemény része.
2. Rendelje hozzá az apiKey kulcsot egy webszolgáltatásból. Tekintse meg az **Engedélyezési kulcs beszereznie** szakaszt a cikk elején.
3. Rendelje hozzá a serviceUri-t a kérelem URI-jával.

**Itt van, amit a teljes kérelem fog kinézni.**
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

### <a name="r-sample"></a>R minta

A Machine Learning webszolgáltatáshoz való csatlakozáshoz használja az **RCurl** és **rjson** kódtárakat a kérés hez, és dolgozza fel a visszaadott JSON-választ. Át fogja adni a ScoreData-t, amely egy FeatureVector-t, egy numerikus funkciók n-dimenziós vektorját tartalmazza, amely a ScoreData-t jelöli. A Machine Learning szolgáltatás api-kulcsával hitelesíti magát.

**Itt van, amit a teljes kérelem fog kinézni.**
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

Ha egy Machine Learning webszolgáltatáshoz szeretne csatlakozni, használja a **projectnpm** kérelem-csomagot. Az objektumsegítségével `JSON` formázhatja a bevitelt, és elemezheti az eredményt. Telepítse `npm install request --save`a segítségével `"request": "*"` , vagy add hozzá `dependencies` a `npm install`package.json alatt, és fuss.

**Itt van, amit a teljes kérelem fog kinézni.**
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
