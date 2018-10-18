---
title: 'Oktatóanyag: A Text Analytics használata C#-pal'
titleSuffix: Azure Cognitive Services
description: Text Analytics kapcsolat az ASP.NET Core-webalkalmazásból.
services: cognitive-services
author: ghogen
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: tutorial
ms.date: 09/12/2018
ms.author: ghogen
ms.openlocfilehash: 95de174087fb2a73ad6c75a2e96caf872c351854
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45605232"
---
# <a name="tutorial-connect-to-the-text-analytics-cognitive-service-by-using-connected-services-in-visual-studio"></a>Oktatóanyag: Text Analytics Cognitive Service kapcsolat a Visual Studio csatlakoztatott szolgáltatásai segítségével

A Text Analytics Service használatával részletes információkat nyerhet ki képekből a vizuális adatok kategorizálásához és feldolgozásához, a gépi támogatású képmoderálás segít őrködni a szolgáltatások felett.

Ez a cikk és a kísérő cikkek részletesen ismertetik, hogyan lehet a Visual Studio csatlakoztatott szolgáltatási funkcióját a Text Analytics Service esetében felhasználni. A funkció a Visual Studio 2017 15.7 és későbbi változatokban is elérhető, ha a Cognitive Services bővítmény telepítve van.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes fiókkal](https://azure.microsoft.com/pricing/free-trial/).
- Visual Studio 2017 15.7 verzió telepítése webfejlesztési tevékenységprofillal. [Töltse le most](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-the-text-analytics-service"></a>Támogatás hozzáadása a projekthez a Text Analytics Service számára

1. Hozza létre a TextAnalyticsDemo nevű új ASP.NET Core web projektet. Használja a webalkalmazás (Model-View-Controller) projektsablont az összes alapértelmezett beállítással. Fontos, hogy a projektnek a MyWebApplication nevet adja, hogy a névtér egyezzen, amikor a kódot a projektbe másolja.  A példában ez a cikk az MVC-t használja, de a Text Analytics csatlakoztatott szolgáltatás bármely ASP.NET-projekttípussal használható.

1. A **Megoldáskezelőben** kattintson duplán a **csatlakoztatott szolgáltatás** elemen.
   Megjelenik a projekthez adható szolgáltatásokat mutató Csatlakoztatott szolgáltatás oldal.

   ![Képernyőkép a csatlakoztatott szolgáltatásról a Megoldáskezelőben](../media/vs-common/Connected-Services-Solution-Explorer.PNG)

1. Az elérhető szolgáltatások menüjében válassza a **Hangulat kiértékelés a Text Analytics segítségével** lehetőséget.

   ![A csatlakoztatott szolgáltatások képernyőjének képe](./media/vs-text-connected-service/Cog-Text-Connected-Service-0.PNG)

   Ha már bejelentkezett a Visual Stúdióba, és rendelkezik a fiókhoz társított Azure-előfizetéssel, akkor megjelenik egy oldal az előfizetéseit tartalmazó legördülő listával.

   ![A Text Analytics csatlakoztatott szolgáltatás képernyőjének képe](media/vs-text-connected-service/Cog-Text-Connected-Service-1.PNG)

1. Válassza ki a használni kívánt előfizetést majd válasszon nevet a Text Analytics Service számára, vagy a **Szerkesztés** hivatkozást választva módosíthatja az automatikusan létrehozott nevet, válassza ki az erőforráscsoportot és a Tarifacsomagot.

   ![Az erőforráscsoport és a tarifacsomag mezők képernyőképe](media/vs-text-connected-service/Cog-Text-Connected-Service-2.PNG)

   A tarifacsomagokkal kapcsolatban kövesse a hivatkozást.

1. Válassza a **Hozzáadás** gombot a csatlakoztatott szolgáltatáshoz támogatás hozzáadásához.
   A Visual Studio módosítja a projektet a NuGet-csomagok, a konfigurációs fájl bejegyzések és egyéb a Text Analytics Service projekthez adásához szükséges módosítások hozzáadásával. A **kimeneti ablak** naplózza, hogy mi történik a projekttel. Az alábbihoz hasonlót kell látnia:

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
 
## <a name="use-the-text-analytics-service-to-detect-the-language-for-a-text-sample"></a>A Text Analytics Service szolgáltatás használatával detektálja egy mintaszöveg nyelvét.

1. Adja hozzá a következőket utasításokkal a Startup.cs fájlba.
 
   ```csharp
   using System.IO;
   using System.Net.Http;
   using System.Net.Http.Headers;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   ```
 
1. A programbeli konfiguráció engedélyezéséhez adjon hozzá egy konfigurációs mezőt és adjon a Startup osztályhoz a konfigurációs mezőt inicializáló konstruktort.

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. Adjon hozzá egy DemoTextAnalyzeController nevű osztály fájlt a tartományvezérlők mappába, és cserélje le annak tartalmát az alábbira:

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
    
    A kód tartalmazza a GetTextAnalyzeClient-et az ügyfél objektum lekérésére, amelyet a Text Analytics API meghívására használhat, és egy kérés kezelőt, ami egy adott szöveggel meghívja a DetectLanguage eljárást.

1. Adja hozzá a MyHandler segédosztályt, amelyet a fenti kóddal használunk.

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

1. A Models mappába vegyen fel egy osztályt a modellnek.

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

1. Adjon hozzá egy nézetet az elemzett szöveg, a meghatározott nyelv, és az elemzés megbízhatóságát jelző pontszám megtekintéséhez. Ehhez kattintson a jobb gombbal a **Nézetek** mappán, válassza a **Hozzáadás**, majd a **Nézet** lehetőséget. A megjelenő párbeszédpanelen adjon meg egy _TextAnalyzeResult_ nevet, fogadja el az alapértelmezéseket egy új, _TextAnalyzeResult.cshtml_ nevű fájlnak a hozzáadásához a **Nézetek** mappába és másolja be az alábbi tartalmat a fájlba:
    
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
 
1. Fordítsa és futtassa a példát helyileg. Írjon be valamilyen szöveget, és nézze meg milyen nyelvnek ismeri fel a Text Analytics.
   
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot. Ezzel törli a kognitív szolgáltatást és a kapcsolódó erőforrásokat. Az erőforráscsoport törlése a Portalon keresztül:

1. Írja be az erőforráscsoport nevét a Portal tetején található keresőmezőbe. Amikor az eredmények listájában megjelenik az ebben az oktatóanyagban használt erőforráscsoport, jelölje ki.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Az **ÍRJA BE AZ ERŐFORRÁSCSOPORT NEVÉT:** mezőbe írja be az erőforráscsoport nevét, és válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>További lépések

További információ a Text Analytics szolgáltatásról: [Text Analytics szolgáltatás dokumentációja](index.yml).
