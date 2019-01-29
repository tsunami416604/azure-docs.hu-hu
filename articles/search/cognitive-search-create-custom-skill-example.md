---
title: 'Példa: Hozzon létre egy egyéni ismeretek cognitive search folyamat – Azure Search'
description: Azt mutatja be, a szöveg lefordítása API-val az egyéni ismeretek egy folyamatot az Azure Search szolgáltatásban az indexelés kognitív keresés leképezve.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 06/29/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: c49a6320b7ba37297a7c1c0775aef2c2d8c6c868
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55104350"
---
# <a name="example-create-a-custom-skill-using-the-text-translate-api"></a>Példa: Hozzon létre egy egyéni ismeretek a szöveg lefordítása API-val

Ebben a példában megtudhatja, hogyan hozhat létre egy webes API-t egyéni ismeretek, amely elfogadja a szöveg bármilyen nyelven, és lefordítja azt angolra. A példában egy [Azure-függvény](https://azure.microsoft.com/services/functions/) burkolása a [fordítása Text API](https://azure.microsoft.com/services/cognitive-services/translator-text-api/) úgy, hogy az egyéni ismeretek felületet valósítja meg.

## <a name="prerequisites"></a>Előfeltételek

+ Olvassa el [egyéni ismeretek felület](cognitive-search-custom-skill-interface.md) című cikket, ha nem ismeri a bemeneti és kimeneti felület, amely egy egyéni függetlenül kell megvalósítania.

+ [Iratkozzon fel a Translator Text API](../cognitive-services/translator/translator-text-how-to-signup.md), és felhasználni, API-kulcs beszerzése.

+ Telepítés [Visual Studio 2017 15.5 verzió](https://www.visualstudio.com/vs/) vagy újabb, mint például az Azure-fejlesztési számítási feladatot.

## <a name="create-an-azure-function"></a>Azure-függvény létrehozása

Bár ebben a példában egy Azure-függvényt használ a webes API-k üzemeltetéséhez, ennek kitöltése nem kötelező.  Mindaddig, amíg megfelel a [cognitive szakértelem követelményei csatoló](cognitive-search-custom-skill-interface.md), a módszert választja, nincs jelentősége. Az Azure Functions, azonban könnyen hozzon létre egy egyéni ismeretek.

### <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

1. A Visual Studióban válassza ki a **új** > **projekt** a Fájl menüből.

1. Új projekt párbeszédpanelen válassza ki a **telepített**, bontsa ki a **Visual C#** > **felhőalapú**, jelölje be **Azure Functions**, adjon meg egy A projekt nevét, és válassza ki **OK**. A függvényalkalmazás nevének egy C#-névtérként is érvényesnek kell lennie, ezért ne használjon aláhúzásjeleket, kötőjeleket vagy más nem alfanumerikus karaktereket.

1. Válassza ki **az Azure Functions v2 (.Net Core)**. Az 1. verzió is megteheti, de a kódot írt alá a v2 sablonon alapul.

1. Válassza ki a kívánt **HTTP-eseményindító**

1. A Storage-fiókot választ, kiválaszthat **None**, minden tároló nem szükséges ezt a funkciót.

1. Válassza ki **OK** a függvény létrehozásához projekt és a HTTP által aktivált függvény.

### <a name="modify-the-code-to-call-the-translate-cognitive-service"></a>A kód fordítása a Cognitive Services meghívásához módosítása

A Visual Studio létrehoz egy projektet, benne egy olyan osztállyal, amely tartalmazza a kiválasztott függvénytípus sablonkódját. A metódus *FunctionName* attribútuma adja meg a függvény nevét. A *HttpTrigger* attribútum adja meg, hogy a függvényt egy HTTP-kérelem aktiválja.

Most cserélje le az összes, a fájl tartalmának *Function1.cs* a következő kóddal:

```csharp
using System;
using System.Net.Http;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.IO;
using System.Text;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Azure.WebJobs.Host;
using Newtonsoft.Json;

namespace TranslateFunction
{
    // This function will simply translate messages sent to it.
    public static class Function1
    {
        static string path = "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0";

        // NOTE: Replace this example key with a valid subscription key.
        static string key = "<enter your api key here>";

        #region classes used to serialize the response
        private class WebApiResponseError
        {
            public string message { get; set; }
        }

        private class WebApiResponseWarning
        {
            public string message { get; set; }
        }

        private class WebApiResponseRecord
        {
            public string recordId { get; set; }
            public Dictionary<string, object> data { get; set; }
            public List<WebApiResponseError> errors { get; set; }
            public List<WebApiResponseWarning> warnings { get; set; }
        }

        private class WebApiEnricherResponse
        {
            public List<WebApiResponseRecord> values { get; set; }
        }
        #endregion

        [FunctionName("Translate")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)]HttpRequest req,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            string recordId = null;
            string originalText = null;
            string toLanguage = null;
            string translatedText = null;

            string requestBody = new StreamReader(req.Body).ReadToEnd();
            dynamic data = JsonConvert.DeserializeObject(requestBody);

            // Validation
            if (data?.values == null)
            {
                return new BadRequestObjectResult(" Could not find values array");
            }
            if (data?.values.HasValues == false || data?.values.First.HasValues == false)
            {
                // It could not find a record, then return empty values array.
                return new BadRequestObjectResult(" Could not find valid records in values array");
            }

            recordId = data?.values?.First?.recordId?.Value as string;
            originalText = data?.values?.First?.data?.text?.Value as string;
            toLanguage = data?.values?.First?.data?.language?.Value as string;

            if (recordId == null)
            {
                return new BadRequestObjectResult("recordId cannot be null");
            }

            translatedText = TranslateText(originalText, toLanguage).Result;
        
            // Put together response.
            WebApiResponseRecord responseRecord = new WebApiResponseRecord();
            responseRecord.data = new Dictionary<string, object>();
            responseRecord.recordId = recordId;
            responseRecord.data.Add("text", translatedText);

            WebApiEnricherResponse response = new WebApiEnricherResponse();
            response.values = new List<WebApiResponseRecord>();
            response.values.Add(responseRecord);

            return (ActionResult)new OkObjectResult(response);
        }


        /// <summary>
        /// Use Cognitive Service to translate text from one language to another.
        /// </summary>
        /// <param name="originalText">The text to translate.</param>
        /// <param name="toLanguage">The language you want to translate to.</param>
        /// <returns>Asynchronous task that returns the translated text. </returns>
        async static Task<string> TranslateText(string originalText, string toLanguage)
        {
            System.Object[] body = new System.Object[] { new { Text = originalText } };
            var requestBody = JsonConvert.SerializeObject(body);

            var uri = $"{path}&to={toLanguage}";

            string result = "";

            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                var responseBody = await response.Content.ReadAsStringAsync();

                dynamic data = JsonConvert.DeserializeObject(responseBody);
                result = data?.First?.translations?.First?.text?.Value as string;

            }
            return result;
        }
    }
}
```

Ügyeljen arra, hogy adja meg a saját *kulcs* értékét a *TranslateText* metódus a fordítás API való regisztráláskor kapott kulcs alapján.

Ebben a példában egy egyszerű enricher, amely egyszerre csak egy rekordot a működik. Ez a tény akkor válik fontossá, később, a kötegméret készségeitől beállításakor.

## <a name="test-the-function-from-visual-studio"></a>A Visual Studióból a függvény tesztelése

Nyomja meg **F5** program és tesztelési függvény viselkedések futtatásához. Ebben az esetben az alábbi függvény segítségével egy angol, spanyol nyelven használható szövegfordításra. Postman vagy a Fiddler segítségével ki egy hívást, mint például az alábbi képen látható:

```http
POST https://localhost:7071/api/Translate
```
### <a name="request-body"></a>A kérés törzse
```json
{
   "values": [
        {
            "recordId": "a1",
            "data":
            {
               "text":  "Este es un contrato en Inglés",
               "language": "en"
            }
        }
   ]
}
```
### <a name="response"></a>Válasz
Az alábbi példához hasonló választ kell megjelennie:

```json
{
    "values": [
        {
            "recordId": "a1",
            "data": {
                "text": "This is a contract in English"
            },
            "errors": null,
            "warnings": null
        }
    ]
}
```

## <a name="publish-the-function-to-azure"></a>A függvény közzététele az Azure-bA

Ha elégedett a függvény működése, közzéteheti azt.

1. A **Megoldáskezelőben** kattintson a jobb gombbal a projektre, és válassza a **Publish** (Közzététel) lehetőséget. Válasszon **létrehozása új** > **közzététele**.

1. Ha a Visual Studio még nem csatlakoztatta az Azure-fiókjába, válassza ki a **-fiók hozzáadása...**

1. Kövesse a képernyőn megjelenő utasításokat. A rendszer felkéri az Azure-fiók, az erőforráscsoport, a szolgáltatási csomag és a használni kívánt tárfiókot adjon meg. Ha még nincs ilyen létrehozhat egy új erőforráscsoportot, egy új szolgáltatási csomagot és egy tárfiókot. Amikor végzett, válassza ki a **létrehozása**

1. Az üzembe helyezés befejezése után jegyezze fel a webhely URL-címe. Az Azure-ban a függvényalkalmazás címe. 

1. Az a [az Azure portal](https://portal.azure.com), keresse meg az erőforráscsoportot, és keresse meg a közzétett fordítása függvény. Alatt a **kezelés** szakaszban Gazdakulcsok kell megjelennie. Válassza ki a **másolási** ikonjára a *alapértelmezett* állomás kulcsát.  

## <a name="test-the-function-in-azure"></a>A függvény tesztelése az Azure-ban

Most, hogy az alapértelmezett gazdagép kulcs, a függvény tesztelése a következőképpen:

```http
POST https://translatecogsrch.azurewebsites.net/api/Translate?code=[enter default host key here]
```
### <a name="request-body"></a>A kérelem törzse
```json
{
   "values": [
        {
            "recordId": "a1",
            "data":
            {
               "text":  "Este es un contrato en Inglés",
               "language": "en"
            }
        }
   ]
}
```

Ebben a korábban látott a helyi környezetben a függvény futtatásakor egy hasonló eredményt kell hozhatók létre.

## <a name="connect-to-your-pipeline"></a>A folyamat kapcsolódni
Most, hogy egy új egyéni ismeretek, adhat hozzá, a képességek alkalmazási lehetőségét. Az alábbi példa bemutatja, hogyan hívhat meg a szakértelem. Szakértelem nem kezeli az kötegekben, mivel a maximális kötegméretet kell csak egy utasítás hozzáadása ```1``` küldése dokumentumok egyenként.

```json
{
    "skills": [
      ...,  
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "Our new translator custom skill",
        "uri": "http://translatecogsrch.azurewebsites.net/api/Translate?code=[enter default host key here]",
        "batchSize":1,
        "context": "/document",
        "inputs": [
          {
            "name": "text",
            "source": "/document/content"
          },
          {
            "name": "language",
            "source": "/document/destinationLanguage"
          }
        ],
        "outputs": [
          {
            "name": "text",
            "targetName": "translatedText"
          }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>További lépések
Gratulálunk! Az első egyéni enricher hozott létre. Most ugyanezt a mintát adhat hozzá a saját egyéni funkciókat is követheti. 

+ [Egy egyéni ismeretek hozzáadása a cognitive search folyamat](cognitive-search-custom-skill-interface.md)
+ [Hogyan képességcsoport megadása](cognitive-search-defining-skillset.md)
+ [Képességcsoport (REST) létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Hogyan hidaljuk mezők leképezése](cognitive-search-output-field-mapping.md)
