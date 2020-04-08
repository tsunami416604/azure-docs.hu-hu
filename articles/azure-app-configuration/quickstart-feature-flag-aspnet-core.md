---
title: Rövid útmutató a szolgáltatásjelzők ASP.NET Core-hoz való hozzáadásához
description: Funkciójelzők hozzáadása ASP.NET Core alkalmazásokhoz, és kezelésük az Azure App Konfigurációja használatával
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 01/14/2020
ms.author: lcozzens
ms.openlocfilehash: b3579d12981e2b0add916a280bac7b4f9392d8ba
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80803143"
---
# <a name="quickstart-add-feature-flags-to-an-aspnet-core-app"></a>Rövid útmutató: Szolgáltatásjelzők hozzáadása egy ASP.NET Core alkalmazáshoz

Ebben a rövid útmutatóban a szolgáltatáskezelés teljes körű megvalósítását hozza létre egy ASP.NET Core alkalmazásban az Azure App Configuration használatával. Az Alkalmazáskonfigurációs szolgáltatás segítségével központilag tárolja az összes funkciójelzőt, és szabályozhatja azok állapotát. 

A . Ezek a könyvtárak a .NET Core konfigurációs rendszerre épülnek. Zökkenőmentesen integrálhatók az alkalmazáskonfigurációval a .NET Core konfigurációszolgáltatón keresztül.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
- [.NET Core SDK](https://dotnet.microsoft.com/download).

## <a name="create-an-app-configuration-store"></a>Alkalmazáskonfigurációs tároló létrehozása

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. A **Szolgáltatáskezelő** > **+Hozzáadás** lehetőséget `Beta`választva hozzáadhat egy szolgáltatásjelzőt.

    > [!div class="mx-imgBorder"]
    > ![Béta nevű szolgáltatásjelző engedélyezése](media/add-beta-feature-flag.png)

    Egyelőre hagyja `label` meg határozatlanul. Az új szolgáltatásjelző mentéséhez válassza az **Alkalmaz** lehetőséget.

## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core-webalkalmazás létrehozása

A [.NET Core parancssori felület (CLI)](https://docs.microsoft.com/dotnet/core/tools/) segítségével hozzon létre egy új ASP.NET Core MVC webalkalmazás-projektet. A .NET Core CLI használatának előnye a Visual Studio helyett, hogy a .NET Core CLI elérhető a Windows, a macOS és a Linux platformokon.

1. Hozzon létre egy új mappát a projekthez. Ehhez a rövid útmutatóhoz nevezze el *TestFeatureFlags*.

1. Az új mappában futtassa a következő parancsot egy új ASP.NET Core MVC webalkalmazás-projekt létrehozásához:

   ```    
   dotnet new mvc --no-https
   ```

## <a name="add-secret-manager"></a>Titkos kezelő hozzáadása

A Secret Manager használatához `UserSecretsId` vegyen fel egy elemet a *.csproj* fájlba.

1. Nyissa meg a *.csproj* fájlt.

1.  Adjon `UserSecretsId` hozzá egy elemet az itt látható módon. Használhatja ugyanazt a GUID azonosítót, vagy lecserélheti ezt az értéket a sajátjára.

    > [!IMPORTANT]
    > `CreateHostBuilder`a `CreateWebHostBuilder` .NET Core 3.0-ban.  Válassza ki a megfelelő szintaxist a környezet alapján.

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

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

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">

        <PropertyGroup>
            <TargetFramework>netcoreapp3.1</TargetFramework>
            <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
        </PropertyGroup>

    </Project>
    ```
    ---

1. Mentse a *.csproj* fájlt.

A Secret Manager eszköz tárolja a projektfán kívüli fejlesztési feladatokhoz tartozó bizalmas adatokat. Ez a módszer megakadályozza, hogy véletlenül megossza az alkalmazás forráskódbeli titkos kódjait.

> [!TIP]
> Ha többet szeretne megtudni a Secret Managerről, olvassa el [az alkalmazástitkok biztonságos tárolása a fejlesztés során ASP.NET Core alkalmazásban.](https://docs.microsoft.com/aspnet/core/security/app-secrets)

## <a name="connect-to-an-app-configuration-store"></a>Csatlakozás alkalmazáskonfigurációs tárolóhoz

1. A következő `Microsoft.Azure.AppConfiguration.AspNetCore` parancsok `Microsoft.FeatureManagement.AspNetCore` futtatásával hivatkozzon a és a NuGet csomagokra:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    dotnet add package Microsoft.FeatureManagement.AspNetCore
    ```

1. A következő parancs futtatásával állítsa vissza a projekt csomagjait:

    ```dotnetcli
    dotnet restore
    ```

1. Adjon hozzá egy **ConnectionStrings:AppConfig** nevű titkos kulcsot a Titkos kezelőhöz.

    Ez a titkos adata tartalmazza az alkalmazáskonfigurációs tároló eléréséhez a kapcsolati karakterláncot. Cserélje `<your_connection_string>` le a következő parancs ban lévő értéket az alkalmazáskonfigurációs tároló kapcsolati karakterláncára. A kapcsolati karakterlánc az **Access Keys** az Azure Portalon található.

    Ezt a parancsot abban a könyvtárban kell végrehajtani, ahol a *.csproj* fájl található.

    ```dotnetcli
    dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>
    ```

    A Secret Manager csak a webalkalmazás helyi teszteléséhez használható. Amikor például telepíti az alkalmazást az [Azure App Service szolgáltatásba,](https://azure.microsoft.com/services/app-service)az Alkalmazásszolgáltatásokban egy **kapcsolati karakterláncok** nevű alkalmazásbeállítást használ ahelyett, hogy a Secret Managert használná a kapcsolati karakterlánc tárolásához.

    Ezt a titkos kulcsot az alkalmazás konfigurációs API-jával érheti el. A kettőspont (:) A konfigurációs névvel működik az alkalmazáskonfigurációs API-val az összes támogatott platformon. Lásd: [Környezet szerint beállítás.](https://docs.microsoft.com/aspnet/core/fundamentals/configuration)

1. A *Program.cs*frissítse `CreateWebHostBuilder` a metódust az `config.AddAzureAppConfiguration()` alkalmazáskonfiguráció használatához a metódus hívásával.

    > [!IMPORTANT]
    > `CreateHostBuilder`a `CreateWebHostBuilder` .NET Core 3.0-ban.  Válassza ki a megfelelő szintaxist a környezet alapján.

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

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

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(options => {
                options.Connect(settings["ConnectionStrings:AppConfig"])
                    .UseFeatureFlags();
            });
        })
        .UseStartup<Startup>());
    ```
    ---

1. Nyissa *meg a Startup.cs,* és adjon hivatkozásokat a .NET Core szolgáltatáskezelőhöz:

    ```csharp
    using Microsoft.FeatureManagement;
    ```

1. Frissítse `ConfigureServices` a metódust, hogy a `services.AddFeatureManagement()` metódus hívásával hozzáadja a szolgáltatásjelző-támogatást. A funkciójelzőkkel használható szűrőket a következő hívással is megadhatja: `services.AddFeatureFilter<FilterType>()`

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)
    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_2);        
        services.AddFeatureManagement();
    }
    ```
    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)
    ```csharp    
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllersWithViews();
        services.AddFeatureManagement();
    }

    ---

1. Update the `Configure` method to add a middleware to allow the feature flag values to be refreshed at a recurring interval while the ASP.NET Core web app continues to receive requests.

    #### [.NET Core 2.x](#tab/core2x)
    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
            }

            app.UseStaticFiles();
            app.UseCookiePolicy();
            app.UseAzureAppConfiguration();
            app.UseMvc(routes =>
            {
                routes.MapRoute(
                    name: "default",
                    template: "{controller=Home}/{action=Index}/{id?}");
            });
    }
    ```
    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)
    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
            }
            app.UseStaticFiles();
            app.UseRouting();
            app.UseAuthorization();
            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllerRoute(
                    name: "default",
                    pattern: "{controller=Home}/{action=Index}/{id?}");
            });
            app.UseAzureAppConfiguration();
    }
    ```
    ---

1. Adjon hozzá *egy MyFeatureFlags.cs* fájlt:

    ```csharp
    namespace TestFeatureFlags
    {
        public enum MyFeatureFlags
        {
            Beta
        }
    }
    ```

1. Adjon hozzá *BetaController.cs* a *Vezérlők* könyvtárhoz:

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

1. Nyissa *meg a _ViewImports.cshtml* fájlt a *Nézetek* könyvtárban, és adja hozzá a szolgáltatáskezelő címkesegédet:

    ```html
    @addTagHelper *, Microsoft.FeatureManagement.AspNetCore
    ```

1. Nyissa *meg a _Layout.cshtml* fájlt a `<nav>` *Megosztott* `<body>`  >  `<header>` *nézetek*\\könyvtárban, és cserélje le a vonalkódot a következő kódra:

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

1. Hozzon létre egy *bétakönyvtárat* a *Nézetek* csoportban, és adja hozzá az *Index.cshtml fájlt:*

    ```html
    @{
        ViewData["Title"] = "Beta Home Page";
    }

    <h1>
        This is the beta website.
    </h1>
    ```

## <a name="build-and-run-the-app-locally"></a>Az alkalmazás helyi létrehozása és futtatása

1. Ha az alkalmazást a .NET Core CLI segítségével szeretné felépíteni, futtassa a következő parancsot a parancshéjban:

    ```
    dotnet build
    ```

1. A sikeres build befejezése után futtassa a következő parancsot a webalkalmazás helyi futtatásához:

    ```
    dotnet run
    ```

1. Nyisson meg egy böngészőablakot, és nyissa meg `https://localhost:5000`a , a helyileg üzemeltetett webalkalmazás alapértelmezett URL-címét.
    Ha az Azure Cloud Shellben dolgozik, válassza a *Webes előnézet* gombot, majd a *Configure*lehetőséget.  Amikor a rendszer kéri, válassza az 5000-es portot.

    ![A Webes előnézet gomb megkeresése](./media/quickstarts/cloud-shell-web-preview.png)

    A böngészőnek az alábbi képhez hasonló oldalt kell megjelenítenie.
    ![A gyorsindítási alkalmazás helyi indítása](./media/quickstarts/aspnet-core-feature-flag-local-before.png)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza az **Összes erőforrás**lehetőséget, és válassza ki a rövid útmutatóban létrehozott Alkalmazáskonfigurációs tároló példányt.

1. Válassza a **Szolgáltatáskezelő**lehetőséget, és módosítsa a **Béta** kulcs állapotát **Be**állapotra.

1. Térjen vissza a parancssorba, és szakítsa meg a futási `dotnet` folyamatot a gomb megnyomásával. `Ctrl-C`  Indítsa újra `dotnet run`az alkalmazást a használatával.

1. Frissítse a böngészőlapot az új konfigurációs beállítások megtekintéséhez.

    ![A gyorsindítási alkalmazás helyi indítása](./media/quickstarts/aspnet-core-feature-flag-local-after.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy új App Configuration Store-t, és arra használta, hogy kezelje a ASP.NET Core webalkalmazás szolgáltatásait a [Szolgáltatáskezelési kódtárakon](https://go.microsoft.com/fwlink/?linkid=2074664)keresztül.

- További információ a [szolgáltatáskezelésről.](./concept-feature-management.md)
- [A szolgáltatásjelzők kezelése](./manage-feature-flags.md).
- [A szolgáltatásjelzők használata ASP.NET Core alkalmazásban.](./use-feature-flags-dotnet-core.md)
- [Dinamikus konfiguráció használata ASP.NET Core alkalmazásban](./enable-dynamic-configuration-aspnet-core.md)
