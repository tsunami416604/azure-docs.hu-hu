---
title: Text Analytics C# oktatóanyag |} A Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Szövegelemzés csatlakozhat egy ASP.NET Core-webalkalmazás.
services: cognitive-services
author: ghogen
manager: douge
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: conceptual
ms.date: 06/01/2018
ms.author: ghogen
ms.openlocfilehash: c97f75e0a41a4bf314963dc26c6424a0b773822b
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38665233"
---
# <a name="connect-to-the-text-analytics-service-by-using-connected-services-in-visual-studio"></a>Csatlakozás a Text Analytics szolgáltatással a Visual Studio csatlakoztatott szolgáltatásai segítségével

A Text Analytics szolgáltatással a vizuális adatok kategorizálásához és feldolgozásához részletes információt nyerhet ki, és hajtsa végre a gépi támogatású képmoderálás segítheti a szolgáltatások összeválogatásában.

Ez a cikk és a kiegészítő cikkek adja meg adatait a Text Analytics szolgáltatáshoz a Visual Studio csatlakoztatott szolgáltatása funkció használatát. A funkció a mindkét Visual Studio 2017 15.7 vagy későbbi, a Cognitive Services-bővítmény telepítve.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes fiókkal](https://azure.microsoft.com/pricing/free-trial/).
- A Visual Studio 2017 15.7, futtassa a következőt a webalkalmazás-fejlesztési számítási feladattal. [Töltse le most](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-the-text-analytics-service"></a>Támogatás hozzáadása a projekthez a Text Analytics szolgáltatáshoz

1. Hozzon létre egy új ASP.NET Core web projektet TextAnalyticsDemo nevű. Az összes alapértelmezett beállításokkal használhatja a webalkalmazás (Model-View-Controller) projektsablonnal. Fontos adja a projektnek MyWebApplication, így a névtér megegyezik, amikor a kód másolását a projektbe.  A példában ez a cikk az MVC-t használja, de a Text Analytics csatlakoztatott szolgáltatás használata az ASP.NET-projekt típustól.

1. A **Megoldáskezelőben**, kattintson duplán a **csatlakoztatott szolgáltatás** elemet.
   A csatlakoztatott szolgáltatás oldalon jelenik meg, a projekthez is hozzáadhat szolgáltatásokkal.

   ![Képernyőkép a csatlakoztatott szolgáltatás a Megoldáskezelőben](../media/vs-common/Connected-Services-Solution-Explorer.PNG)

1. Elérhető szolgáltatások menüjében válassza **vélemények kiértékelése, szövegelemzés**.

   ![A csatlakoztatott szolgáltatások képernyőfelvételen képernyő](./media/vs-text-connected-service/Cog-Text-Connected-Service-0.PNG)

   Ha már bejelentkezett a Visual Studióban, és a fiókhoz társított Azure-előfizetéssel rendelkezik, megjelenik egy oldal, és a egy legördülő lista az összes előfizetés.

   ![Képernyőkép a Text Analytics csatlakoztatott szolgáltatás képernyő](media/vs-text-connected-service/Cog-Text-Connected-Service-1.PNG)

1. Válassza ki az előfizetést szeretné használni, majd válasszon egy nevet a Text Analytics szolgáltatás, vagy válassza ki a **szerkesztése** módosíthatja is az automatikusan létrehozott nevet, válassza ki az erőforráscsoportot és a Tarifacsomag hivatkozásra.

   ![Képernyőkép az erőforráscsoportot és az árképzési szint mezők](media/vs-text-connected-service/Cog-Text-Connected-Service-2.PNG)

   Kövesse a hivatkozást a tarifacsomagok.

1. Válasszon **Hozzáadás** a csatlakoztatott szolgáltatás támogatása érdekében.
   A Visual Studio módosítja a NuGet-csomagok, konfigurációs bejegyzéseket és egyéb módosításokat a Text Analytics szolgáltatással való kapcsolat hozzáadása a projekthez. A **kimeneti ablak** naplózását, hogy mi történik a projekthez. A következőhöz hasonló kell megjelennie:

   ```output
    [6/1/2018 3:04:02.347 PM] Adding Text Analytics to the project.
    [6/1/2018 3:04:02.906 PM] Creating new Text Analytics...
    [6/1/2018 3:04:06.314 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Language' version 1.0.0-preview...
    [6/1/2018 3:04:56.759 PM] Retrieving keys...
    [6/1/2018 3:04:57.822 PM] Updating appsettings.json setting: 'ServiceKey' = '<service key>'
    [6/1/2018 3:04:57.827 PM] Updating appsettings.json setting: 'ServiceEndPoint' = 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.0'
    [6/1/2018 3:04:57.832 PM] Updating appsettings.json setting: 'Name' = 'TextAnalyticsDemo'
    [6/1/2018 3:05:01.840 PM] Successfully added Text Analytics to the project.
    ```
 
## <a name="use-the-text-analytics-service-to-detect-the-language-for-a-text-sample"></a>A Text Analytics szolgáltatás használatával észlelje a nyelvet, szöveges minta.

1. Adja hozzá a következő using utasításokat a Startup.cs fájlban.
 
   ```csharp
   using System.IO;
   using System.Net.Http;
   using System.Net.Http.Headers;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   ```
 
1. Adjon hozzá egy konfigurációs mező, és adja hozzá a konstruktort, amely a konfigurációs mező konfigurációs ahhoz, hogy a program az indítási osztályban inicializálja.

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. Adjon hozzá egy osztály fájlt a tartományvezérlők DemoTextAnalyzeController nevű mappában, és cserélje le annak tartalmát az alábbira:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Net.Http;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
    using Microsoft.Extensions.Configuration;
    using TextAnalyticsDemo.Models;
    
    namespace TextAnalyticsDemo.Controllers
    {
        public class DemoTextAnalyzeController : Controller
        {
            private IConfiguration configuration;
    
            public DemoTextAnalyzeController(IConfiguration configuration)
            {
                this.configuration = configuration;
            }
    
            public IActionResult TextAnalyzeResult(TextAnalyzeModel model)
            {
    
                if (!string.IsNullOrWhiteSpace(model.TextStr))
                {
                    ITextAnalyticsAPI client = this.GetTextAnalyzeClient(new MyHandler());
                    model.AnalyzeResult = client.DetectLanguage(
                        new BatchInput(
                            new List<Input>()
                            {
                                new Input("id",model.TextStr)
                            }));
                }
                return View(model);
            }
    
            [HttpPost("Analyze")]
            public IActionResult Analyze(TextAnalyzeModel model)
            {
                return RedirectToAction("TextAnalyzeResult", model);
            }
    
            // Using the ServiceKey from the configuration file,
            // get an instance of the Text Analytics client.
            private ITextAnalyticsAPI GetTextAnalyzeClient(DelegatingHandler handler)
            {
                string key = configuration.GetSection("CognitiveServices")["TextAnalytics:ServiceKey"];
    
                ITextAnalyticsAPI client = new TextAnalyticsAPI( handlers: handler);
                client.SubscriptionKey = key;
                client.AzureRegion = AzureRegions.Westus;
    
                return client;
            }
        }
    }
    ```
    
    A kód GetTextAnalyzeClient megszerezni az objektum, amelyet használhat a szövegelemzési API meghívására, és a egy kérelem-kezelő, amely meghívja a DetectLanguage a egy adott szöveg tartalmazza.

1. Adja hozzá a MyHandler segítő osztályt, amelyet a fenti kóddal.

    ```csharp
        class MyHandler : DelegatingHandler
        {
            protected async override Task<HttpResponseMessage> SendAsync(
            HttpRequestMessage request, CancellationToken cancellationToken)
            {
                // Call the inner handler.
                var response = await base.SendAsync(request, cancellationToken);
                
                return response;
            }
        }
    ```

1. A Models mappát adja hozzá a modell osztályát.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
    
    namespace Demo.Models
    {
        public class TextAnalyzeModel
        {
            public string TextStr { get; set; }
    
            public LanguageBatchResult AnalyzeResult { get; set; }
    
            public SentimentBatchResult AnalyzeResult2 { get; set; }
        }
    }
    ```

1. Adja hozzá a nézetet, amely megjeleníti az elemzett szöveg, határozza meg a nyelvet és a pontszám, amely a megbízhatósági szint az elemzés jelöli.
    
    ```cshtml
    @using System
    @model Demo.Models.TextAnalyzeModel
    
    @{
        ViewData["Title"] = "TextAnalyzeResult";
    }
    
    <h2>Text Language</h2>
    
    <div class="row">
        <section>
            <form asp-controller="DemoTextAnalyze" asp-action="Analyze" method="POST"
                  class="form-horizontal" enctype="multipart/form-data">
                <table width="90%">
                    <tr>
                        <td>
                            <input type="text" name="TextStr" class="form-control" />
                        </td>
                        <td>
                            <button type="submit" class="btn btn-default">Analyze</button>
                        </td>
                    </tr>
                </table>
            </form>
        </section>
    </div>
    
    <h2>Result</h2>
    <div>
        <dl class="dl-horizontal">
            <dt>
                Text :
            </dt>
            <dd>
                @Html.DisplayFor(model => model.TextStr)
            </dd>
            <dt>
                Language Name :
            </dt>
            <dd>
                @Html.DisplayFor(model => model.AnalyzeResult.Documents[0].DetectedLanguages[0].Name)
            </dd>
            <dt>
                Score :
            </dt>
            <dd>
                @Html.DisplayFor(model => model.AnalyzeResult.Documents[0].DetectedLanguages[0].Score)
            </dd>
        </dl>
    </div>
    <div>
        <hr />
        <p>
            <a asp-controller="Home" asp-action="Index">Return to Index</a>
        </p>
    </div>
    
    ```
 
1. Hozhat létre, és a példa helyileg történő futtatása. Írjon be szöveget, és tekintse meg a Szövegelemzés felismeri a nyelvet.
   
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot. Ezzel törli a cognitive Services-szolgáltatás és a kapcsolódó erőforrásokat. Az erőforráscsoport törlése a Portalon keresztül:

1. Írja be az erőforráscsoport nevét a Portal tetején található keresőmezőbe. Amikor megjelenik a keresési eredmények ebben az oktatóanyagban használt erőforráscsoport, jelölje ki azt.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Az **ÍRJA BE AZ ERŐFORRÁSCSOPORT NEVÉT:** mezőbe írja be az erőforráscsoport nevét, és válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>További lépések

További információ a Text Analytics szolgáltatás olvassa el a [Text Analytics szolgáltatás dokumentációja](index.yml).
