---
title: Rövid útmutató, hogyan adhat hozzá ASP.NET Core szolgáltatás jelzők |} A Microsoft Docs
description: Gyors üzembe helyezés szolgáltatás jelzők hozzáadása az ASP.NET Core-alkalmazásokhoz és kezelnie azokat az Azure-alkalmazások konfigurálása
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: ASP.NET Core
ms.workload: tbd
ms.date: 04/19/2019
ms.author: yegu
ms.openlocfilehash: f83283dbf2a78b3717ba719f21dd9249a8a09d1f
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393292"
---
# <a name="quickstart-add-feature-flags-to-an-aspnet-core-app"></a>Gyors útmutató: A szolgáltatás jelzők hozzáadása ASP.NET Core-alkalmazás

Csatlakozzon az Azure-alkalmazások konfigurálása alkalmazás engedélyezheti az ASP.NET Core-kezelés szolgáltatás. A felügyelt szolgáltatás segítségével minden funkció jelző tárolja, és központilag szabályozhatja azok állapota. Ez a rövid útmutató bemutatja, hogyan Alkalmazáskonfiguráció építhet be ASP.NET Core-webalkalmazás létrehozása a szolgáltatás felügyeleti teljes körű megvalósítását.

A .NET Core szolgáltatás kezelési kódtárakat kiterjesztheti a keretrendszer az átfogó jelző támogatásáról. Ezek a könyvtárak a .NET Core konfigurációs rendszer épülnek. Ezek zökkenőmentes integrálását a konfigurálása a .NET Core konfigurációs szolgáltatón keresztül.

Bármely Kódszerkesztő segítségével ebben a rövid útmutatóban található lépések elvégzése. [A Visual Studio Code](https://code.visualstudio.com/) kiváló lehetőség a Windows, macOS és Linux platformokon az érhető el.

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató elvégzéséhez telepítse a [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Hozzon létre egy alkalmazást a konfigurációs adattároló

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Válassza ki **funkció Manager** >  **+ létrehozás** hozzáadása a következő szolgáltatás jelzők:

    | Kulcs | Állapot |
    |---|---|
    | Beta | Ki |

## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core-webalkalmazás létrehozása

Használja a [.NET Core parancssori felület (CLI)](https://docs.microsoft.com/dotnet/core/tools/) hozhat létre egy új ASP.NET Core MVC webalkalmazás-projektet. A .NET Core parancssori felület használatával a Visual Studio helyett előnye, hogy a .NET Core parancssori felület a Windows, macOS és Linux platformon keresztül érhető el.

1. Hozzon létre egy új mappát a projekthez. Ez a rövid útmutatóhoz adja neki *TestFeatureFlags*.

1. Az új mappára futtassa a következő parancsot egy új ASP.NET Core MVC webalkalmazás-projektet létrehozása:

   ```    
   dotnet new mvc
   ```

## <a name="add-secret-manager"></a>Add Secret Manager

Adja hozzá a [Secret Manager eszköz](https://docs.microsoft.com/aspnet/core/security/app-secrets) a projekthez. A titkos kód kezelő eszköz fejlesztési munkálatok során, a projekt fa kívüli bizalmas adatait tárolja. Ez a módszer megakadályozza, hogy véletlenül megossza az alkalmazás forráskódbeli titkos kódjait.

1. Nyissa meg a *.csproj* fájlt.
1. Adjon hozzá egy `UserSecretsId` elem a következő példában látható módon, és cserélje le a saját, az értékét, amely általában egy GUID Azonosítót:

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">

    <PropertyGroup>
        <TargetFramework>netcoreapp2.1</TargetFramework>
        <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
    </PropertyGroup>

    <ItemGroup>
        <PackageReference Include="Microsoft.AspNetCore.App" />
        <PackageReference Include="Microsoft.AspNetCore.Razor.Design" Version="2.1.2" PrivateAssets="All" />
    </ItemGroup>

    </Project>
    ```

1. Mentse a fájlt.

## <a name="connect-to-an-app-configuration-store"></a>Egy alkalmazás a konfigurációs adattároló csatlakozni

1. Adja hozzá hivatkozásokat az `Microsoft.Extensions.Configuration.AzureAppConfiguration` és `Microsoft.FeatureManagement` NuGet-csomagok a következő parancsok futtatásával:

    ```
    dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration --version 1.0.0-preview-008520001

    dotnet add package Microsoft.FeatureManagement.AspNetCore --version 1.0.0-preview-008560001-910
    ```

1. Futtassa a következő parancsot a projekt-csomagok visszaállítására szolgáló:

    ```
    dotnet restore
    ```

1. Adja hozzá a titkos kulcs nevű **ConnectionStrings:AppConfig** Secret Manager.

    A titkos kód eléréséhez az alkalmazás a konfigurációs adattárolónál a kapcsolati karakterláncot tartalmazza. Cserélje le a `<your_connection_string>` értéke a következő parancsot az alkalmazás konfigurációs tárához tartozó kapcsolati karakterlánccal.

    Ezt a parancsot abban a könyvtárban kell végrehajtani, ahol a *.csproj* fájl található.

    ```
    dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>
    ```

    Használatával Secret Manager csak a webalkalmazás helyi tesztelése. Az alkalmazás üzembe helyezésekor [Azure App Service](https://azure.microsoft.com/services/app-service), például használhat egy elnevezett nastavení aplikace **kapcsolati karakterláncok** Secret Manager használatával tárolja a kapcsolati karakterlánc helyett App Service-ben.

    A titkos kód, az alkalmazás konfigurációs API-val is elérheti. Egy kettőspontot (:) a konfiguráció nevét, az összes támogatott platformon alkalmazás-konfigurációs API-val működik. Lásd: [környezet konfigurációjának](https://docs.microsoft.com/aspnet/core/fundamentals/configuration).

1. Nyissa meg *Program.cs*, és vegyen fel egy hivatkozást a .NET Core alkalmazás konfigurációszolgáltató:

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. Frissítés a `CreateWebHostBuilder` metódus meghívásával használhatja az Alkalmazáskonfigurációt az `config.AddAzureAppConfiguration()` metódust.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options => {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                           .UseFeatureFlags();
                });
            })
            .UseStartup<Startup>();
    ```

1. Nyissa meg *Startup.cs*, és adja hozzá a .NET Core-szolgáltatás kezelő hivatkozásokat:

    ```csharp
    using Microsoft.FeatureManagement.AspNetCore;
    ```

1. Frissítés a `ConfigureServices` metódus meghívásával támogatja funkció hozzáadása a `services.AddFeatureManagement()` metódust. Szükség esetén belefoglalhatja a meghívásával funkció jelzővel használandó bármely szűrőt `services.AddFeatureFilter<FilterType>()`:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement();
    }
    ```

1. Adjon hozzá egy *MyFeatureFlags.cs* fájlt:

    ```csharp
    namespace TestFeatureFlags
    {
        public enum MyFeatureFlags
        {
            Beta
        }
    }
    ```

1. Adjon hozzá *BetaController.cs* , a *tartományvezérlők* könyvtár:

    ```csharp
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.FeatureManagement.AspNetCore;

    namespace TestFeatureFlags.Controllers
    {
        public class BetaController: Controller
        {
            private readonly IFeatureManager _featureManager;

            public BetaController(IFeatureManagerSnapshot featureManager)
            {
                _featureManager = featureManager;
            }

            [Feature(MyFeatureFlags.Beta)]
            public IActionResult Index()
            {
                return View();
            }
        }
    }
    ```

1. Nyissa meg *_ViewImports.cshtml* a a *nézetek* könyvtárat, és adja hozzá a szolgáltatás manager címke segítő:

    ```html
    @addTagHelper *, Microsoft.FeatureManagement.AspNetCore
    ```

1. Nyissa meg *_Layout.cshtml* a a *nézetek*\\*megosztott* könyvtárat, és cserélje le a `<nav>` vonalkód alapján `<body>`  >  `<header>` a következő kóddal:

    ```html
    <nav class="navbar navbar-expand-sm navbar-toggleable-sm navbar-light bg-white border-bottom box-shadow mb-3">
        <div class="container">
            <a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">TestFeatureFlags</a>
            <button class="navbar-toggler" type="button" data-toggle="collapse" data-target=".navbar-collapse" aria-controls="navbarSupportedContent"
            aria-expanded="false" aria-label="Toggle navigation">
            <span class="navbar-toggler-icon"></span>
            </button>
            <div class="navbar-collapse collapse d-sm-inline-flex flex-sm-row-reverse">
                <ul class="navbar-nav flex-grow-1">
                    <li class="nav-item">
                        <a class="nav-link text-dark" asp-area="" asp-controller="Home" asp-action="Index">Home</a>
                    </li>
                    <feature name="Beta">
                    <li class="nav-item">
                        <a class="nav-link text-dark" asp-area="" asp-controller="Beta" asp-action="Index">Beta</a>
                    </li>
                    </feature>
                    <li class="nav-item">
                        <a class="nav-link text-dark" asp-area="" asp-controller="Home" asp-action="Privacy">Privacy</a>
                    </li>
                </ul>
            </div>
        </div>
    </nav>
    ```

1. Hozzon létre egy *béta* könyvtár alatt *nézetek* , és adja hozzá *Index.cshtml* hozzá:

    ```html
    @{
        ViewData["Title"] = "Beta Home Page";
    }

    <h1>
        This is the beta website.
    </h1>
    ```

## <a name="build-and-run-the-app-locally"></a>Hozhat létre, és az alkalmazás helyileg történő futtatása

1. Az alkalmazás létrehozása a .NET Core-CLI-vel, a következő parancsot az parancs-rendszerhéjba:

    ```
    dotnet build
    ```

1. A build sikeres befejezése után futtassa a következő parancsot a webalkalmazás helyi futtatásához:

    ```
    dotnet run
    ```

1. Nyisson meg egy böngészőablakot, és váltson `https://localhost:5001`, azaz a helyileg üzemeltetett webes alkalmazás alapértelmezett URL-CÍMÉT.

    ![A rövid útmutató alkalmazások indítása helyi](./media/quickstarts/aspnet-core-feature-flag-local-before.png)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza ki **összes erőforrás**, és válassza ki az alkalmazás konfigurációs tár a rövid útmutatóban létrehozott.

1. Válassza ki **funkció Manager**, és módosítsa az állapotát a **béta** kulcs **a**:

    | Kulcs | Állapot |
    |---|---|
    | Beta | Bekapcsolva |

1. Az új konfigurációs beállítások megtekintéséhez a böngészőlap frissítéséhez.

    ![A rövid útmutató alkalmazások indítása helyi](./media/quickstarts/aspnet-core-feature-flag-local-after.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy új alkalmazás-konfigurációs adattároló, és felügyeli az ASP.NET Core-webalkalmazás-n keresztül a használta a [funkció kezelési kódtárak](https://go.microsoft.com/fwlink/?linkid=2074664).

- Tudjon meg többet [felügyeleti funkció](./concept-feature-management.md).
- [A szolgáltatás jelölők kezelése](./manage-feature-flags.md).
- [A szolgáltatás jelzők használata az ASP.NET Core-alkalmazás](./use-feature-flags-dotnet-core.md).
