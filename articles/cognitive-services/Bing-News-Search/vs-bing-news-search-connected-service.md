---
title: 'Oktatóanyag: Bing News Search, C#'
titleSuffix: Azure Cognitive Services
description: Kapcsolódás a Bing News Searchhöz egy ASP.NET Core-webalkalmazással.
services: cognitive-services
author: ghogen
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: tutorial
ms.date: 03/01/2018
ms.author: ghogen
ms.openlocfilehash: f1f5c590216975ce6b0813da6d9d98279d591454
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48804344"
---
# <a name="tutorial-connect-to-bing-news-search-api-by-using-connected-services-in-visual-studio"></a>Oktatóanyag: Kapcsolódás a Bing News Search API-hoz a Connected Services segítségével a Visual Studióban

A Bing News Search használatával lehetővé teheti, hogy alkalmazásai és szolgáltatásai egy reklámmentes keresőmotorral keressenek a weben. A Bing News Search a Cognitive Servicesben elérhető egyik keresési szolgáltatás.

A cikk részletes információkat szolgáltat a Visual Studio Bing News Searchhöz elérhető Connected Services szolgáltatásának használatáról. Ez a funkció a Visual Studio 2017 szoftver 15.7-es vagy újabb verziójában érhető el, ha a Cognitive Services bővítmény telepítve van.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes fiókkal](https://azure.microsoft.com/pricing/free-trial/).
- A Visual Studio 2017 15.7-es verziója telepített webfejlesztési tevékenységprofillal. [Letöltés](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-bing-news-search-api"></a>Projekt kiegészítése a Bing News Search API támogatásával

1. Hozzon létre egy új ASP.NET Core-webprojektet MyWebApplication néven. Használja a **Web Application (Model-View-Controller)** (Webalkalmazás) projektsablont az összes alapértelmezett beállítással. Fontos, hogy a projektnek a MyWebApplication nevet adja, hogy a névtér egyezzen, amikor kódot másol a projektbe. 

1. A **Megoldáskezelőben** válassza az **Add** (Hozzáadás) > **Connected Service** (Csatlakoztatott szolgáltatás) lehetőséget.
   Megjelenik a Connected Service (Csatlakoztatott szolgáltatás) lap a projekthez adható szolgáltatásokkal.

   ![Az Add Connected Service (Csatlakoztatott szolgáltatás hozzáadása) menüelem képernyőképe](../media/vs-common/Connected-Service-Menu.PNG)

1. Az elérhető szolgáltatásokat tartalmazó listában válassza a **Bring Intelligent Search To Your Apps** (Intelligens keresés megvalósítása az alkalmazásokban) lehetőséget.

   ![Csatlakoztatott szolgáltatások listájának képernyőképe](./media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-0.PNG)

   Ha bejelentkezett a Visual Studióba, és rendelkezik a fiókjához társított Azure-előfizetéssel, egy lap jelenik meg, amely az előfizetéseit tartalmazza egy legördülő listában. Válassza ki a használni kívánt előfizetést, majd válasszon egy nevet a Bing News Search API-nak. Az **Edit** (Szerkesztés) elem kiválasztásával módosíthatja az automatikusan létrehozott nevet.

   ![Az előfizetés és név mezők képernyőképe](media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-1.PNG)

1. Válassza ki az erőforráscsoportot, valamint a tarifacsomagot.

   ![Az erőforráscsoport és a tarifacsomag mezők képernyőképe](media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-2.PNG)

   A tarifacsomagokkal kapcsolatos további információért válassza a **Review pricing** (Díjszabás áttekintése) lehetőséget.

1. Az **Add** (Hozzáadás) lehetőség kiválasztásával adja hozzá a csatlakoztatott szolgáltatás támogatását.
   A Visual Studio módosítja a projektet, és hozzáadja a NuGet-csomagokat, a konfigurációs fájlbejegyzéseket, és a Bing News Search API-kapcsolat támogatása érdekében végrehajtott egyéb módosításokat. A kimenet a projektben történt események naplóját mutatja. Az alábbihoz hasonló kimenet jelenik meg:

   ```output
   [5/4/2018 12:41:21.084 PM] Adding Intelligent Search to the project.
   [5/4/2018 12:41:21.271 PM] Creating new Intelligent Search...
   [5/4/2018 12:41:24.128 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.ImageSearch' version 1.2.0...
   [5/4/2018 12:41:24.135 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.NewsSearch' version 1.2.0...
   [5/4/2018 12:41:24.154 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.WebSearch' version 1.2.0...
   [5/4/2018 12:41:24.168 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.CustomSearch' version 1.2.0...
   [5/4/2018 12:41:24.187 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.VideoSearch' version 1.2.0...
   [5/4/2018 12:42:07.287 PM] Retrieving keys...
   [5/4/2018 12:42:07.741 PM] Updating appsettings.json setting: 'ServiceKey' = 'c271412f3e4c4e1dacc7c4145fa0572a'
   [5/4/2018 12:42:07.745 PM] Updating appsettings.json setting: 'ServiceEndPoint' = 'https://api.cognitive.microsoft.com/bing/v7.0'
   [5/4/2018 12:42:07.749 PM] Updating appsettings.json setting: 'Name' = 'WebApplicationCore-Search_IntelligentSearch'
   [5/4/2018 12:42:10.217 PM] Successfully added Intelligent Search to the project.
   ```

   Az appsettings.json fájl most az alábbi új beállításokat tartalmazza:

   ```json
   "CognitiveServices": {
     "IntelligentSearch": {
       "ServiceKey": "<your service key>",
       "ServiceEndPoint": "https://api.cognitive.microsoft.com/bing/v7.0",
       "Name": "WebApplicationCore-Search_IntelligentSearch"
     }
   }
   ```
 
## <a name="use-the-bing-news-search-api-to-add-search-functionality-to-a-web-page"></a>Keresési funkciók hozzáadása weboldalakhoz a Bing News Search API használatával

Most hogy hozzáadta a Bing News Search API támogatását a projekthez, az alábbiak szerint adhatja hozzá az intelligens keresést a weboldalaihoz az API használatával.

1.  A *Startup.cs* fájlban a `ConfigureServices` metódusban adja hozzá az `IServiceCollection.AddSingleton` meghívását. Ez elérhetővé teszi a kulcsbeállításokat tartalmazó konfigurációs objektumot a projektben a kód számára.
 
   ```csharp
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddMvc();
            services.AddSingleton<IConfiguration>(Configuration);
        }
   ```


1. Adjon hozzá egy új osztályfájlt a **Models** mappában *BingNewsModel.cs* néven. Ha más nevet adott a projektnek, használja a saját projektje névterét a MyWebApplication helyett. Cserélje le a tartalmat a következő kódra:
 
    ```csharp
    using Microsoft.Azure.CognitiveServices.Search.NewsSearch.Models;
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    
    namespace MyWebApplication.Models
    {
        public class BingNewsModel
        {
            public News SearchResult { get; set; } 
            public string SearchText { get; set; }
        }
    }
    ```

   Ez a modell tárolja a Bing News Search-szolgáltatáshívások eredményeit.
 
1. A **Controllers** mappában adjon hozzá egy új osztályfájlt *IntelligentSearchController.cs* néven. Cserélje le a tartalmat a következő kódra:

   ```csharp
    using System.Net.Http;
    using System.Threading.Tasks;
    using MyWebApplication.Models;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Azure.CognitiveServices.Search.NewsSearch;
    using Microsoft.Extensions.Configuration;
    
    namespace MyWebApplication.Controllers
    {
        // A controller to handle News Search requests
        public class IntelligentSearchController : Controller
        {
            private IConfiguration configuration;
  
            // Set up the configuration that contains the keys
            // (from the appsettings.json file)
            // that you will use to access the service  
            public IntelligentSearchController(IConfiguration configuration)
            {
                this.configuration = configuration;
            }

            // Call the Bing News Search API and put the result in the model object.    
            public async Task<IActionResult> BingSearchResult(BingNewsModel model)
            {
                if (!string.IsNullOrWhiteSpace(model.SearchText))
                { 
                    INewsSearchAPI client = this.GetNewsSearchClient(new MyHandler());
                    model.SearchResult = await client.News.SearchAsync(model.SearchText);
                }
                return View(model);
            }
    
            // Forward requests to the Search endpoint to the BingSearchResult method
            [HttpPost("Search")]
            public IActionResult Search(BingNewsModel model)
            {
                return RedirectToAction("BingSearchResult", model);
            }

            // Get the search client object
            private INewsSearchAPI GetNewsSearchClient(DelegatingHandler handler)
            {
                string key =
                   configuration.GetSection("CognitiveServices")["IntelligentSearch:ServiceKey"];
    
                INewsSearchAPI client = new NewsSearchAPI(
                   new ApiKeyServiceClientCredentials(key), handlers: handler);
    
                return client;
            }
        }
    }
   ```

   Ebben a kódban a konstruktor állítja be a kulcsokat tartalmazó konfigurációs objektumot. A `Search` útvonal metódusa egy egyszerű átirányítás a `BingSearchResult` függvényre. Ez meghívja a `GetNewsSearchClient` metódust a `NewsSearchAPI` ügyfélobjektum lekérése érdekében.  A `NewsSearchAPI` ügyfélobjektum tartalmazza a `SearchAsync` metódust, amely ténylegesen meghívja a szolgáltatást, és visszaadja az eredményeket az imént létrehozott `SearchResult` modellben. 

1. Adja hozzá az `MyHandler` osztályt, amelyre az előző kód hivatkozott. Ez a keresési szolgáltatás felé indított hívásokat a szolgáltatás `DelegatingHandler` alaposztályára delegálja.

   ```csharp
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Threading;

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

1. A keresések beküldésének és az eredmények megtekintésének kiszolgálására hozzon létre egy új mappát **IntelligentSearch** néven a **Views** mappában. Ebben a mappában adjon hozzá egy nézetet *BingSearchResult.cshtml* néven. Másolja be a következő kódot:

    ```cshtml
    @using System
    @model MyWebApplication.Models.BingNewsModel
    
    @{
        ViewData["Title"] = "BingSearchResult";
    }
    
    <h2>Search News</h2>
    
    <div class="row">
        <section>
            <form asp-controller="IntelligentSearch" asp-action="Search" method="POST"
                  class="form-horizontal" enctype="multipart/form-data">
                <table width ="90%">
                    <tr>
                        <td>
                            <input type="text" name="SearchText" class="form-control" />
                        </td>
                        <td>
                            <button type="submit" class="btn btn-default">Search</button>
                        </td>
                    </tr>
                </table>
            </form>
        </section>
    </div>
    <h2>Search Result</h2=
    <table>
    @if (!string.IsNullOrEmpty(Model.SearchText)) {
        foreach (var item in Model.SearchResult.Value) {
        <tr>
            <td rowspan="2" width="90">
               <img src=@item?.Image?.Thumbnail?.ContentUrl width="80" height="80" />
            </td>
            <td><a href=@item.Url>@item.Name</a></td>
        </tr>   
        <tr>
            <td>@item.Description</td>
        </tr>
        <tr height="10">
            <td/><td/>
         </tr>
        } }
     </table>
    <div>
        <hr />
        <p>
            <a asp-controller="Home" asp-action="Index">Return to Index</a>
        </p>
    </div>
    ```

1. Indítsa el a webalkalmazást helyben, adja meg az imént létrehozott oldal URL-címét (/IntelligentSearch/BingSearchResult), és küldjön el egy keresési kérést a Search (Keresés) gomb használatával.

   ![A Bing News Search találatainak képernyőképe](media/vs-bing-news-search-connected-service/Cog-News-Search-Results.PNG)
           
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölheti az erőforráscsoportot. Ezzel törli a Cognitive Services-szolgáltatást és a kapcsolódó erőforrásokat is. Az erőforráscsoport törlése a Portalon keresztül:

1. Írja be az erőforráscsoport nevét a Portal tetején található keresőmezőbe. Válassza ki a törölni kívánt erőforráscsoportot.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Az **Írja be az erőforráscsoport nevét** mezőbe írja be az erőforráscsoport nevét, és válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>További lépések

További információ a Bing News Search API-ról: [Mi a Bing News Search?](index.yml).
