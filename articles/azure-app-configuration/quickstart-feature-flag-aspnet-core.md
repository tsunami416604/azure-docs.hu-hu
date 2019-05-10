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
ms.openlocfilehash: 95f702b1d85dc8fe22b1800df3f7b0ebc987bee5
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2019
ms.locfileid: "65412384"
---
# <a name="quickstart-add-feature-flags-to-an-aspnet-core-app"></a>Gyors útmutató: A szolgáltatás jelzők hozzáadása ASP.NET Core-alkalmazás

ASP.NET Core-kezelés szolgáltatás az Azure-alkalmazások konfigurálása alkalmazás csatlakozással engedélyezhető. A felügyelt szolgáltatás segítségével minden funkció jelző tárolja, és központilag szabályozhatja azok állapota. Ez a rövid útmutató bemutatja, hogyan belefoglalhatja a szolgáltatást az ASP.NET Core-webalkalmazás létrehozása a szolgáltatás felügyeleti teljes körű megvalósítását.

A .NET Core szolgáltatás kezelési kódtárakat kiterjesztheti a keretrendszer az átfogó jelző támogatásáról. A .NET Core konfigurációs rendszer épülnek. Ezek zökkenőmentes integrálását a konfigurálása a .NET Core konfigurációs szolgáltatón keresztül.

Bármely Kódszerkesztő segítségével ebben a rövid útmutatóban található lépések elvégzése. [A Visual Studio Code](https://code.visualstudio.com/) kiváló lehetőség a Windows, macOS és Linux platformokon az érhető el.

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató elvégzéséhez telepítse a [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Hozzon létre egy alkalmazást a konfigurációs adattároló

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Válassza ki **funkció Manager** > **+ létrehozás** hozzáadása a következő szolgáltatás jelzők:

    | Kulcs | Állapot |
    |---|---|
    | Bétaverzió | Ki |

## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core-webalkalmazás létrehozása

Használja a [.NET Core parancssori felület (CLI)](https://docs.microsoft.com/dotnet/core/tools/) hozhat létre egy új ASP.NET Core MVC webalkalmazás-projektet. A .NET Core parancssori felület a Visual Studio-val előnye, hogy legyen elérhető a Windows, macOS és Linux rendszerek között.

1. Hozzon létre egy új mappát a projekthez. Ez a rövid útmutatóhoz adja neki *TestFeatureFlags*.

2. Az új mappára futtassa a következő parancsot egy új ASP.NET Core MVC webalkalmazás-projektet létrehozása:

        dotnet new mvc

## <a name="add-secret-manager"></a>Add Secret Manager

Adja hozzá a [Secret Manager eszköz](https://docs.microsoft.com/aspnet/core/security/app-secrets) a projekthez. A Secret Manager eszköz tárolja a projektfán kívüli fejlesztési feladatokhoz tartozó bizalmas adatokat. Ez a módszer megakadályozza, hogy véletlenül megossza az alkalmazás forráskódbeli titkos kódjait.

- Nyissa meg a *.csproj* fájlt. Adjon hozzá egy `UserSecretsId` elem itt látható módon, és cserélje le a saját, az értékét, amely általában egy GUID Azonosítót. Mentse a fájlt.

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

## <a name="connect-to-an-app-configuration-store"></a>Egy alkalmazás a konfigurációs adattároló csatlakozni

1. Adja hozzá hivatkozásokat az `Microsoft.Extensions.Configuration.AzureAppConfiguration` és `Microsoft.FeatureManagement` NuGet-csomagok a következő parancsok futtatásával:

        dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration --version 1.0.0-preview-008520001

        dotnet add package Microsoft.FeatureManagement.AspNetCore --version 1.0.0-preview-008560001-910

2. Futtassa a következő parancsot a projekt-csomagok visszaállítására szolgáló:

        dotnet restore

3. Adja hozzá a titkos kulcs nevű *ConnectionStrings:AppConfig* Secret Manager.

    A titkos kód eléréséhez az alkalmazás a konfigurációs adattárolónál a kapcsolati karakterláncot tartalmazza. Az érték a következő parancsban cserélje le a kapcsolati karakterláncát az alkalmazás a konfigurációs adattároló.

    Ezt a parancsot abban a könyvtárban kell végrehajtani, ahol a *.csproj* fájl található.

        dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>

    SECRET Manager szolgál arra, hogy csak a webalkalmazás helyi tesztelése. Ha az alkalmazás telepítését a [Azure App Service](https://azure.microsoft.com/services/app-service/web), például használhat alkalmazás-beállítás **kapcsolati karakterláncok** az App Service helyett a titkos kulcs Managerrel a kapcsolati karakterlánc tárolására.

    A titkos kód API-konfigurációval érhető el. Egy kettőspontot (:) a konfiguráció nevét a konfigurációs API-t az összes támogatott platformon működik. Lásd: [környezet konfigurációjának](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0).

4. Nyissa meg *Program.cs*, és vegyen fel egy hivatkozást a .NET Core alkalmazás konfigurációszolgáltatót.

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

5. Frissítés a `CreateWebHostBuilder` metódus meghívásával használhatja az Alkalmazáskonfigurációt az `config.AddAzureAppConfiguration()` metódust.

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

6. Nyissa meg *Startup.cs*, és adja hozzá a .NET Core-szolgáltatás kezelő hivatkozásokat.

    ```csharp
    using Microsoft.FeatureManagement.AspNetCore;
    ```

7. Frissítés a `ConfigureServices` metódus meghívásával támogatja a szolgáltatás jelző hozzáadása a `services.AddFeatureManagement()` metódust, és szükség esetén belefoglalhatja a meghívásával funkció jelzővel használandó bármely szűrőt `services.AddFeatureFilter<FilterType>()`:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement();
    }
    ```

8. Adjon hozzá egy *MyFeatureFlags.cs* fájlt.

    ```csharp
    namespace TestFeatureFlags
    {
        public enum MyFeatureFlags
        {
            Beta
        }
    }
    ```

9. Adjon hozzá *BetaController.cs* tartományvezérlők könyvtárba:

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

10. Nyissa meg *_ViewImports.cshtml* a nézetek könyvtárban, és adja hozzá a szolgáltatás manager címke segítő:

    ```html
    @addTagHelper *, Microsoft.FeatureManagement.AspNetCore
    ```

11. Nyissa meg *_Layout.cshtml* a nézetek > megosztott könyvtárat, és cserélje le a a `<nav>` sáv alatt `<body>`  >  `<header>` a következő kóddal:

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

12. Hozzon létre egy nézetek béta könyvtárat, és adja hozzá *Index.cshtml* hozzá:

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

        dotnet build

2. A build sikeres befejezése után futtassa a következő parancsot a webalkalmazás helyi futtatásához:

        dotnet run

3. Nyisson meg egy böngészőablakot, és váltson `https://localhost:5001`, azaz a helyileg üzemeltetett webes alkalmazás alapértelmezett URL-CÍMÉT.

    ![A rövid útmutató alkalmazások indítása helyi](./media/quickstarts/aspnet-core-feature-flag-local-before.png)

4. Jelentkezzen be az [Azure Portalra](https://aka.ms/azconfig/portal). Válassza ki **összes erőforrás**, és válassza ki a konfigurációs tár-példányt, amely a rövid útmutatóban létrehozott.

5. Válassza ki **funkció Manager**, és módosítsa az értéket a *béta* való *a*:

    | Kulcs | Állapot |
    |---|---|
    | Bétaverzió | Be |

6. Az új konfigurációs beállítások megtekintéséhez a böngészőlap frissítéséhez.

    ![A rövid útmutató alkalmazások indítása helyi](./media/quickstarts/aspnet-core-feature-flag-local-after.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy új alkalmazás-konfigurációs adattároló, és felügyeli az ASP.NET Core-webalkalmazás-n keresztül a használta a [funkció kezelési kódtárak](https://go.microsoft.com/fwlink/?linkid=2074664).

* Tudjon meg többet [felügyeleti funkció](./concept-feature-management.md)
* [Funkciójelölők kezelése](./manage-feature-flags.md)
* [A szolgáltatás jelzők használata az ASP.NET Core-alkalmazás](./use-feature-flags-dotnet-core.md)
