---
title: Oktatóanyag az Azure-alkalmazások konfigurációjának dinamikus konfigurációjának használatához egy ASP.NET Core alkalmazásban | Microsoft Docs
description: Ez az oktatóanyag azt ismerteti, hogyan lehet dinamikusan frissíteni a ASP.NET Core-alkalmazások konfigurációs információit
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
ms.openlocfilehash: f49161531753c217e31d0681bcd19043cb47de75
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185268"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>Oktatóanyag: dinamikus konfiguráció használata egy ASP.NET Core alkalmazásban

ASP.NET Core olyan csatlakoztatható konfigurációs rendszerrel rendelkezik, amely különböző forrásokból származó konfigurációs adatok olvasására képes. A módosításokat menet közben is kezelheti anélkül, hogy egy alkalmazást újra kellene indítani. ASP.NET Core támogatja a konfigurációs beállítások kötését a .NET-osztályokhoz. A különböző `IOptions<T>` minták használatával beinjektálja őket a kódban. Az egyik ilyen minta, különösen `IOptionsSnapshot<T>`, automatikusan újratölti az alkalmazás konfigurációját, amikor az alapul szolgáló adat megváltozik. Az Azure-alkalmazás konfigurációjában tárolt legújabb konfiguráció eléréséhez beillesztheti `IOptionsSnapshot<T>`eit az alkalmazásban lévő vezérlőkbe.

Emellett beállíthatja az alkalmazás konfigurációját ASP.NET Core ügyfél-függvénytárat, hogy dinamikusan frissítse a konfigurációs beállításokat egy middleware használatával. Ha a webalkalmazás továbbra is fogad kérelmeket, a konfigurációs beállítások továbbra is frissülnek a konfigurációs tárolóval.

A beállítások frissítésének megtartásához és a konfigurációs tárolóhoz való túl sok hívás elkerüléséhez minden beállításhoz gyorsítótárat kell használni. Amíg a beállítás gyorsítótárazott értéke lejárt, a frissítési művelet nem frissíti az értéket, még akkor is, ha az érték módosult a konfigurációs tárolóban. Az egyes kérések alapértelmezett lejárati ideje 30 másodperc, de szükség esetén felül lehet bírálni.

Ez az oktatóanyag bemutatja, hogyan valósítható meg a dinamikus konfigurációs frissítések a kódban. A szolgáltatás a gyors útmutatókban bemutatott webalkalmazásra épül. A folytatás előtt fejezze be a [ASP.net Core alkalmazás létrehozása az alkalmazás-konfigurációval](./quickstart-aspnet-core-app.md) először.

Az oktatóanyag lépéseihez bármilyen Kódszerkesztő használható. A [Visual Studio Code](https://code.visualstudio.com/) egy kiváló lehetőség, amely a Windows, MacOS és Linux platformokon érhető el.

Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Állítsa be úgy az alkalmazást, hogy frissítse a konfigurációját az alkalmazás konfigurációs tárolójában történt változásokra reagálva.
> * Adja meg a legújabb konfigurációt az alkalmazás vezérlői között.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez telepítse a [.net Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

A folytatás előtt fejezze be a [ASP.net Core alkalmazás létrehozása az alkalmazás-konfigurációval](./quickstart-aspnet-core-app.md) először.

## <a name="reload-data-from-app-configuration"></a>Adatok újratöltése az alkalmazás konfigurációjától

1. Adja hozzá a `Microsoft.Azure.AppConfiguration.AspNetCore` NuGet-csomagra mutató hivatkozást a következő parancs futtatásával:

    ```CLI
        dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore --version 2.0.0-preview-010060003-1250
    ```

1. Nyissa meg a *program.cs*, és frissítse a `CreateWebHostBuilder` metódust a `config.AddAzureAppConfiguration()` metódus hozzáadásához.

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
                                      .Register("TestApp:Settings:Message");
                           });
                });
            })
            .UseStartup<Startup>();
    ```

    A `ConfigureRefresh` metódussal adhatja meg azokat a beállításokat, amelyeket a konfigurációs adatainak az alkalmazás konfigurációs tárolójával való frissítéséhez használ a frissítési művelet elindításakor. A frissítési művelet tényleges kiváltásához be kell állítani az alkalmazás frissítését, hogy a rendszer bármilyen változás esetén frissítse a konfigurációs adataikat.

2. Adjon hozzá egy új `Settings` osztályt definiáló és megvalósító *Settings.cs* -fájlt.

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

3. Nyissa meg a *Startup.cs*, és frissítse a `ConfigureServices` metódust a konfigurációs adat`Settings` osztályhoz való kötéshez.

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

4. Frissítse a `Configure` metódust egy middleware hozzáadásához, amely lehetővé teszi a frissítéshez regisztrált konfigurációs beállítások frissítését, miközben a ASP.NET Core webalkalmazás továbbra is fogadja a kéréseket.

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseAzureAppConfiguration();
        app.UseMvc();
    }
    ```
    
    A middleware a `Program.cs` `AddAzureAppConfiguration` metódusában megadott frissítési konfigurációt használja az ASP.NET Core-webalkalmazás által fogadott egyes kérelmek frissítésének elindításához. Minden kérelem esetében egy frissítési művelet aktiválódik, és az ügyféloldali kódtár ellenőrzi, hogy a regisztrált konfigurációs beállítások gyorsítótárazott értéke lejárt-e. A lejárt gyorsítótárazott értékek esetében a beállítások értékei frissülnek az alkalmazás konfigurációs tárolójával, és a fennmaradó értékek változatlanok maradnak.
    
    > [!NOTE]
    > A konfigurációs beállítás alapértelmezett gyorsítótár-lejárati ideje 30 másodperc, de felülbírálható úgy, hogy meghívja a `SetCacheExpiration` metódust az `ConfigureRefresh` metódus argumentumként átadott inicializálási beállításokkal.

## <a name="use-the-latest-configuration-data"></a>A legújabb konfigurációs adatértékek használata

1. Nyissa meg a *HomeController.cs* a vezérlők könyvtárban, és adjon hozzá egy hivatkozást a `Microsoft.Extensions.Options` csomaghoz.

    ```csharp
    using Microsoft.Extensions.Options;
    ```

2. Frissítse a `HomeController` osztályt a függőségi injektáláson keresztüli `Settings` fogadására, és használja ki az értékét.

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

3. Nyissa meg az *index. cshtml* a views > Home könyvtárban, és cserélje le a tartalmát a következő parancsfájlra:

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

## <a name="build-and-run-the-app-locally"></a>Az alkalmazás helyi létrehozása és futtatása

1. Ha az alkalmazást a a .NET Core parancssori felülete használatával szeretné felépíteni, futtassa a következő parancsot a parancs-rendszerhéjban:

        dotnet build

2. A létrehozás sikeres befejezése után futtassa a következő parancsot a webalkalmazás helyi futtatásához:

        dotnet run

3. Nyisson meg egy böngészőablakot, és lépjen a `http://localhost:5000`elemre, amely a helyileg üzemeltetett webalkalmazás alapértelmezett URL-címe.

    ![Gyorsindítás alkalmazás elindítása helyi](./media/quickstarts/aspnet-core-app-launch-local-before.png)

4. Bejelentkezés az [Azure Portalra](https://portal.azure.com). Válassza a **minden erőforrás**lehetőséget, majd válassza ki a gyors útmutatóban létrehozott app Configuration Store-példányt.

5. Válassza a **Configuration Explorer**lehetőséget, és frissítse a következő kulcsok értékeit:

    | Paraméter | Érték |
    |---|---|
    | TestApp:Settings:BackgroundColor | zöld |
    | TestApp:Settings:FontColor | lightGray |
    | TestApp:Settings:Message | Adatok az Azure app Configuration szolgáltatásból – mostantól élő frissítésekkel! |

6. A böngésző oldalának frissítésével tekintheti meg az új konfigurációs beállításokat. A változtatások megjelenítéséhez a böngésző lapja több frissítése is szükséges lehet.

    ![Rövid útmutató alkalmazás helyi frissítése](./media/quickstarts/aspnet-core-app-launch-local-after.png)
    
    > [!NOTE]
    > Mivel a konfigurációs beállításokat a rendszer 30 másodperces alapértelmezett lejárati idővel gyorsítótárazza, az alkalmazás konfigurációs tárolójának beállításaiban történt módosítások csak a webalkalmazásban jelennek meg, ha a gyorsítótár lejárt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban engedélyezte a ASP.NET Core webalkalmazásának, hogy dinamikusan frissítse a konfigurációs beállításokat az alkalmazás konfigurációjában. Ha meg szeretné tudni, hogyan használható az Azure felügyelt identitása az alkalmazás-konfigurációhoz való hozzáférés egyszerűsítéséhez, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Felügyelt identitások integrációja](./howto-integrate-azure-managed-service-identity.md)
