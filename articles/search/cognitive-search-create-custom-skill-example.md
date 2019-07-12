---
title: 'Példa: A Bing Entity Search API – Azure Search egy egyéni cognitive szakértelem létrehozása'
description: Azt mutatja be, a Bing Entity Search szolgáltatás használata az Azure Search szolgáltatásban a kognitív Keresés indexelése folyamat leképezve egyéni műveleteket.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 7d90f46ada9b9453b4c1516a4a898456dc73b8e7
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672145"
---
# <a name="example-create-a-custom-skill-using-the-bing-entity-search-api"></a>Példa: Hozzon létre egy egyéni ismeretek a Bing Entity Search API használatával

Ebben a példában megtudhatja, hogyan hozhat létre egy webes API-t egyéni ismeretek. Ezen a képzettségi fogadja el a helyek, a nyilvános adatokat és a szervezetek, és azok leírását adja vissza. A példában egy [Azure-függvény](https://azure.microsoft.com/services/functions/) burkolása a [Bing Entity Search API](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) úgy, hogy az egyéni ismeretek felületet valósítja meg.

## <a name="prerequisites"></a>Előfeltételek

+ További információ [egyéni ismeretek felület](cognitive-search-custom-skill-interface.md) című cikket, ha nem ismeri a bemeneti és kimeneti felület, amely egy egyéni függetlenül kell megvalósítania.

+ [!INCLUDE [cognitive-services-bing-entity-search-signup-requirements](../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

+ Telepítés [Visual Studio 2019](https://www.visualstudio.com/vs/) vagy újabb, mint például az Azure-fejlesztési számítási feladatot.

## <a name="create-an-azure-function"></a>Azure-függvény létrehozása

Bár ebben a példában egy Azure-függvényt használ a webes API-k üzemeltetéséhez, nem szükséges.  Mindaddig, amíg megfelel a [cognitive szakértelem követelményei csatoló](cognitive-search-custom-skill-interface.md), a módszert választja, nincs jelentősége. Az Azure Functions, azonban könnyen hozzon létre egy egyéni ismeretek.

### <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

1. A Visual Studióban válassza ki a **új** > **projekt** a Fájl menüből.

1. Új projekt párbeszédpanelen válassza ki a **telepített**, bontsa ki a **Visual C#**  > **felhőalapú**, jelölje be **Azure Functions**, adjon meg egy A projekt nevét, és válassza ki **OK**. A függvényalkalmazás nevét, érvényesnek kell lennie egy C# névtér, ezért ne használjon aláhúzásjeleket, kötőjeleket vagy bármely más nem alfanumerikus karakterek.

1. Válassza ki **az Azure Functions v2 (.NET Core)** . Az 1. verzió is megteheti, de a kódot írt alá a v2 sablonon alapul.

1. Válassza ki a kívánt **HTTP-eseményindító**

1. A Storage-fiókot választ, kiválaszthat **None**, minden tároló nem szükséges ezt a funkciót.

1. Válassza ki **OK** a függvény létrehozásához projekt és a HTTP által aktivált függvény.

### <a name="modify-the-code-to-call-the-bing-entity-search-service"></a>A Bing Entity Search szolgáltatás hívása a kód módosítása

A Visual Studio létrehoz egy projektet, benne egy olyan osztállyal, amely tartalmazza a kiválasztott függvénytípus sablonkódját. A metódus *FunctionName* attribútuma adja meg a függvény nevét. A *HttpTrigger* attribútum adja meg, hogy a függvényt egy HTTP-kérelem aktiválja.

Most cserélje le az összes, a fájl tartalmának *Function1.cs* a következő kóddal:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;

namespace SampleSkills
{
    /// <summary>
    /// Sample custom skill that wraps the Bing entity search API to connect it with a 
    /// cognitive search pipeline.
    /// </summary>
    public static class BingEntitySearch
    {
        #region Credentials
        // IMPORTANT: Make sure to enter your credential and to verify the API endpoint matches yours.
        static readonly string bingApiEndpoint = "https://api.cognitive.microsoft.com/bing/v7.0/entities/";
        static readonly string key = "<enter your api key here>";  
        #endregion

        #region Class used to deserialize the request
        private class InputRecord
        {
            public class InputRecordData
            {
                public string Name { get; set; }
            }

            public string RecordId { get; set; }
            public InputRecordData Data { get; set; }
        }

        private class WebApiRequest
        {
            public List<InputRecord> Values { get; set; }
        }
        #endregion

        #region Classes used to serialize the response

        private class OutputRecord
        {
            public class OutputRecordData
            {
                public string Name { get; set; } = "";
                public string Description { get; set; } = "";
                public string Source { get; set; } = "";
                public string SourceUrl { get; set; } = "";
                public string LicenseAttribution { get; set; } = "";
                public string LicenseUrl { get; set; } = "";
            }

            public class OutputRecordMessage
            {
                public string Message { get; set; }
            }

            public string RecordId { get; set; }
            public OutputRecordData Data { get; set; }
            public List<OutputRecordMessage> Errors { get; set; }
            public List<OutputRecordMessage> Warnings { get; set; }
        }

        private class WebApiResponse
        {
            public List<OutputRecord> Values { get; set; }
        }
        #endregion

        #region Classes used to interact with the Bing API
        private class BingResponse
        {
            public BingEntities Entities { get; set; }
        }
        private class BingEntities
        {
            public BingEntity[] Value { get; set; }
        }

        private class BingEntity
        {
            public class EntityPresentationinfo
            {
                public string[] EntityTypeHints { get; set; }
            }

            public class License
            {
                public string Url { get; set; }
            }

            public class ContractualRule
            {
                public string _type { get; set; }
                public License License { get; set; }
                public string LicenseNotice { get; set; }
                public string Text { get; set; }
                public string Url { get; set; }
            }

            public ContractualRule[] ContractualRules { get; set; }
            public string Description { get; set; }
            public string Name { get; set; }
            public EntityPresentationinfo EntityPresentationInfo { get; set; }
        }
        #endregion

        #region The Azure Function definition

        [FunctionName("EntitySearch")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("Entity Search function: C# HTTP trigger function processed a request.");

            var response = new WebApiResponse
            {
                Values = new List<OutputRecord>()
            };

            string requestBody = new StreamReader(req.Body).ReadToEnd();
            var data = JsonConvert.DeserializeObject<WebApiRequest>(requestBody);

            // Do some schema validation
            if (data == null)
            {
                return new BadRequestObjectResult("The request schema does not match expected schema.");
            }
            if (data.Values == null)
            {
                return new BadRequestObjectResult("The request schema does not match expected schema. Could not find values array.");
            }

            // Calculate the response for each value.
            foreach (var record in data.Values)
            {
                if (record == null || record.RecordId == null) continue;

                OutputRecord responseRecord = new OutputRecord
                {
                    RecordId = record.RecordId
                };

                try
                {
                    responseRecord.Data = GetEntityMetadata(record.Data.Name).Result;
                }
                catch (Exception e)
                {
                    // Something bad happened, log the issue.
                    var error = new OutputRecord.OutputRecordMessage
                    {
                        Message = e.Message
                    };

                    responseRecord.Errors = new List<OutputRecord.OutputRecordMessage>
                    {
                        error
                    };
                }
                finally
                {
                    response.Values.Add(responseRecord);
                }
            }

            return (ActionResult)new OkObjectResult(response);
        }

        #endregion

        #region Methods to call the Bing API
        /// <summary>
        /// Gets metadata for a particular entity based on its name using Bing Entity Search
        /// </summary>
        /// <param name="entityName">The name of the entity to extract data for.</param>
        /// <returns>Asynchronous task that returns entity data. </returns>
        private async static Task<OutputRecord.OutputRecordData> GetEntityMetadata(string entityName)
        {
            var uri = bingApiEndpoint + "?q=" + entityName + "&mkt=en-us&count=10&offset=0&safesearch=Moderate";
            var result = new OutputRecord.OutputRecordData();

            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage {
                Method = HttpMethod.Get,
                RequestUri = new Uri(uri)
            })
            {
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                HttpResponseMessage response = await client.SendAsync(request);
                string responseBody = await response?.Content?.ReadAsStringAsync();

                BingResponse bingResult = JsonConvert.DeserializeObject<BingResponse>(responseBody);
                if (bingResult != null)
                {
                    // In addition to the list of entities that could match the name, for simplicity let's return information
                    // for the top match as additional metadata at the root object.
                    return AddTopEntityMetadata(bingResult.Entities?.Value);
                }
            }

            return result;
        }

        private static OutputRecord.OutputRecordData AddTopEntityMetadata(BingEntity[] entities)
        {
            if (entities != null)
            {
                foreach (BingEntity entity in entities.Where(
                    entity => entity?.EntityPresentationInfo?.EntityTypeHints != null
                        && (entity.EntityPresentationInfo.EntityTypeHints[0] == "Person"
                            || entity.EntityPresentationInfo.EntityTypeHints[0] == "Organization"
                            || entity.EntityPresentationInfo.EntityTypeHints[0] == "Location")
                        && !String.IsNullOrEmpty(entity.Description)))
                {
                    var rootObject = new OutputRecord.OutputRecordData
                    {
                        Description = entity.Description,
                        Name = entity.Name
                    };

                    if (entity.ContractualRules != null)
                    {
                        foreach (var rule in entity.ContractualRules)
                        {
                            switch (rule._type)
                            {
                                case "ContractualRules/LicenseAttribution":
                                    rootObject.LicenseAttribution = rule.LicenseNotice;
                                    rootObject.LicenseUrl = rule.License.Url;
                                    break;
                                case "ContractualRules/LinkAttribution":
                                    rootObject.Source = rule.Text;
                                    rootObject.SourceUrl = rule.Url;
                                    break;
                            }
                        }
                    }

                    return rootObject;
                }
            }

            return new OutputRecord.OutputRecordData();
        }
        #endregion
    }
}
```

Ügyeljen arra, hogy adja meg a saját *kulcs* értékét a `key` állandót akkor kapott, amikor regisztrál a Bing entity search API kulcs alapján.

Ez a minta kényelmi egyetlen fájlban az összes szükséges kódot tartalmaz. Annak, hogy az azonos ismeretek strukturáltabb némileg verziója [az energiagazdálkodási képességek tárház](https://github.com/Azure-Samples/azure-search-power-skills/tree/master/Text/BingEntitySearch).

Természetesen, előfordulhat, hogy nevezze át a fájlt `Function1.cs` való `BingEntitySearch.cs`.

## <a name="test-the-function-from-visual-studio"></a>A Visual Studióból a függvény tesztelése

Nyomja meg **F5** program és tesztelési függvény viselkedések futtatásához. Az alábbi függvény ebben az esetben két entitás keresése használjuk. Postman vagy a Fiddler segítségével ki egy hívást, mint például az alábbi képen látható:

```http
POST https://localhost:7071/api/EntitySearch
```

### <a name="request-body"></a>A kérés törzse
```json
{
    "values": [
        {
            "recordId": "e1",
            "data":
            {
                "name":  "Pablo Picasso"
            }
        },
        {
            "recordId": "e2",
            "data":
            {
                "name":  "Microsoft"
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
            "recordId": "e1",
            "data": {
                "name": "Pablo Picasso",
                "description": "Pablo Ruiz Picasso was a Spanish painter [...]",
                "source": "Wikipedia",
                "sourceUrl": "http://en.wikipedia.org/wiki/Pablo_Picasso",
                "licenseAttribution": "Text under CC-BY-SA license",
                "licenseUrl": "http://creativecommons.org/licenses/by-sa/3.0/"
            },
            "errors": null,
            "warnings": null
        },
        "..."
    ]
}
```

## <a name="publish-the-function-to-azure"></a>A függvény közzététele az Azure-bA

Ha elégedett a függvény működése, közzéteheti azt.

1. A **Megoldáskezelőben** kattintson a jobb gombbal a projektre, és válassza a **Publish** (Közzététel) lehetőséget. Válasszon **létrehozása új** > **közzététele**.

1. Ha a Visual Studio még nem csatlakoztatta az Azure-fiókjába, válassza ki a **-fiók hozzáadása...**

1. Kövesse a képernyőn megjelenő utasításokat. Kéri, hogy adjon meg egy egyedi nevet az app service, az Azure-előfizetést, az erőforráscsoport, a szolgáltatási csomag és a használni kívánt tárfiókot. Ha még nincs ilyen létrehozhat egy új erőforráscsoportot, egy új szolgáltatási csomagot és egy tárfiókot. Amikor végzett, válassza ki a **létrehozása**

1. A telepítés befejezését követően figyelje meg, hogy a webhely URL-címe. Az Azure-ban a függvényalkalmazás címe. 

1. Az a [az Azure portal](https://portal.azure.com), keresse meg az erőforráscsoportot, és keresse meg a `EntitySearch` közzétett függvény. Alatt a **kezelés** szakaszban Gazdakulcsok kell megjelennie. Válassza ki a **másolási** ikonjára a *alapértelmezett* állomás kulcsát.  

## <a name="test-the-function-in-azure"></a>A függvény tesztelése az Azure-ban

Most, hogy az alapértelmezett gazdagép kulcs, a függvény tesztelése a következőképpen:

```http
POST https://[your-entity-search-app-name].azurewebsites.net/api/EntitySearch?code=[enter default host key here]
```

### <a name="request-body"></a>A kérelem törzse
```json
{
    "values": [
        {
            "recordId": "e1",
            "data":
            {
                "name":  "Pablo Picasso"
            }
        },
        {
            "recordId": "e2",
            "data":
            {
                "name":  "Microsoft"
            }
        }
    ]
}
```

Ebben a példában ugyanazt az eredményt korábban már látott a funkció a helyi környezetben való futtatáskor kell előállítania.

## <a name="connect-to-your-pipeline"></a>A folyamat kapcsolódni
Most, hogy egy új egyéni ismeretek, adhat hozzá, a képességek alkalmazási lehetőségét. Az alábbi példa bemutatja, hogyan hívhat meg a szakértelem leírások hozzáadása a szervezetek számára a dokumentum (Ez sikerült kell terjeszteni a helyek és a felhasználók is működik). Cserélje le `[your-entity-search-app-name]` az alkalmazás nevére.

```json
{
    "skills": [
      "[... your existing skills remain here]",  
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "Our new Bing entity search custom skill",
        "uri": "https://[your-entity-search-app-name].azurewebsites.net/api/EntitySearch?code=[enter default host key here]",
          "context": "/document/merged_content/organizations/*",
          "inputs": [
            {
              "name": "name",
              "source": "/document/merged_content/organizations/*"
            }
          ],
          "outputs": [
            {
              "name": "description",
              "targetName": "description"
            }
          ]
      }
  ]
}
```

Itt azt Ön leltár a beépített a [entitások felismerése szakértelem](cognitive-search-skill-entity-recognition.md) megtalálható a képességek alkalmazási lehetőségét, és a szervezetek listáját tartalmazó dokumentum gyarapítása. Referenciaként itt látható egy entitás kinyerési szakértelem konfigurációt, amely elegendő az adatokat kell létrehozni:

```json
{
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "name": "#1",
    "description": "Organization name extraction",
    "context": "/document/merged_content",
    "categories": [ "Organization" ],
    "defaultLanguageCode": "en",
    "inputs": [
        {
            "name": "text",
            "source": "/document/merged_content"
        },
        {
            "name": "languageCode",
            "source": "/document/language"
        }
    ],
    "outputs": [
        {
            "name": "organizations",
            "targetName": "organizations"
        }
    ]
},
```

## <a name="next-steps"></a>További lépések
Gratulálunk! Az első egyéni enricher létrehozott. Most ugyanezt a mintát adhat hozzá a saját egyéni funkciókat is követheti. 

+ [Egy egyéni ismeretek hozzáadása a cognitive search folyamat](cognitive-search-custom-skill-interface.md)
+ [Hogyan képességcsoport megadása](cognitive-search-defining-skillset.md)
+ [Képességcsoport (REST) létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Hogyan hidaljuk mezők leképezése](cognitive-search-output-field-mapping.md)
