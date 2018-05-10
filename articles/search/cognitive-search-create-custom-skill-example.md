---
title: 'Példa: Hozzon létre egy egyéni szakértelem kognitív keresési folyamat (Azure Search) |} Microsoft Docs'
description: Azt mutatja be, a feldolgozási sor az Azure Search indexelő kognitív keresés leképezve egyéni szakértelem szöveg állomásneveket, az API használatával.
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: a295bf741862bb58a86234b5c85f48d7a1b52be7
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="example-create-a-custom-skill-using-the-text-translate-api"></a>Példa: Hozzon létre egy egyéni szakértelem, a szöveg állomásneveket, az API-val

Ebben a példában az útmutató egy webes API egyéni szakértelem, amely elfogadja a szöveg bármilyen nyelven, és az eszköz az angol létrehozásához. A példa egy [Azure-függvény](https://azure.microsoft.com/services/functions/) burkolása a [fordítása szöveg API](https://azure.microsoft.com/services/cognitive-services/translator-text-api/) , hogy megvalósítja az egyéni szakértelem illesztőfelületet.

## <a name="prerequisites"></a>Előfeltételek

+ További információ a [egyéni szakértelem felület](cognitive-search-custom-skill-interface.md) a következő cikket: Ha nem ismeri a bemeneti/kimeneti felületet, amely egy egyéni szakértelem meg kell valósítania.

+ [Iratkozzon fel a fordító szöveg API](../cognitive-services/translator/translator-text-how-to-signup.md), és felhasználhatják az API-kulcs beszerzése.

+ Telepítés [Visual Studio 2017 verzió 15.5](https://www.visualstudio.com/vs/) vagy újabb, beleértve az Azure fejlesztési munkaterhelés.

## <a name="create-an-azure-function"></a>Azure-függvény létrehozása

Bár ebben a példában egy Azure-függvény egy webes API-t használ, nincs szükség.  Mindaddig, amíg megfelel a [kognitív szakértelem követelményei csatoló](cognitive-search-custom-skill-interface.md), a módszert választja, nincs jelentősége. Az Azure Functions, azonban könnyen hozzon létre egy egyéni szakértelem.

### <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

1. A Visual Studio válassza **új** > **projekt** a Fájl menüből.

1. Válassza ki az új projekt párbeszédpanel **telepített**, bontsa ki a **Visual C#** > **felhő**, jelölje be **Azure Functions**, adjon meg egy A projekt neve, és válassza ki **OK**. A függvényalkalmazás nevének egy C#-névtérként is érvényesnek kell lennie, ezért ne használjon aláhúzásjeleket, kötőjeleket vagy más nem alfanumerikus karaktereket.

1. Jelölje ki a kell **HTTP-eseményindítóval**

1. A Storage-fiókot, előfordulhat, hogy válassza **nincs**, mert nincs szükség minden tárterület ennél a függvénynél.

1. Válassza ki **OK** a függvény létrehozásához a projekt és HTTP indított függvény.

### <a name="modify-the-code-to-call-the-translate-cognitive-service"></a>A hívó fordítása kognitív kód módosítása

A Visual Studio létrehoz egy projektet, benne egy olyan osztállyal, amely tartalmazza a kiválasztott függvénytípus sablonkódját. A metódus *FunctionName* attribútuma adja meg a függvény nevét. A *HttpTrigger* attribútum adja meg, hogy a függvényt egy HTTP-kérelem aktiválja.

Most, cserélje le a fájl tartalma mindegyikét *Function1.cs* az alábbi kódra:

```csharp
using System.IO;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Azure.WebJobs.Host;
using Newtonsoft.Json;
using System.Net.Http;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Text;

namespace TranslateFunction
{
    // This function will simply translate messages sent to it.
    public static class Function1
    {
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


        /// <summary>
        /// Note that this function can translate up to 1000 characters. If you expect to need to translate more characters, use 
        /// the paginator skill before calling this custom enricher
        /// </summary>
        [FunctionName("Translate")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)]HttpRequest req, 
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            string recordId = null;
            string originalText = null;
            string originalLanguage = null;
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
            originalLanguage = data?.values?.First?.data?.language?.Value as string;

            if (recordId == null)
            {
                return new BadRequestObjectResult("recordId cannot be null");
            }

            // Only translate records that actually need to be translated. 
            if (!originalLanguage.Contains("en"))
            {
                translatedText = TranslateText(originalText, "en-us").Result;
            }
            else
            {
                // text is already in English.
                translatedText = originalText;
            }

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
        /// Use Cognitive Service to translate text from one language to antoher.
        /// </summary>
        /// <param name="myText">The text to translate</param>
        /// <param name="destinationLanguage">The language you want to translate to.</param>
        /// <returns>Asynchronous task that returns the translated text. </returns>
        async static Task<string> TranslateText(string myText, string destinationLanguage)
        {
            string host = "https://api.microsofttranslator.com";
            string path = "/V2/Http.svc/Translate";

            // NOTE: Replace this example key with a valid subscription key.
            string key = "064d8095730d4a99b49f4bcf16ac67f8";

            HttpClient client = new HttpClient();
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

            List<KeyValuePair<string, string>> list = new List<KeyValuePair<string, string>>() {
                new KeyValuePair<string, string>(myText, "en-us")
            };

            StringBuilder totalResult = new StringBuilder();

            foreach (KeyValuePair<string, string> i in list)
            {
                string uri = host + path + "?to=" + i.Value + "&text=" + System.Net.WebUtility.UrlEncode(i.Key);

                HttpResponseMessage response = await client.GetAsync(uri);

                string result = await response.Content.ReadAsStringAsync();

                // Parse the response XML
                System.Xml.XmlDocument xmlResponse = new System.Xml.XmlDocument();
                xmlResponse.LoadXml(result);
                totalResult.Append(xmlResponse.InnerText); 
            }

            return totalResult.ToString();
        }
    }
}
```

Ügyeljen arra, hogy adja meg a saját *kulcs* értéket a *TranslateText* metódus portáltól, amikor regisztrál az a szöveg fordítása API kulcs alapján.

Ebben a példában egy egyszerű enricher, amely csak akkor működik a több rekordot egyszerre. Ez a tény válik fontos később, a kötegméret a skillset beállításakor.

## <a name="test-the-function-from-visual-studio"></a>A Visual Studio eszközből a függvény tesztelése

Nyomja le az **F5** a program és a vizsgálati függvény viselkedéseket futtatásához. Postman vagy a Fiddler segítségével adja ki az alábbihoz hasonló hívása:

```http
POST https://localhost:7071/api/Translate
```
### <a name="request-body"></a>Kérelem törzse
```json
{
   "values": [
        {
            "recordId": "a1",
            "data":
            {
               "text":  "Este es un contrato en Inglés",
               "language": "es"
            }
        }
   ]
}
```
### <a name="response"></a>Válasz
A következőhöz hasonló választ kell megjelennie:

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

1. A **Megoldáskezelőben** kattintson a jobb gombbal a projektre, és válassza a **Publish** (Közzététel) lehetőséget. Válasszon **új** > **közzététele**.

1. Ha a Visual Studio már Azure-fiókja még nem kapcsolódik, válassza ki a **fiók hozzáadása...**

1. Kövesse a képernyőn megjelenő kéri. Az Azure-fiók, az erőforráscsoport, a üzemeltetési terv és a használni kívánt tárfiókot meg kell adnia. Ha még nem rendelkezik ezek létrehozhat egy új erőforráscsoportot, egy új üzemeltetési terv és a storage-fiók. Ha elkészült, válassza ki a **létrehozása**

1. A telepítés befejezése után jegyezze fel a webhely URL-címe. A cím, a függvény alkalmazás az Azure-ban. 

1. Az a [Azure-portálon](https://portal.azure.com), keresse meg az erőforráscsoportot, és keresse meg a közzétett fordítása függvény. Az a **kezelése** szakaszban megtekintheti az állomások kulcsait. Válassza ki a **másolási** ikonjára a *alapértelmezett* állomás kulcsát.  


## <a name="test-the-function-in-azure"></a>A függvény tesztelése az Azure-ban

Most, hogy az alapértelmezett gazdagép kulcs, a függvény tesztelése az alábbiak szerint:

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
               "language": "es"
            }
        }
   ]
}
```

Ez a kell létrehoz egy hasonló eredményt korábban látott a funkció a helyi környezetben futtatásakor.

## <a name="connect-to-your-pipeline"></a>A folyamat kapcsolódni
Most, hogy egy új egyéni szakértelem, hozzáadhatja a skillset. Az alábbi példa bemutatja, hogyan szakértelem hívja. Mivel a szakértelem kötegek nem tud kezelni, vegye fel a Köteg maximális mérete csak utasítás ```1``` küldendő dokumentumok egyenként.

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
            "source": "/document/languageCode"
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
Gratulálunk! Az első egyéni enricher hozott létre. Most követésével ugyanilyen mintájú hozzá a saját egyéni funkciót. 

+ [Egyéni szakértelem hozzáadása egy kognitív keresési folyamat](cognitive-search-custom-skill-interface.md)
+ [Egy skillset definiálása](cognitive-search-defining-skillset.md)
+ [Hozzon létre Skillset (REST)](ref-create-skillset.md)
+ [Hogyan bővített mezők](cognitive-search-output-field-mapping.md)