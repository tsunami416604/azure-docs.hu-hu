---
title: Oktatóanyag az Azure-alkalmazások konfigurálása dinamikus konfiguráció az ASP.NET Core-alkalmazás |} A Microsoft Docs
description: Ebben az oktatóanyagban elsajátíthatja, hogyan frissítse az ASP.NET Core-alkalmazások konfigurációs adatai
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: na
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: d5e1e5989f9902d9ab8dcc3e6c2b9d2a71f12df9
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58224379"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>Oktatóanyag: A dinamikus konfiguráció használata az ASP.NET Core-alkalmazás

ASP.NET Core, amely képes olvasni a konfigurációs adatok különböző forrásokból származó moduláris konfigurációs rendszerrel rendelkezik. Működés közbeni változások anélkül, hogy ez az alkalmazás újraindítása tud kezelni. ASP.NET Core támogatja a kötés .NET erősen típusos osztály konfigurációs beállításokat. Ez kódtárba őket elhelyezni a kódban a különböző használatával `IOptions<T>` mintákat. Ezek egyikét mintáit, kifejezetten `IOptionsSnapshot<T>`, automatikusan újra betölti az alkalmazást, az alapul szolgáló adatok változásakor. 

Is beszúrása `IOptionsSnapshot<T>` be tartományvezérlői eléréséhez a legfrissebb konfigurálási tárolva az Azure-alkalmazások konfigurálása az alkalmazásban. Is állíthat be az alkalmazás konfigurációs ASP.NET Core ügyféloldali kódtár, folyamatos figyelése és beolvasni egy alkalmazás a konfigurációs adattároló bármilyen változás. A lekérdezés rendszeres időközönként megadhat.

Ez az oktatóanyag bemutatja, hogyan valósíthat meg dinamikus konfiguráció frissítéseit a kódban. A web app, a rövid útmutatók rendszerben bevezetett épül. A folytatás előtt Befejezés [ASP.NET Core-alkalmazás létrehozása az alkalmazás konfigurációs](./quickstart-aspnet-core-app.md) első.

Bármely Kódszerkesztő segítségével ebben a rövid útmutatóban található lépések elvégzése. [A Visual Studio Code](https://code.visualstudio.com/) egy kiváló lehetőség, hogy az a Windows, macOS és Linux platformokon érhető el.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Állítsa be az alkalmazás frissíti a konfigurációját egy alkalmazás a konfigurációs adattároló változásainak adott válaszként.
> * A legfrissebb konfigurációt, az alkalmazás vezérlők helyezhet el.

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató elvégzéséhez telepítse a [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>Töltse be újra az alkalmazás konfigurációs adatait

1. Nyissa meg a Program.cs, és frissítse a `CreateWebHostBuilder` metódus hozzáadásával a `config.AddAzureAppConfiguration()` metódust.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(o => o.Connect(settings["ConnectionStrings:AppConfig"])
                    .Watch("TestApp:Settings:BackgroundColor", TimeSpan.FromSeconds(1))
                    .Watch("TestApp:Settings:FontColor", TimeSpan.FromSeconds(1))
                    .Watch("TestApp:Settings:Message", TimeSpan.FromSeconds(1)));
            })
            .UseStartup<Startup>();
    ```

    A második paramétert a `.Watch` módszer a lekérdezési időköz az ASP.NET ügyféloldali kódtár egy alkalmazás a konfigurációs adattároló kérdezi le. Az ügyféloldali kódtár ellenőrzi a meghatározott konfigurációs beállítás megtekintéséhez, ha minden olyan módosítás történt.

2. Adjon hozzá egy kinyerhetők fájlt, amely definiálja, és implementál egy új `Settings` osztály.

    ```csharp
    namespace TestAppConfig
    {
        public class Settings
        {
            public string BackgroundColor { get; set; }
            public long FontSize { get; set; }
            public string FontColor { get; set; }
            public string Message { get; set; }
        }
    }
    ```

3. Nyissa meg a Startup.cs, és frissítse a `ConfigureServices` metódus kötést létrehozni a konfigurációs adatokat a `Settings` osztály.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));

        services.Configure<CookiePolicyOptions>(options =>
        {
            options.CheckConsentNeeded = context => true;
            options.MinimumSameSitePolicy = SameSiteMode.None;
        });

        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

## <a name="use-the-latest-configuration-data"></a>A legfrissebb konfigurációs adatok használata

1. Nyissa meg a HomeController.cs vezérlők a címtárban. Frissítés a `HomeController` fogadásához osztály `Settings` keresztül függőségi beszúrást, és ellenőrizze, használja az értékeket.

    ```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        public HomeController(IOptionsSnapshot<Settings> settings)
        {
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }
    }
    ```

2. Nyissa meg a nézetek Index.cshtml > directory otthoni, és cserélje le annak tartalmát a következő parancsfájlt:

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <style>
        body {
            background-color: @ViewData["BackgroundColor"]
        }
        h1 {
            color: @ViewData["FontColor"];
            font-size: @ViewData["FontSize"];
        }
    </style>
    <head>
        <title>Index View</title>
    </head>
    <body>
        <h1>@ViewData["Message"]</h1>
    </body>
    </html>
    ```

## <a name="build-and-run-the-app-locally"></a>Hozhat létre, és az alkalmazás helyileg történő futtatása

1. Az alkalmazás létrehozása a .NET Core-CLI-vel, a következő parancsot az parancs-rendszerhéjba:

        dotnet build

2. A build sikeres befejezése után futtassa a következő parancsot a webalkalmazás helyi futtatásához:

        dotnet run

3. Nyisson meg egy böngészőablakot, és váltson `http://localhost:5000`, azaz a helyileg üzemeltetett webes alkalmazás alapértelmezett URL-CÍMÉT.

    ![A rövid útmutató alkalmazások indítása helyi](./media/quickstarts/aspnet-core-app-launch-local-before.png)

4. Jelentkezzen be az [Azure Portalra](https://aka.ms/azconfig/portal). Válassza ki **összes erőforrás**, és válassza ki a konfigurációs tár-példányt, amely a rövid útmutatóban létrehozott.

5. Válassza ki **kulcs/érték Explorer**, és frissítse az értékeket a következő kulcsokat:

    | Kulcs | Érték |
    |---|---|
    | TestAppSettings:BackgroundColor | Kék |
    | TestAppSettings:FontColor | lightGray |
    | TestAppSettings:Message | Élő frissítés-alkalmazás konfigurálása Azure - adatokat! |

6. Az új konfigurációs beállítások megtekintéséhez a böngészőlap frissítéséhez.

    ![A rövid útmutató app frissítése helyi](./media/quickstarts/aspnet-core-app-launch-local-after.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy Azure hozzáadott felügyeltszolgáltatás-identitás leegyszerűsítheti az Alkalmazáskonfigurációt hozzá, és az alkalmazás hitelesítőadat-kezelés javítására. Ismerje meg, hogyan használhatja az Alkalmazáskonfigurációt, folytassa az Azure CLI-minták.

> [!div class="nextstepaction"]
> [CLI-minták](./cli-samples.md)
