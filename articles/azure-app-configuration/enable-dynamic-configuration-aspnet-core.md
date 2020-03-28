---
title: 'Oktatóanyag: Az alkalmazáskonfiguráció dinamikus konfigurációjának használata ASP.NET Core-ban'
titleSuffix: Azure App Configuration
description: Ebben az oktatóanyagban megtudhatja, hogyan frissítheti dinamikusan ASP.NET Core alkalmazások konfigurációs adatait
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: e9df6d2e7a8219d16e7b60f7c3b8d826a87e6110
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348854"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>Oktatóanyag: Dinamikus konfiguráció használata ASP.NET Core alkalmazásban

ASP.NET Core egy dugaszolható konfigurációs rendszer, amely képes olvasni konfigurációs adatokat a különböző forrásokból. Képes dinamikusan kezelni a módosításokat anélkül, hogy egy alkalmazás újraindítását okozna. ASP.NET Core támogatja a konfigurációs beállítások erősen beírt .NET osztályokhoz való kötését. Befecskendezi őket a kódba `IOptions<T>` a különböző minták használatával. Az egyik ilyen minták, különösen, `IOptionsSnapshot<T>`automatikusan újratölti az alkalmazás konfigurációját, amikor az alapul szolgáló adatok megváltoznak. Az alkalmazás `IOptionsSnapshot<T>` vezérlőibe beadhatja az Azure App Configuration szolgáltatásában tárolt legújabb konfigurációeléréséhez.

Az Alkalmazáskonfiguráció ASP.NET Core ügyfélkódtár beállítható úgy is, hogy a konfigurációs beállítások egy részét dinamikusan frissítse egy köztes szoftver használatával. A konfigurációs beállítások minden alkalommal frissülnek a konfigurációs tárolóval, amíg a webalkalmazás kéréseket kap.

Az alkalmazáskonfiguráció automatikusan gyorsítótárazza az egyes beállításokat, hogy elkerülje a konfigurációs tárolóba irányuló túl sok hívást. A frissítési művelet megvárja, amíg egy beállítás gyorsítótárazott értéke lejár a beállítás frissítéséhez, még akkor is, ha az értéke megváltozik a konfigurációs tárolóban. A gyorsítótár lejárati ideje alapértelmezetten 30 másodperc. Szükség esetén felülbírálhatja ezt a lejárati időt.

Ez az oktatóanyag bemutatja, hogyan valósíthatja meg a dinamikus konfigurációs frissítéseket a kódban. A rövid útmutatókban bevezetett webalkalmazásra épül. Mielőtt folytatna, először fejezze be [a ASP.NET Core alkalmazás létrehozása az alkalmazás konfigurációjával.](./quickstart-aspnet-core-app.md)

Bármelyik kódszerkesztőt használhatja az oktatóanyag lépéseihez. [A Visual Studio Code](https://code.visualstudio.com/) kiváló lehetőség, amely windowsos, macOS és Linux platformokon érhető el.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Állítsa be az alkalmazást, hogy frissítse a konfigurációját az alkalmazáskonfigurációs tárolóban végrehajtott változásokra válaszul.
> * Adja meg a legújabb konfigurációt az alkalmazás vezérlőiben.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag hoz a [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Mielőtt folytatna, először fejezze be [a ASP.NET Core alkalmazás létrehozása az alkalmazás konfigurációjával.](./quickstart-aspnet-core-app.md)

## <a name="add-a-sentinel-key"></a>Jelzőkulcs hozzáadása

A *jelzőkulcs* egy speciális kulcs, amely jelzi, ha a konfiguráció megváltozott. Az alkalmazás figyeli a sentinel kulcsot a módosításokhoz. A rendszer a rendszer a módosítás észlelésekor frissíti az összes konfigurációs értéket. Ez a megközelítés csökkenti az alkalmazás által az alkalmazás konfigurációja, összehasonlítva a változások figyelése az összes kulcs figyelése.

1. Az Azure Portalon válassza a **Configuration Explorer > Create > Key-value**lehetőséget.

1. A **Kulcs**mezőbe írja be a *TestApp:Settings:Sentinel*parancsot. Az **Érték mezőbe**írja be az 1 értéket. Hagyja üresen **a Címke** és a **Tartalom típust.**

1. Kattintson az **Alkalmaz** gombra.

## <a name="reload-data-from-app-configuration"></a>Adatok újratöltése az alkalmazáskonfigurációból

1. Adjon hozzá hivatkozást a `Microsoft.Azure.AppConfiguration.AspNetCore` NuGet csomaghoz a következő parancs futtatásával:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

1. Nyissa *meg a Program.cs,* és frissítse a `CreateWebHostBuilder` metódust a `config.AddAzureAppConfiguration()` metódus hozzáadásához.

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

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
                                    refresh.Register("TestApp:Settings:Sentinel", refreshAll: true)
                                           .SetCacheExpiration(new TimeSpan(0, 5, 0));
                                });
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
                    config.AddAzureAppConfiguration(options =>
                    {
                        options.Connect(settings["ConnectionStrings:AppConfig"])
                               .ConfigureRefresh(refresh =>
                                    {
                                        refresh.Register("TestApp:Settings:Sentinel", refreshAll: true)
                                               .SetCacheExpiration(new TimeSpan(0, 5, 0));
                                    });
                    });
                })
            .UseStartup<Startup>());
    ```
    ---

    A `ConfigureRefresh` módszer segítségével adja meg a konfigurációs adatok frissítéséhez használt beállításokat az Alkalmazás konfigurációs tárolójával, amikor egy frissítési művelet aktiválódik. A `refreshAll` `Register` metódus paramétere azt jelzi, hogy minden konfigurációs értéket frissíteni kell, ha a jelzőkulcs megváltozik.

    Emellett a `SetCacheExpiration` metódus felülbírálja a gyorsítótár alapértelmezett lejárati idejét 30 másodperc, és 5 perces időt ad meg. Ez csökkenti az alkalmazáskonfigurációra irányuló kérelmek számát.

    > [!NOTE]
    > Tesztelési célokra érdemes lehet csökkenteni a gyorsítótár lejárati idejét.

    A frissítési művelet tényleges elindításához be kell állítania egy frissítési köztes szoftvert az alkalmazás számára a konfigurációs adatok frissítéséhez, ha bármilyen változás történik. Majd meglátjuk, hogyan kell ezt egy későbbi lépésben.

2. Adjon hozzá egy *Settings.cs* fájlt, amely `Settings` új osztályt határoz meg és valósít meg.

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

3. Nyissa meg a `IServiceCollection.Configure<T>` *Startup.cs,* és használja a `Settings` `ConfigureServices` metódusban a konfigurációs adatokat az osztályhoz.

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddControllersWithViews();
    }
    ```
    ---

4. Frissítse `Configure` a módszert, `UseAzureAppConfiguration` és adja hozzá a köztes szoftvert, hogy a frissítéshez regisztrált konfigurációs beállítások at frissíthessék, miközben a ASP.NET Core webalkalmazás továbbra is fogadja a kéréseket.


    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseAzureAppConfiguration();

        services.Configure<CookiePolicyOptions>(options =>
        {
            options.CheckConsentNeeded = context => true;
            options.MinimumSameSitePolicy = SameSiteMode.None;
        });

        app.UseMvc();
    }
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
                // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
                app.UseHsts();
            }

            // Add the following line:
            app.UseAzureAppConfiguration();

            app.UseHttpsRedirection();
            
            app.UseStaticFiles();

            app.UseRouting();

            app.UseAuthorization();

            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllerRoute(
                    name: "default",
                    pattern: "{controller=Home}/{action=Index}/{id?}");
            });
    }
    ```
    ---
    
    A köztes szoftver a metódusban `AddAzureAppConfiguration` megadott `Program.cs` frissítési konfigurációt használja a ASP.NET Core webalkalmazás által fogadott minden egyes kérelem frissítésének elindításához. Minden kérelemhez egy frissítési művelet aktiválódik, és az ügyféltár ellenőrzi, hogy a regisztrált konfigurációs beállítás gyorsítótárazott értéke lejárt-e. Ha lejárt, akkor frissül.

## <a name="use-the-latest-configuration-data"></a>A legújabb konfigurációs adatok használata

1. Nyissa *meg HomeController.cs* a Vezérlők könyvtárban, `Microsoft.Extensions.Options` és adjon hozzá hivatkozást a csomaghoz.

    ```csharp
    using Microsoft.Extensions.Options;
    ```

2. Frissítse `HomeController` az osztályt, hogy függőségi injektáláson keresztül kapjon, `Settings` és használja az értékeit.

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

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

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        private readonly ILogger<HomeController> _logger;

        public HomeController(ILogger<HomeController> logger, IOptionsSnapshot<Settings> settings)
        {
            _logger = logger;
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

        // ...
    }
    ```
    ---



3. Nyissa meg az *Index.cshtml fájlt* a Nézetek > kezdőkönyvtárában, és cserélje le annak tartalmát a következő parancsfájlra:

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <style>
        body {
            background-color: @ViewData["BackgroundColor"]
        }
        h1 {
            color: @ViewData["FontColor"];
            font-size: @ViewData["FontSize"]px;
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

## <a name="build-and-run-the-app-locally"></a>Az alkalmazás helyi létrehozása és futtatása

1. Ha az alkalmazást a .NET Core CLI segítségével szeretné felépíteni, futtassa a következő parancsot a parancshéjban:

        dotnet build

1. A sikeres build befejezése után futtassa a következő parancsot a webalkalmazás helyi futtatásához:

        dotnet run
1. Nyisson meg egy böngészőablakot, és `dotnet run` lépjen a kimenetben látható URL-címre.

    ![A rövid útmutató alkalmazás helyi indítása](./media/quickstarts/aspnet-core-app-launch-local-before.png)

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza az **Összes erőforrás**lehetőséget, és válassza ki a rövid útmutatóban létrehozott Alkalmazáskonfigurációs tároló példányt.

1. Válassza a **Configuration Explorer**lehetőséget, és frissítse a következő kulcsok értékeit:

    | Kulcs | Érték |
    |---|---|
    | TestApp:Beállítások:BackgroundColor | zöld |
    | TestApp:Beállítások:Betűszín | lightGray (világosszürke) |
    | TestApp:Beállítások:Üzenet | Adatok az Azure App Configuration szolgáltatásból – élő frissítésekkel! |
    | TestApp:Beállítások:Sentinel | 2 |

1. Frissítse a böngészőlapot az új konfigurációs beállítások megtekintéséhez. Előfordulhat, hogy a módosítások többször is frissítenie kell.

    ![Frissített gyorsindítási alkalmazás helyi indítása](./media/quickstarts/aspnet-core-app-launch-local-after.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban engedélyezte a ASP.NET Core webalkalmazás számára a konfigurációs beállítások dinamikus frissítését az alkalmazáskonfigurációból. Ha meg szeretné tudni, hogyan használhatja az Azure által felügyelt identitást az alkalmazáskonfigurációhoz való hozzáférés egyszerűsítéséhez, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Felügyelt identitásintegráció](./howto-integrate-azure-managed-service-identity.md)
