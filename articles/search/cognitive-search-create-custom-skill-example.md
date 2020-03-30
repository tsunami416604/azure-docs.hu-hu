---
title: Egyéni szakértelem példa a Bing Entity Search API használatával
titleSuffix: Azure Cognitive Search
description: Bemutatja a Bing entitáskeresési szolgáltatás használatát egy AI-dúsított indexelési folyamathoz az Azure Cognitive Search-ben egy AI-dúsított indexelési folyamathoz leképezve.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 2994c55b39d30ff16a0ca135e93a116784feb201
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113815"
---
# <a name="example-create-a-custom-skill-using-the-bing-entity-search-api"></a>Példa: Egyéni szakértelem létrehozása a Bing Entitás keresési API-jával

Ebben a példában megtudhatja, hogyan hozhat létre egy webes API egyéni szakértelem. Ez a szakértelem elfogadja a helyeket, a közéleti személyiségeket és a szervezeteket, és visszaküldi a leírásokat. A példa egy [Azure-függvényt](https://azure.microsoft.com/services/functions/) használ a [Bing Entity Search API](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) burkolócsomagolásához, hogy megvalósítsa az egyéni szakértelem-felületet.

## <a name="prerequisites"></a>Előfeltételek

+ Olvassa el [az egyéni szakértelem felület](cognitive-search-custom-skill-interface.md) cikket, ha nem ismeri a bemeneti/kimeneti felületet, amely egy egyéni szakértelem kell végrehajtania.

+ [!INCLUDE [cognitive-services-bing-entity-search-signup-requirements](../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

+ Telepítse a [Visual Studio 2019-es](https://www.visualstudio.com/vs/) vagy újabb telepítését, beleértve az Azure fejlesztési munkaterhelését is.

## <a name="create-an-azure-function"></a>Azure-függvény létrehozása

Bár ez a példa egy Azure-függvényt használ egy webes API üzemeltetéséhez, nem szükséges.  Mindaddig, amíg megfelel a [felület követelményeinek a kognitív készség,](cognitive-search-custom-skill-interface.md)a megközelítés szedése lényegtelen. Az Azure Functions azonban megkönnyíti az egyéni szakértelem létrehozását.

### <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

1. A Visual Studióban válassza a Fájl menü **Új** > **projekt** parancsát.

1. Az Új projekt párbeszédpanelen válassza a **Installed (Telepítve)** lehetőséget, a Visual C# Cloud **(Visual C#** > **Cloud)** lehetőséget, válassza az **Azure Functions**lehetőséget, írja be a projekt nevét, és válassza az OK **gombot.** A függvényalkalmazás nevének C# névtérként érvényesnek kell lennie, ezért ne használjon aláhúzásjeleket, kötőjeleket vagy más nem alfanumerikus karaktereket.

1. Válassza ki **az Azure Functions v2 (.NET Core) lehetőséget.** Ön is csinálni 1-es verzióval, de az alábbi kód alapján a v2 sablont.

1. Válassza ki a **HTTP-eseményindítóként endő típust.**

1. A tárfiók esetében a **Nincs**lehetőséget választhatja, mivel ehhez a funkcióhoz nem lesz szüksége tárhelyre.

1. Válassza az **OK gombot** a függvényprojekt és a HTTP által aktivált függvény létrehozásához.

### <a name="modify-the-code-to-call-the-bing-entity-search-service"></a>A Bing entitáskeresési szolgáltatás hívásához módosítsa a kódot

A Visual Studio létrehoz egy projektet, benne egy olyan osztállyal, amely tartalmazza a kiválasztott függvénytípus sablonkódját. A metódus *FunctionName* attribútuma adja meg a függvény nevét. A *HttpTrigger* attribútum adja meg, hogy a függvényt egy HTTP-kérelem aktiválja.

Most cserélje le a *fájl* tartalmának Function1.cs a következő kóddal:

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
    /// AI enrichment pipeline.
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

Győződjön meg arról, hogy `key` adja meg a saját *kulcsértékét* az állandó alapján a kulcsot kapott, amikor feliratkozik a Bing entitás keresési API-t.

Ez a minta tartalmazza az összes szükséges kódot egyetlen fájlban a kényelem érdekében. Ugyanezen szakértelem kissé strukturáltabb változatát [a power skills repository-ban](https://github.com/Azure-Samples/azure-search-power-skills/tree/master/Text/BingEntitySearch)találja.

Természetesen átnevezheti a fájlt a-ra. `Function1.cs` `BingEntitySearch.cs`

## <a name="test-the-function-from-visual-studio"></a>A funkció tesztelése a Visual Studio-ból

Nyomja le az **F5 billentyűt** a program futtatásához és a funkcióviselkedés teszteléséhez. Ebben az esetben az alábbi függvényt használva két entitást keresünk. Használja postás vagy hegedűs, hogy kiadja a hívást, mint az alábbi:

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
A következő példához hasonló választ kell látnia:

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

## <a name="publish-the-function-to-azure"></a>A függvény közzététele az Azure-ban

Ha elégedett a funkció viselkedésével, közzéteheti azt.

1. A **Megoldáskezelőben** kattintson a jobb gombbal a projektre, és válassza a **Publish** (Közzététel) lehetőséget. Válassza **az Új** > **közzététel**létrehozása lehetőséget.

1. Ha még nem kapcsolta össze a Visual Studio alkalmazást az Azure-fiókjával, válassza **a Fiók hozzáadása lehetőséget....**

1. Kövesse a képernyőn megjelenő utasításokat. A rendszer megkell adnia egy egyedi nevet az alkalmazásszolgáltatáshoz, az Azure-előfizetéshez, az erőforráscsoporthoz, az üzemeltetési csomaghoz és a használni kívánt tárfiókhoz. Létrehozhat egy új erőforráscsoportot, egy új üzemeltetési csomagot és egy tárfiókot, ha még nem rendelkezik ezekkel. Ha végzett, válassza a **Létrehozás gombot.**

1. A központi telepítés befejezése után figyelje meg a webhely URL-címét. Ez a függvényalkalmazás címe az Azure-ban. 

1. Az [Azure Portalon](https://portal.azure.com)keresse meg az erőforráscsoportot, és keresse meg a `EntitySearch` közzétett függvényt. A **Kezelés** szakaszban a gazdakulcsok nak kell megjelennie. Válassza az *alapértelmezett* állomáskulcs **Másolás** ikonját.  

## <a name="test-the-function-in-azure"></a>A funkció tesztelése az Azure-ban

Most, hogy rendelkezik az alapértelmezett állomáskulccsal, tesztelje a függvényt az alábbiak szerint:

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

Ebben a példában kell létrehoznia ugyanazt az eredményt, amit korábban látott, amikor a függvény futtatása a helyi környezetben.

## <a name="connect-to-your-pipeline"></a>Csatlakozás a folyamathoz
Most, hogy új egyéni szakértelemmel rendelkezik, hozzáadhatja azt a skillsethez. Az alábbi példa bemutatja, hogyan hívhatja meg a képzettséget, hogy leírásokat adjon a dokumentumokban lévő szervezetekhez (ez kiterjeszthető a helyeken és személyeken végzett munkára is). Cserélje `[your-entity-search-app-name]` le az alkalmazás nevére.

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

Itt arra számítunk, hogy a beépített [entitásfelismerési szakértelem](cognitive-search-skill-entity-recognition.md) jelen van a skillsetben, és a dokumentumot a szervezetek listájával bővítette. Referenciaként, itt van egy entitás kitermelési szakértelem konfiguráció, amely elegendő lenne a szükséges adatok létrehozásához:

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
Gratulálunk! Létrehozta az első egyéni képzettségét. Most ugyanazt a mintát követheti a saját egyéni funkciók hozzáadásához. További információért kattintson az alábbi hivatkozásokra.

+ [Power Skills: egyéni készségek tárháza](https://github.com/Azure-Samples/azure-search-power-skills)
+ [Egyéni szakértelem hozzáadása a ai-dúsítási folyamathoz](cognitive-search-custom-skill-interface.md)
+ [Hogyan definiálni a skillset](cognitive-search-defining-skillset.md)
+ [Szakértelemkészlet létrehozása (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Bővített mezők leképezése](cognitive-search-output-field-mapping.md)
