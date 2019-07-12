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
ms.devlang: csharp
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 78c64786f523aa424e8a9816e42db70e2a2997c2
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798455"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>Oktatóanyag: A dinamikus konfiguráció használata az ASP.NET Core-alkalmazás

ASP.NET Core, amely képes olvasni a konfigurációs adatok különböző forrásokból származó moduláris konfigurációs rendszerrel rendelkezik. Működés közbeni változások anélkül, hogy ez az alkalmazás újraindítása tud kezelni. ASP.NET Core támogatja a kötés .NET erősen típusos osztály konfigurációs beállításokat. Ez kódtárba őket elhelyezni a kódban a különböző használatával `IOptions<T>` mintákat. Ezek egyikét mintáit, kifejezetten `IOptionsSnapshot<T>`, automatikusan újra betölti az alkalmazást, az alapul szolgáló adatok változásakor. Is beszúrása `IOptionsSnapshot<T>` be tartományvezérlői eléréséhez a legfrissebb konfigurálási tárolva az Azure-alkalmazások konfigurálása az alkalmazásban.

Is állíthat be az alkalmazás konfigurációs ASP.NET Core-ügyfélkódtár frissíteni a konfigurációs beállítások segítségével dinamikusan egy közbenső készletét. Mindaddig, amíg a webalkalmazás küldött fogadási kérelmekre küld továbbra is fennáll, a konfigurációs beállítások továbbra is a konfigurációs adattárolónál a várakozott.

Annak érdekében, hogy a beállítások frissítve, és ezáltal a konfigurációs tár túl sok hívás, gyorsítótár minden egyes beállítás szolgál. Mindaddig, amíg a gyorsítótárazott értéket adják lejárt, a frissítési művelet nem frissíti az értéket, akkor is, ha az értéke megváltozott a konfigurációs tárban. Az alapértelmezett lejárati időt, az egyes kérések 30 másodperc, de szükség esetén felülbírálható lesz.

Ez az oktatóanyag bemutatja, hogyan valósíthat meg dinamikus konfiguráció frissítéseit a kódban. A web app, a rövid útmutatók rendszerben bevezetett épül. A folytatás előtt Befejezés [ASP.NET Core-alkalmazás létrehozása az alkalmazás konfigurációs](./quickstart-aspnet-core-app.md) első.

Bármely Kódszerkesztő segítségével hajtsa végre a lépéseket ebben az oktatóanyagban. [A Visual Studio Code](https://code.visualstudio.com/) egy kiváló lehetőség, hogy az a Windows, macOS és Linux platformokon érhető el.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Állítsa be az alkalmazás frissíti a konfigurációját egy alkalmazás a konfigurációs adattároló változásainak adott válaszként.
> * A legfrissebb konfigurációt, az alkalmazás vezérlők helyezhet el.

## <a name="prerequisites"></a>Előfeltételek

Ehhez az oktatóanyaghoz, telepítse a [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>Töltse be újra az alkalmazás konfigurációs adatait

1. Nyissa meg *Program.cs*, és frissítse a `CreateWebHostBuilder` metódus hozzáadása a `config.AddAzureAppConfiguration()` metódust.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                           .ConfigureRefresh(refresh =>
                           {
                               refresh.Register("TestApp:Settings:BackgroundColor")
                                      .Register("TestApp:Settings:FontColor")
                                      .Register("TestApp:Settings:Message")
                           });
                }
            })
            .UseStartup<Startup>();
    ```

    A `ConfigureRefresh` a beállításait, frissítse a konfigurációs adatokat az alkalmazás a konfigurációs adattárolónál a frissítési művelet akkor aktiválódik, amikor segítségével módszert. Annak érdekében, hogy ténylegesen vált a frissítési műveletet, a frissítés közbenső kell konfigurálni az alkalmazás a konfigurációs adatok frissítése, ha bármilyen változás történik.

2. Adjon hozzá egy *kinyerhetők* határozza meg, és implementál egy új fájlt `Settings` osztály.

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

3. Nyissa meg *Startup.cs*, és frissítse a `ConfigureServices` metódus kötést létrehozni a konfigurációs adatokat a `Settings` osztály.

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

4. Frissítés a `Configure` adható hozzá, hogy a konfigurációs beállításokat egy közbenső frissítéshez frissíteni kell az ASP.NET Core-webalkalmazás küldött fogadási kérelmekre küld továbbra is regisztrálva.

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseAzureAppConfiguration();
        app.UseMvc();
    }
    ```
    
    A megadott frissítés a konfigurációt használja, a közbenső szoftver az `AddAzureAppConfiguration` metódus az `Program.cs` az ASP.NET Core webes alkalmazás által fogadott összes kérelemhez frissítésének aktiválásához. Az egyes kérések egy frissítési művelet akkor aktiválódik, és az ügyféloldali kódtár ellenőrzi, hogy ha a regisztrált a konfigurációs beállításokat a gyorsítótárazott érték lejárt. A gyorsítótárban lévő értékek a lejárt a beállítások értékeit a rendszer frissíti az alkalmazás a konfigurációs adattároló, és a megmaradó értékek nem változnak.
    
    > [!NOTE]
    > Egy konfigurációs beállítás alapértelmezett gyorsítótár lejárati idő pedig 30 másodperc, de meghívásával bírálható felül a `SetCacheExpiration` argumentumaként átadott a beállítások inicializáló metódust a `ConfigureRefresh` metódust.

## <a name="use-the-latest-configuration-data"></a>A legfrissebb konfigurációs adatok használata

1. Nyissa meg *HomeController.cs* a tartományvezérlők könyvtárban, és vegyen fel egy hivatkozást a `Microsoft.Extensions.Options` csomagot.

    ```csharp
    using Microsoft.Extensions.Options;
    ```

2. Frissítés a `HomeController` fogadásához osztály `Settings` keresztül függőségi beszúrást, és ellenőrizze, használja az értékeket.

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

3. Nyissa meg *Index.cshtml* a nézetek > directory otthoni, és cserélje le annak tartalmát a következő parancsfájlt:

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

4. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza ki **összes erőforrás**, és válassza ki a konfigurációs tár-példányt, amely a rövid útmutatóban létrehozott.

5. Válassza ki **konfigurációs Explorer**, és frissítse az értékeket a következő kulcsokat:

    | Kulcs | Value |
    |---|---|
    | TestAppSettings:BackgroundColor | zöld |
    | TestAppSettings:FontColor | lightGray |
    | TestAppSettings:Message | Élő frissítés-alkalmazás konfigurálása Azure - adatokat! |

6. Az új konfigurációs beállítások megtekintéséhez a böngészőlap frissítéséhez. A böngészőlap frissítése több is megkövetelhető a módosítások érvénybe lépnek.

    ![A rövid útmutató app frissítése helyi](./media/quickstarts/aspnet-core-app-launch-local-after.png)
    
    > [!NOTE]
    > A konfigurációs beállítások lettek gyorsítótárazva, egy alapértelmezett lejárati idő pedig 30 másodperc, mivel az alkalmazás a konfigurációs adattárolónál a beállítások módosításai volna csak megjelennek a web app Ha a gyorsítótár érvényessége lejárt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy Azure hozzáadott felügyeltszolgáltatás-identitás leegyszerűsítheti az Alkalmazáskonfigurációt hozzá, és az alkalmazás hitelesítőadat-kezelés javítására. Ismerje meg, hogyan használhatja az Alkalmazáskonfigurációt, folytassa az Azure CLI-minták.

> [!div class="nextstepaction"]
> [CLI-minták](./cli-samples.md)
