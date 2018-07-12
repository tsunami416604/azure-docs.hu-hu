---
title: Az Azure Machine Learning Modellkezelés webes szolgáltatás használata során |} A Microsoft Docs
description: Ez a dokumentum ismerteti a lépéseket és fogalmak vesz részt az Azure Machine Learning modellkezelés használatával üzembe helyezett webszolgáltatások felhasználása.
services: machine-learning
author: raymondlaghaeian
ms.author: raymondl
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/06/2017
ms.openlocfilehash: 4a49ccff68003cf7b81a7d945176992a2893d1ac
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38972634"
---
# <a name="consuming-web-services"></a>Webszolgáltatások felhasználása
Miután telepített egy modellt, valós idejű webszolgáltatásként, küldje el az adatokat, és kaphatnak előjelzéseket számos különböző eszközplatformról és alkalmazások. A valós idejű webszolgáltatás REST API-t az első előrejelzéseket tesz elérhetővé. A web Service, egyszerre egy vagy több előrejelzés beolvasása egyetlen vagy több sor formátumban küldhet adatokat.

Az a [Azure Machine Learning Web service](model-management-service-deploy.md), egy külső alkalmazás szinkron módon kommunikál a prediktív modellek azáltal, hogy a HTTP POST híváson a szolgáltatás URL-CÍMÉT. Ahhoz, hogy a webszolgáltatás felé irányuló hívások, az ügyfélalkalmazás kell adja meg az API-kulcsot, amely egy előrejelzési telepíti, és a kérelem adatai elhelyezi a POST-kérés törzse jön létre.

> [!NOTE]
> Vegye figyelembe, hogy API-kulcsok csak a fürt üzembe helyezési módban érhető el. Helyi webszolgáltatások nem rendelkezik a kulcsokat.

## <a name="service-deployment-options"></a>Szolgáltatás központi telepítési beállítások
Az Azure Machine Learning webszolgáltatások is telepíthető, a felhő alapú fürtök termelési és tesztelési célokra, valamint a helyi munkaállomáson docker-motor. A funkció a prediktív modell mindkét esetben ugyanaz marad. Fürt-alapú üzembe helyezési méretezhető biztosít, és nagy teljesítményű megoldás alapján az Azure Container Service, amíg a helyi telepítés hibakeresést is használható. 

Az Azure Machine Learning parancssori és API-t biztosít a kényelmes parancsokkal hozhat létre és kezelhet számítási környezetek a használatával üzembe helyezéséhez a ```az ml env``` lehetőséget. 

## <a name="list-deployed-services-and-images"></a>Lista telepített szolgáltatások és képek
A jelenleg telepített szolgáltatások és a Docker-rendszerképek CLI-paranccsal listázhatja ```az ml service list realtime -o table```. Vegye figyelembe, hogy ez a parancs mindig az aktuális számítási környezet összefüggésében működik. Nem állítható be az aktuális környezetben nem jelenik meg telepített szolgáltatásokat. Környezet használatának beállításához ```az ml env set```. 

## <a name="get-service-information"></a>Szolgáltatás adatai olvashatók be
Miután a web service telepítése sikerült, a következő paranccsal beolvasni a szolgáltatás URL-CÍMÉT és egyéb részletek a szolgáltatásvégpont hívása. 

```
az ml service usage realtime -i <web service id>
```

Ez a parancs kiírja ezt az URL-címe, szükséges kérelemfejlécek, swagger URL-cím és a szolgáltatás meghívása, ha a szolgáltatás API-séma a központi telepítéskor lett megadva a mintaadatokat.

A szolgáltatás közvetlenül a parancssori felület a HTTP-kérést összeállítása a bemeneti adatokat a mintául szolgáló parancssori parancs beírásával nélkül lehet tesztelni:

```
az ml service run realtime -i <web service id> -d "Your input data"
```

## <a name="get-the-service-api-key"></a>A szolgáltatás API-kulcs beszerzése
A webes kulcs lekéréséhez használja a következő parancsot:

```
az ml service keys realtime -i <web service id>
```
HTTP-kérés létrehozásakor használja a kulcsot az engedélyezési fejléc: "Engedélyezés": "tulajdonosi <key>"

## <a name="get-the-service-swagger-description"></a>A Swagger szolgáltatásleírás lekérése
Ha a szolgáltatás API-séma van megadva, a szolgáltatási végpont teszi ki a Swagger-dokumentumok ```http://<ip>/api/v1/service/<service name>/swagger.json```. A Swagger-dokumentumok automatikus létrehozásához a szolgáltatás ügyfele, és vizsgálja meg a várt bemeneti adatokat és egyéb adatait a szolgáltatás használható.

## <a name="get-service-logs"></a>Szolgáltatási naplók lekérése
A szolgáltatás viselkedésének megértéséhez, és diagnosztizálhatja a problémákat, számos módon a szolgáltatási naplók begyűjtéséről:
- CLI-paranccsal ```az ml service logs realtime -i <service id>```. Ezt a parancsot a fürt és a helyi módban is működik.
- Ha a szolgáltatás naplózás engedélyezve lett a központi telepítéskor, a szolgáltatás naplói is küld AppInsight. A CLI-paranccsal ```az ml service usage realtime -i <service id>``` AppInsight URL-CÍMÉT jeleníti meg. Vegye figyelembe, hogy a AppInsight-naplók késleltethetők 2 – 5 percet.
- Fürt naplóit tekinthetnek meg az aktuális fürt környezet beállításakor csatlakoztatott Kubernetes konzolon keresztül ```az ml env set```
- Helyi docker-naplók a helyben fut a szolgáltatás esetén a docker engine-naplók keresztül érhetők el.

## <a name="call-the-service-using-c"></a>Meghívja a szolgáltatást, C# használatával
A szolgáltatás URL-címe használatával egy kérelmet küld a C# Konzolalkalmazást. 

1. A Visual Studióban hozzon létre egy új Konzolalkalmazást: 
    * A menüben kattintson a, a fájl -> New Project ->
    * A Visual Studio C# nyelven írt Windows osztály asztali kattintson, majd válassza ki a Konzolalkalmazást.
2. Adja meg `MyFirstService` a projekt nevét, majd kattintson az OK gombra.
3. A projekt Referenciáihoz, hivatkozásainak beállítása `System.Net`, és `System.Net.Http`.
4. Kattintson az eszközök -> NuGet Package Manager Csomagkezelői konzol ->, majd telepítse a **Microsoft.AspNet.WebApi.Client** csomagot.
5. Nyissa meg **Program.cs** fájlt, és cserélje le a kódot az alábbira:
6. Frissítés a `SERVICE_URL` és `API_KEY` paraméterek a webszolgáltatás adataival.
7. Futtassa a projektet.

```csharp
using System;
using System.Collections.Generic;
using System.Net.Http;
using System.Net.Http.Headers;
using Newtonsoft.Json;

namespace MyFirstService
{
    class Program
    {
        // Web Service URL (update it with your service url)
        private const string SERVICE_URL = "http://<service ip address>:80/api/v1/service/<service name>/score";
        private const string API_KEY = "your service key";

        static void Main(string[] args)
        {
            Program.PostRequest();
            Console.ReadLine();
        }

        private static void PostRequest()
        {
            HttpClient client = new HttpClient();
            client.BaseAddress = new Uri(SERVICE_URL);
            //For local web service, comment out this line.
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", API_KEY);

            var inputJson = new List<RequestPayload>();
            RequestPayload payload = new RequestPayload();
            List<InputDf> inputDf = new List<InputDf>();
            inputDf.Add(new InputDf()
            {
                feature1 = value1,
                feature2 = value2,
            });
            payload.Input_df_list = inputDf;
            inputJson.Add(payload);

            try
            {
                var request = new HttpRequestMessage(HttpMethod.Post, string.Empty);
                request.Content = new StringContent(JsonConvert.SerializeObject(payload));
                request.Content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                var response = client.SendAsync(request).Result;

                Console.Write(response.Content.ReadAsStringAsync().Result);
            }
            catch (Exception e)
            {
                Console.Out.WriteLine(e.Message);
            }
        }
        public class InputDf
        {
            public double feature1 { get; set; }
            public double feature2 { get; set; }
        }
        public class RequestPayload
        {
            [JsonProperty("input_df")]
            public List<InputDf> Input_df_list { get; set; }
        }
    }
}
```

## <a name="call-the-web-service-using-python"></a>A Python használatával webes szolgáltatás hívása
A valós idejű webszolgáltatás-kérés küldése a Python használatával. 

1. Másolja a következő mintakód egy új Python-fájlt.
2. Frissítse az adatait, az URL-cím és api_key paramétereket. A helyi webszolgáltatások távolítsa el az "Engedélyezés" fejléc.
3. Futtassa a kódot. 

```python
import requests
import json

data = "{\"input_df\": [{\"feature1\": value1, \"feature2\": value2}]}"
body = str.encode(json.dumps(data))

url = 'http://<service ip address>:80/api/v1/service/<service name>/score'
api_key = 'your service key' 
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}

resp = requests.post(url, body, headers=headers)
resp.text
```
