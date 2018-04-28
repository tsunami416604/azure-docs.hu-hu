---
title: Az Azure Machine Learning modell felügyeleti webes szolgáltatások felhasználásához |} Microsoft Docs
description: Ez a dokumentum ismerteti a lépéseket és fogalmak telepített modell kezelése az Azure Machine Learning webszolgáltatások felhasználása részt.
services: machine-learning
author: raymondlaghaeian
ms.author: raymondl
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/06/2017
ms.openlocfilehash: 93df053bf62b5a69f74d816fc05520d040d73bd7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="consuming-web-services"></a>Webszolgáltatások felhasználása
Miután telepít egy modell valós idejű webszolgáltatásként, elküldi a adatok, és előrejelzéseket beolvasása számos különböző rendszerek és alkalmazások. A valós idejű webszolgáltatás közzétesz egy REST API előrejelzéseket kapcsolódnak. A webszolgáltatás egy vagy több előrejelzéseket egyszerre beolvasandó egyetlen vagy több sor formátumú adatokat küldhet.

Az a [Azure Machine Learning webszolgáltatás](model-management-service-deploy.md), külső alkalmazás szinkron módon kommunikál a prediktív modell azáltal, hogy a HTTP POST híváson a szolgáltatás URL-CÍMÉT. Ahhoz, hogy egy webes szolgáltatás hívása, az ügyfélalkalmazás kell meghatároznia az API-kulcsot, amely jön létre, amikor előrejelzéshez telepíti, és a kérelem adatai üzembe a POST kérés törzsében.

Ne feledje, hogy API-kulcsokat csak a fürt telepítési módban érhető el. Helyi webszolgáltatások nincs kulcsok.

## <a name="service-deployment-options"></a>Szolgáltatás központi telepítési lehetőségek
Az Azure Machine Learning webszolgáltatások is telepíthető, a felhő alapú fürtök is a termelési, és a tesztelési forgatókönyvek, és helyi munkaállomáson docker-motorhoz. Mindkét esetben a prediktív modell funkció változatlan marad. Fürt-alapú telepítés biztosítja, méretezhető és performant megoldás alapján Azure tárolószolgáltatások, amíg a helyi központi telepítést használhat a hibakeresés. 

Az Azure Machine Learning CLI és API biztosítja a létrehozására és kezelésére kényelmes parancsok számítási környezeteket szolgáltatás üzembe helyezése a ```az ml env``` lehetőséget. 

## <a name="list-deployed-services-and-images"></a>Lista telepített szolgáltatások és a képeket
A jelenleg telepített szolgáltatások és a Docker képek CLI paranccsal listázhatja ```az ml service list realtime -o table```. Vegye figyelembe, hogy ez a parancs mindig működik az aktuális számítási környezet környezetében. Azt nem mutat, amely a telepített szolgáltatások értéke nem lehet az aktuális környezetben. Környezet használatának beállításához ```az ml env set```. 

## <a name="get-service-information"></a>Szolgáltatás adatai beolvasása
A webszolgáltatás sikeres telepítése után a következő paranccsal beolvasni a szolgáltatás URL-CÍMÉT és egyéb adatait a szolgáltatás-végpont meghívása. 

```
az ml service usage realtime -i <web service id>
```

Ez a parancs megjeleníti a szolgáltatás URL-címe, szükséges kérelemfejléc, swagger URL-cím és a szolgáltatás hívása, ha a szolgáltatás API-séma a központi telepítéskor kapott a mintaadatok.

A szolgáltatás a CLI-ről, a HTTP-lekérdezés létrehozása a bemeneti adatokat tartalmazó minta CLI parancs beírásával nélkül lehet tesztelni:

```
az ml service run realtime -i <web service id> -d "Your input data"
```

## <a name="get-the-service-api-key"></a>Az API-kulcs beszerzése
A webes szolgáltatás kulcs beszerzéséhez a következő paranccsal:

```
az ml service keys realtime -i <web service id>
```
HTTP-kérelem létrehozásakor a billentyűvel hitelesítési fejlécéhez: "Engedélyezés": "tulajdonosi <key>"

## <a name="get-the-service-swagger-description"></a>Szolgáltatásleírás a Swagger
Ha a szolgáltatás API-séma, a szolgáltatási végpont teszi ki a Swagger-dokumentum, ```http://<ip>/api/v1/service/<service name>/swagger.json```. A Swagger-dokumentum automatikusan a szolgáltatásügyfél létrehozása és a szükséges bemeneti adatok és egyéb adatait a szolgáltatás használható.

## <a name="get-service-logs"></a>Szolgáltatás-naplófájlok
A szolgáltatás viselkedésének megértése és a problémák diagnosztizálásához, számos módon a szolgáltatásnaplók beolvasása:
- Parancssori felület parancs ```az ml service logs realtime -i <service id>```. Ez a parancs a fürt és a helyi módok működik.
- A szolgáltatás naplózási engedélyezve volt a központi telepítés, a service naplóit is kapnak AppInsight. A parancssori felület parancs ```az ml service usage realtime -i <service id>``` AppInsight URL-CÍMÉT jeleníti meg. Vegye figyelembe, hogy később, a AppInsight naplók által a 2 – 5 perc.
- Fürt naplók megtekinthetők, amely csatlakozik, ha úgy állítja be az aktuális fürtözött környezetben Kubernetes konzollal ```az ml env set```
- Helyi docker naplók helyben fut a szolgáltatás esetén a docker-motor naplók keresztül érhetők el.

## <a name="call-the-service-using-c"></a>Meghívja a szolgáltatást használó C#
A szolgáltatás URL-címe használatával egy C# Konzolalkalmazás a kérés küldése. 

1. A Visual Studióban hozzon létre egy új Konzolalkalmazás: 
    * A menüben kattintson a gombra, a fájl -> Új projekt ->
    * A Visual Studio C#, kattintson a Windows osztály asztali, majd válassza ki a Konzolalkalmazás.
2. Adja meg _MyFirstService_ , a projekt nevét, majd kattintson az OK gombra.
3. A projekt hivatkozásainak beállítása hivatkozások _System.NET névtérbeli_, és _System.Net.Http_.
4. Kattintson az eszközök -> NuGet-Csomagkezelő Csomagkezelő konzol ->, majd telepítse a Microsoft.AspNet.WebApi.Client csomagot.
5. Nyissa meg a Program.cs fájlt, és cserélje le a kódot az alábbira:
6. Frissítés a _SERVICE_URL_ és _API_KEY_ paraméterek a webszolgáltatás származó információkkal.
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

## <a name="call-the-web-service-using-python"></a>Hívja meg a webszolgáltatás pythonos környezetekben
Python segítségével kérelmet küld a valós idejű webszolgáltatás. 

1. Másolja az alábbi példakód egy új Python-fájl.
2. Frissítse az adatokat, az URL-cím és az api_key paraméterek. Helyi webszolgáltatások távolítsa el az "Engedélyezés" fejléc.
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
