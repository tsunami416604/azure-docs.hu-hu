---
title: Útmutató a szolgáltatás-jelzők hozzáadásához a ASP.NET Corehoz | Microsoft Docs
description: Útmutató a funkció-jelzők hozzáadásához az alkalmazások ASP.NET Coreéhez és az Azure-alkalmazások konfigurációjában való kezeléséhez
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
ms.openlocfilehash: 41947de18ae27b41d046fd4358f0039a1b56374d
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68347908"
---
# <a name="quickstart-add-feature-flags-to-an-aspnet-core-app"></a>Gyors útmutató: Szolgáltatás-jelzők hozzáadása egy ASP.NET Core alkalmazáshoz

Ebben a rövid útmutatóban beépíti az Azure-alkalmazások konfigurációját egy ASP.NET Core webalkalmazásba, amely a szolgáltatások felügyeletének teljes körű megvalósítását hozza létre. Az alkalmazás konfigurációs szolgáltatásával központilag tárolhatja az összes funkció jelzőjét, és szabályozhatja az állapotukat. 

A .NET Core-szolgáltatások felügyeleti kódtárai kiterjesztik a keretrendszert átfogó funkció-jelölő támogatással. Ezek a kódtárak a .NET Core konfigurációs rendszerre épülnek. Zökkenőmentesen integrálva vannak az alkalmazások konfigurációjával a .NET Core konfigurációs szolgáltatón keresztül.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
- [.NET Core SDK](https://dotnet.microsoft.com/download).

## <a name="create-an-app-configuration-store"></a>Alkalmazás-konfigurációs tároló létrehozása

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Válassza a **szolgáltatás-kezelő** >  **+ Létrehozás** lehetőséget a következő funkció-jelzők hozzáadásához:

    | Kulcs | Állapot |
    |---|---|
    | Bétaverzió | Ki |

## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core-webalkalmazás létrehozása

A [.net Core parancssori felület (CLI)](https://docs.microsoft.com/dotnet/core/tools/) használatával hozzon létre egy új ASP.net Core MVC webalkalmazás-projektet. A Visual Studio helyett a a .NET Core parancssori felülete használatának előnye, hogy a a .NET Core parancssori felülete a Windows, macOS és Linux platformokon érhető el.

1. Hozzon létre egy új mappát a projekthez. Ebben a rövid útmutatóban nevezze el *TestFeatureFlags*.

1. Az új mappában futtassa az alábbi parancsot egy új ASP.NET Core MVC webalkalmazás-projekt létrehozásához:

   ```    
   dotnet new mvc
   ```

## <a name="add-secret-manager"></a>Secret Manager hozzáadása

Adja hozzá a [Secret Manager eszközt](https://docs.microsoft.com/aspnet/core/security/app-secrets) a projekthez. A Secret Manager eszköz bizalmas adatokat tárol a projekt fáján kívüli fejlesztési munkához. Ez a módszer megakadályozza, hogy véletlenül megossza az alkalmazás forráskódbeli titkos kódjait.

1. Nyissa meg a *. csproj* fájlt.
1. Adjon hozzá `UserSecretsId` egy elemet az alábbi példában látható módon, és cserélje le az értékét a saját értékére, amely általában egy GUID:

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

## <a name="connect-to-an-app-configuration-store"></a>Kapcsolódás alkalmazás-konfigurációs tárolóhoz

1. Adja hozzá a `Microsoft.Azure.AppConfiguration.AspNetCore` NuGet-csomagra mutató hivatkozást a következő parancs futtatásával:

    ```
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore --version 2.0.0-preview-009200001-7
    ```

1. Futtassa a következő parancsot a projekt csomagjainak visszaállításához:

    ```
    dotnet restore
    ```

1. Adjon hozzá egy **ConnectionStrings: AppConfig** nevű titkos kulcsot a Secret Managerhez.

    Ez a titok tartalmazza a kapcsolati karakterláncot az alkalmazás konfigurációs tárolójának eléréséhez. Cserélje le a következő parancs értékétazalkalmazáskonfigurációstárolójánakakapcsolatokkarakterláncára.`<your_connection_string>`

    Ezt a parancsot abban a könyvtárban kell végrehajtani, ahol a *.csproj* fájl található.

    ```
    dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>
    ```

    A Secret Manager csak a webalkalmazás helyi tesztelésére használható. Amikor az alkalmazást Azure App Servicera telepíti [](https://azure.microsoft.com/services/app-service), például a kapcsolódási karakterláncok nevű alkalmazás-beállítást  használja app Service ahelyett, hogy a Secret Managert használja a kapcsolódási karakterlánc tárolásához.

    Ezt a titkot az alkalmazás konfigurációs API-jával érheti el. Egy kettőspont (:) a konfiguráció neve az App Configuration API-val minden támogatott platformon használható. Lásd: [konfiguráció környezet alapján](https://docs.microsoft.com/aspnet/core/fundamentals/configuration).

1. Nyissa meg a *program.cs*, és adjon hozzá egy hivatkozást a .net Core alkalmazás-konfigurációs szolgáltatóhoz:

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. Frissítse a `CreateWebHostBuilder` metódust az alkalmazás konfigurációjának használatára a `config.AddAzureAppConfiguration()` metódus meghívásával.

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

1. Nyissa meg a *Startup.cs*, és adjon hozzá hivatkozásokat a .net Core Feature Managerhez:

    ```csharp
    using Microsoft.FeatureManagement;
    ```

1. Frissítse a `ConfigureServices` metódust a szolgáltatás jelölő támogatásának hozzáadásához `services.AddFeatureManagement()` a metódus meghívásával. Megadhatja, hogy a szolgáltatás jelzőjén használható szűrőket a következő hívásával `services.AddFeatureFilter<FilterType>()`is felhasználhatja:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement();
    }
    ```

1. Frissítse a `Configure` metódust úgy, hogy olyan köztes értéket adjon hozzá, amely lehetővé teszi, hogy a szolgáltatás jelölő értékei ismétlődő időközönként frissüljenek, miközben a ASP.net Core webalkalmazás továbbra is fogadja a kéréseket.

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseAzureAppConfiguration();
        app.UseMvc();
    }
    ```

1. *MyFeatureFlags.cs* -fájl hozzáadása:

    ```csharp
    namespace TestFeatureFlags
    {
        public enum MyFeatureFlags
        {
            Beta
        }
    }
    ```

1. Adja hozzá a *BetaController.cs* a *vezérlők* könyvtárba:

    ```csharp
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.FeatureManagement;
    using Microsoft.FeatureManagement.Mvc;

    namespace TestFeatureFlags.Controllers
    {
        public class BetaController: Controller
        {
            private readonly IFeatureManager _featureManager;

            public BetaController(IFeatureManagerSnapshot featureManager)
            {
                _featureManager = featureManager;
            }

            [FeatureGate(MyFeatureFlags.Beta)]
            public IActionResult Index()
            {
                return View();
            }
        }
    }
    ```

1. Nyissa meg a *_ViewImports. cshtml* mappát a views ( *nézetek* ) könyvtárban, és adja hozzá a Feature Manager tag helpert:

    ```html
    @addTagHelper *, Microsoft.FeatureManagement.AspNetCore
    ```

1. Nyissa meg a *_Layout. cshtml* \\ `<nav>`  `<body>` mappátanézetekmegosztottkönyvtárában,éscseréljelea >  vonalkódot a következő kódra: `<header>`

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

1. Hozzon létre egy *Beta* könyvtárat a *nézetek* területen, és adja hozzá az *index. cshtml* :

    ```html
    @{
        ViewData["Title"] = "Beta Home Page";
    }

    <h1>
        This is the beta website.
    </h1>
    ```

## <a name="build-and-run-the-app-locally"></a>Az alkalmazás helyi létrehozása és futtatása

1. Ha az alkalmazást a a .NET Core parancssori felülete használatával szeretné felépíteni, futtassa a következő parancsot a parancs-rendszerhéjban:

    ```
    dotnet build
    ```

1. A létrehozás sikeres befejezése után futtassa a következő parancsot a webalkalmazás helyi futtatásához:

    ```
    dotnet run
    ```

1. Nyisson meg egy böngészőablakot, és `https://localhost:5001`nyissa meg a következőt:, amely a helyileg üzemeltetett webalkalmazás alapértelmezett URL-címe.

    ![Gyorsindítás alkalmazás elindítása helyi](./media/quickstarts/aspnet-core-feature-flag-local-before.png)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **minden erőforrás**lehetőséget, majd válassza ki a gyors útmutatóban létrehozott app Configuration Store-példányt.

1. Válassza ki a **Feature Manager**elemet, és módosítsa a **bétaverzió** állapotát a **következőre:**

    | Kulcs | Állapot |
    |---|---|
    | Bétaverzió | Bekapcsolva |

1. A böngésző oldalának frissítésével tekintheti meg az új konfigurációs beállításokat.

    ![Gyorsindítás alkalmazás elindítása helyi](./media/quickstarts/aspnet-core-feature-flag-local-after.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy új alkalmazás-konfigurációs tárolót, és felhasználta egy ASP.NET Core webalkalmazás funkcióinak kezelésére a [szolgáltatás-felügyeleti kódtárak](https://go.microsoft.com/fwlink/?linkid=2074664)segítségével.

- További információ a [szolgáltatások kezeléséről](./concept-feature-management.md).
- [Szolgáltatás-jelzők kezelése](./manage-feature-flags.md).
- [A szolgáltatás-jelzők használata egy ASP.net Core alkalmazásban](./use-feature-flags-dotnet-core.md).
