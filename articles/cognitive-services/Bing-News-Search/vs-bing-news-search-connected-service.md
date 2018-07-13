---
title: A Bing News Search C# oktatóanyag |} A Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Cognitive Services Bing News Search csatlakozhat egy ASP.NET Core-webalkalmazás.
services: cognitive-services
author: ghogen
manager: douge
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: conceptual
ms.date: 03/01/2018
ms.author: ghogen
ms.openlocfilehash: 5cfa82067d28b05f32bd87e0e83d55a03da8d508
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38665219"
---
# <a name="connect-to-bing-news-search-api-by-using-connected-services-in-visual-studio"></a>Csatlakozás a Visual Studio csatlakoztatott szolgáltatásai segítségével Bing News Search API

A Bing News Search használatával engedélyezheti, hogy alkalmazásai és szolgáltatásai egy reklámmentes keresőmotort hatóköre a webes hatékonyságát. Bing – Hírkeresés a Cognitive Services szolgáltatással elérhető keresési szolgáltatások egyike.

Ez a cikk részletes adatokat biztosít a Visual Studio csatlakoztatott szolgáltatása a Bing News Search szolgáltatással. A Visual Studio 2017 15.7 vagy újabb verziójú, a Cognitive Services-bővítmény telepítve van az a funkció érhető el.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes fiókkal](https://azure.microsoft.com/pricing/free-trial/).
- A Visual Studio 2017 15.7, futtassa a következőt a webalkalmazás-fejlesztési számítási feladattal. [Töltse le most](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-bing-news-search-api"></a>Támogatás hozzáadása a projekthez a Bing News Search API

1. Hozzon létre egy új ASP.NET Core web projektet MyWebApplication nevű. Használja a **webalkalmazás (Model-View-Controller)** webesprojekt-sablon, az alapértelmezett beállítások használatával. Fontos adja a projektnek MyWebApplication, így a névtér megegyezik, amikor a kód másolását a projektbe. 

1. A **Megoldáskezelőben**, válassza a **Hozzáadás** > **csatlakoztatott szolgáltatás**.
   A csatlakoztatott szolgáltatás oldalon jelenik meg, a projekthez is hozzáadhat szolgáltatásokkal.

   ![Képernyőkép a csatlakoztatott szolgáltatás hozzáadása menüpont](../media/vs-common/Connected-Service-Menu.PNG)

1. Elérhető szolgáltatások menüjében válassza **használata intelligens keresés a saját alkalmazások**.

   ![Képernyőkép a csatlakoztatott szolgáltatások listája](./media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-0.PNG)

   Ha már bejelentkezett a Visual Studióban, és a fiókhoz társított Azure-előfizetéssel rendelkezik, megjelenik egy oldal, és a egy legördülő lista az összes előfizetés. Válassza ki a használni kívánt előfizetést, és válassza a Bing News Search API nevét. Azt is beállíthatja **szerkesztése** módosíthatja az automatikusan létrehozott nevet.

   ![Képernyőkép az előfizetés és a név mező](media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-1.PNG)

1. Válassza ki az erőforráscsoportot és a tarifacsomagot.

   ![Képernyőkép az erőforráscsoportot és az árképzési szint mezők](media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-2.PNG)

   Ha a tarifacsomagok további részleteit, jelölje be **tekintse át a díjszabási**.

1. Válasszon **Hozzáadás** a csatlakoztatott szolgáltatás támogatása érdekében.
   A Visual Studio módosítja a NuGet-csomagok, konfigurációs bejegyzéseket és egyéb módosításokat a Bing News Search API-kapcsolat hozzáadása a projekthez. A kimenet mutatja a naplót, hogy mi történik a projekthez. A következőhöz hasonló kell megjelennie:

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

   Az appsettings.json fájl most már tartalmazza a következő új beállítások:

   ```json
   "CognitiveServices": {
     "IntelligentSearch": {
       "ServiceKey": "<your service key>",
       "ServiceEndPoint": "https://api.cognitive.microsoft.com/bing/v7.0",
       "Name": "WebApplicationCore-Search_IntelligentSearch"
     }
   }
   ```
 
## <a name="use-the-bing-news-search-api-to-add-search-functionality-to-a-web-page"></a>A webes keresési funkciók adhatók hozzá a Bing News Search API használatával

Most, hogy a hozzáadott a Bing News Search API támogatása a projekthez, Íme az API használatával intelligens keresési funkciókat adhat hozzá egy weblap.

1.  A *Startup.cs*, a a `ConfigureServices` metódus hívása hozzáadása `IServiceCollection.AddSingleton`. Ez lehetővé teszi a konfigurációs objektumot, amely elérhető a kódba a projekt fő beállításait tartalmazza.
 
   ```csharp
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddMvc();
            services.AddSingleton<IConfiguration>(Configuration);
        }
   ```


1. Adjon hozzá egy új osztályfájlt alatt a **modellek** nevű mappát *BingNewsModel.cs*. Ha más néven a projekt, használja a saját project-névteret, MyWebApplication helyett. Cserélje ki annak tartalmát az alábbira:
 
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

   Ez a modell a Bing News Search szolgáltatás hívása eredményeinek tárolására szolgál.
 
1. Az a **tartományvezérlők** mappában, adjon hozzá egy új osztályfájlt nevű *IntelligentSearchController.cs*. Cserélje ki annak tartalmát az alábbira:

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

   Ebben a kódban a konstruktor beállítja a konfigurációs objektumot, amely tartalmazza a kulcsokat. A módszer a `Search` útvonal, átirányítás csak a `BingSearchResult` függvény. Meghívja a `GetNewsSearchClient` metódus lekéréséhez a `NewsSearchAPI` ügyfélobjektummal.  A `NewsSearchAPI` objektumot tartalmaz az `SearchAsync` metódussal, amely a tényleges meghívja a szolgáltatást, és visszaadja az eredményeket a `SearchResult` imént létrehozott modellt. 

1. Adjon hozzá egy osztályt `MyHandler`, amely a fenti kód hivatkozik. Ez annak alaposztályát, a keresési szolgáltatás aszinkron hívás delegálja `DelegatingHandler`.

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

1. Keresések elküldése, és az eredmények megtekintéséről támogatásának hozzáadása a **nézetek** mappában hozzon létre egy új nevű mappát **IntelligentSearch**. Az új mappába, a nézet hozzáadása *BingSearchResult.cshtml*. Másolja az alábbi kódot:

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

1. A webalkalmazás helyi indítása, adja meg az URL-címet az oldal imént létrehozott (/ IntelligentSearch/BingSearchResult), és keresési kérelem küldése a Keresés gomb használatával.

   ![Képernyőkép a Bing News Search eredmények](media/vs-bing-news-search-connected-service/Cog-News-Search-Results.PNG)
           
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az erőforráscsoport már nincs rá szükség, törölheti azt. Ezzel törli a cognitive Services-szolgáltatás és a kapcsolódó erőforrásokat. Az erőforráscsoport törlése a Portalon keresztül:

1. Adja meg az erőforráscsoport nevét, a portál tetején található keresőmezőbe. Válassza ki a törölni kívánt erőforráscsoportot.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Az a **írja be az erőforráscsoport nevének** mezőbe írja be az erőforráscsoport nevét, és válassza ki **törlése**.

## <a name="next-steps"></a>További lépések

A Bing News Search API kapcsolatos további információkért lásd: [Bing News Search újdonságai?](index.yml).
