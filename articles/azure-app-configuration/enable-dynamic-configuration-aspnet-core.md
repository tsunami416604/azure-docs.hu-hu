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
ms.openlocfilehash: 44ae922b182874eef378d4868fb278c3c76252db
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56884791"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>Oktatóanyag: A dinamikus konfiguráció használata az ASP.NET Core-alkalmazás

ASP.NET Core, amely képes a konfigurációs adatok olvasása a különböző forrásokból származó, valamint a működés közbeni változások kezelésére anélkül, hogy ez az alkalmazás újraindítása moduláris konfigurációs rendszerrel rendelkezik. Támogatja a kötés .NET erősen típusos osztály konfigurációs beállításokat és injektálásra őket a kódot a különböző `IOptions<T>` mintákat. Ezek egyikét mintáit, kifejezetten `IOptionsSnapshot<T>`, automatikus újjáépítés az alkalmazást, az alapul szolgáló adatok változásakor biztosít. Is beszúrása `IOptionsSnapshot<T>` be tartományvezérlői eléréséhez a legfrissebb konfigurálási tárolva az Azure-alkalmazások konfigurálása az alkalmazásban. Ezenkívül, állítsa be az alkalmazás konfigurációs ASP.NET Core ügyféloldali kódtár, folyamatos figyelése és beolvasni egy alkalmazás a konfigurációs adattároló, amely rendszeres időközönként lekérdezési keresztül bármilyen változás.

Ez az oktatóanyag bemutatja, hogyan valósíthat meg dinamikus konfiguráció frissítéseit a kódban. A web app, a rövid útmutatók rendszerben bevezetett épül. Teljes [ASP.NET Core-alkalmazás létrehozása az alkalmazás konfigurációs](./quickstart-aspnet-core-app.md) első, a folytatás előtt.

A rövid útmutató lépései bármilyen szövegszerkesztővel elvégezhetők. A [Visual Studio Code](https://code.visualstudio.com/) például jó választás lehet, és Windows, macOS és Linux platformokon is használható.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Állítsa be az alkalmazás egy alkalmazás a konfigurációs adattároló változásainak válaszul a konfigurációjának frissítése
> * A legfrissebb konfigurációt, az alkalmazás vezérlők beszúrása

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez telepítse a [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>Töltse be újra az alkalmazás konfigurációs adatait

1. Nyissa meg *Program.cs* és frissítheti a `CreateWebHostBuilder` metódus hozzáadásával a `config.AddAzureAppConfiguration()` metódust.

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

    A második paramétert a `.Watch` módszer a lekérdezési időköz az ASP.NET-ügyfélkódtár kérdezi le az alkalmazás a konfigurációs adattároló megtekintheti, ha az adott konfigurációs beállítás bármilyen változás történt.

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

3. Nyissa meg *Startup.cs* és frissítheti a `ConfigureServices` metódus kötést létrehozni a konfigurációs adatokat a `Settings` osztály.

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

1. Nyissa meg *HomeController.cs* a a *tartományvezérlők* directory, a frissítés a `HomeController` fogadásához osztály `Settings` keresztül függőségi beszúrást, és lehetővé teheti az értékek használatát.

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

2. Nyissa meg *Index.cshtml* a a *nézetek* > *kezdőlap* könyvtárat, és cserélje ki annak tartalmát a következőre:

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

1. Ha az alkalmazást a .NET Core CLI használatával szeretné létrehozni, futtassa a következő parancsot a parancsfelületen:

        dotnet build

2. Ha a létrehozás sikeresen befejeződött, hajtsa végre a következő parancsot a webalkalmazás helyben való futtatásához:

        dotnet run

3. Egy böngészőablakot, és navigáljon a `http://localhost:5000`, azaz a helyileg üzemeltetett webes alkalmazás alapértelmezett URL-CÍMÉT.

    ![A rövid útmutató alkalmazások indítása helyi](./media/quickstarts/aspnet-core-app-launch-local-before.png)

4. Jelentkezzen be a [az Azure portal](https://aka.ms/azconfig/portal), kattintson a **összes erőforrás** és a konfigurációs tár-példányt, amely a rövid útmutatóban létrehozott.

5. Kattintson a **kulcs/érték Explorer** , és frissítse az értékeket a következő kulcsokat:

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

Ebben az oktatóanyagban egy Azure hozzáadott felügyeltszolgáltatás-identitás leegyszerűsítheti az Alkalmazáskonfigurációt hozzá, és az alkalmazás hitelesítőadat-kezelés javítására. Alkalmazáskonfiguráció használatával kapcsolatos további információkért folytassa az Azure CLI-minták.

> [!div class="nextstepaction"]
> [CLI-minták](./cli-samples.md)
