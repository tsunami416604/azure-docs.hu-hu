---
title: Gyors útmutató az Azure-alkalmazások konfigurálásához a ASP.NET Corekal | Microsoft Docs
description: Útmutató az Azure-alkalmazások konfigurációjának ASP.NET Core alkalmazásokkal való használatához
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: ASP.NET Core
ms.workload: tbd
ms.date: 10/11/2019
ms.author: yegu
ms.openlocfilehash: 91712b3f730317e65cda7b48c8f5636b2fb9ab2c
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185091"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>Gyors útmutató: ASP.NET Core-alkalmazás létrehozása az Azure-alkalmazás konfigurálásával

Ebben a rövid útmutatóban beépíti az Azure-alkalmazások konfigurációját egy ASP.NET Core alkalmazásba, hogy központilag kezelje az alkalmazás-beállítások tárolását és kezelését a kódból elválasztva. A ASP.NET Core egy kulcs-érték alapú konfigurációs objektumot hoz létre egy alkalmazás által megadott adatforrások beállításainak használatával. Ezeket az adatforrásokat *konfigurációs szolgáltatóknak*nevezzük. Mivel az alkalmazás konfigurációjának .NET Core-ügyfele a szolgáltatóként van megvalósítva, a szolgáltatás egy másik adatforráshoz hasonlóan jelenik meg.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
- [.NET Core SDK](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Alkalmazás-konfigurációs tároló létrehozása

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Válassza a **Configuration Explorer** >  **+ Létrehozás** lehetőséget a következő kulcs-érték párok hozzáadásához:

    | Paraméter | Érték |
    |---|---|
    | TestApp:Settings:BackgroundColor | Fehér |
    | TestApp:Settings:FontSize | 24 |
    | TestApp:Settings:FontColor | Fekete |
    | TestApp:Settings:Message | Adatok az Azure-alkalmazás konfigurációjától |

    Most hagyja üresen a **címke** és a **tartalom típusát** .

## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core-webalkalmazás létrehozása

A [.net Core parancssori felület (CLI)](https://docs.microsoft.com/dotnet/core/tools/) használatával hozzon létre egy új ASP.net Core MVC webalkalmazás-projektet. A a .NET Core parancssori felülete a Visual Studióban való használatának előnye, hogy a Windows, macOS és Linux platformokon is elérhető.

1. Hozzon létre egy új mappát a projekthez. Ebben a rövid útmutatóban nevezze el *TestAppConfig*.

2. Az új mappában futtassa az alábbi parancsot egy új ASP.NET Core MVC webalkalmazás-projekt létrehozásához:

    ```CLI
        dotnet new mvc --no-https
    ```

## <a name="add-secret-manager"></a>Secret Manager hozzáadása

A Secret Manager használatához adjon hozzá egy `UserSecretsId` elemet a *. csproj* fájlhoz.

- Nyissa meg a *. csproj* fájlt. Vegyen fel egy `UserSecretsId` elemet az itt látható módon. Használhatja ugyanazt a GUID azonosítót, vagy lecserélheti ezt az értéket a saját értékére is. Mentse a fájlt.

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

A Secret Manager eszköz tárolja a projektfán kívüli fejlesztési feladatokhoz tartozó bizalmas adatokat. Ez a módszer megakadályozza, hogy véletlenül megossza az alkalmazás forráskódbeli titkos kódjait. A Secret Managerrel kapcsolatos további információkért lásd: az [alkalmazás-titkok biztonságos tárolása a fejlesztésben ASP.net Core](https://docs.microsoft.com/aspnet/core/security/app-secrets)

## <a name="connect-to-an-app-configuration-store"></a>Kapcsolódás alkalmazás-konfigurációs tárolóhoz

1. Adja hozzá a `Microsoft.Azure.AppConfiguration.AspNetCore` NuGet-csomagra mutató hivatkozást a következő parancs futtatásával:

    ```CLI
        dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore --version 2.0.0-preview-010060003-1250
    ```
2. Futtassa a következő parancsot a projekt csomagjainak visszaállításához:

    ```CLI
        dotnet restore
    ```
3. Adjon hozzá egy *ConnectionStrings: AppConfig* nevű titkos kulcsot a Secret Managerhez.

    Ez a titok tartalmazza a kapcsolati karakterláncot az alkalmazás konfigurációs tárolójának eléréséhez. Cserélje le a következő parancs értékét az alkalmazás konfigurációs tárolójának a kapcsolatok karakterláncára.

    Ezt a parancsot abban a könyvtárban kell végrehajtani, ahol a *.csproj* fájl található.

    ```CLI
        dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>
    ```

    > [!IMPORTANT]
    > Egyes rendszerhéjak megcsonkítják a kapcsolatok karakterláncát, kivéve, ha idézőjelek közé vannak zárva. Győződjön meg arról, hogy az `dotnet user-secrets` parancs kimenete a teljes hálózati karakterláncot jeleníti meg. Ha nem, futtassa újra a parancsot, és foglalja bele a kapcsolatok karakterláncát idézőjelek közé.

    A Secret Manager csak a webalkalmazás helyi tesztelésére szolgál. Ha az alkalmazás üzembe helyezése [Azure app Servicere](https://azure.microsoft.com/services/app-service/web)történik, például egy alkalmazás-beállítási **karakterláncot** használ app Service helyett a Secret Managerrel a kapcsolódási karakterlánc tárolásához.

    Ez a titok a konfigurációs API-val érhető el. Egy kettőspont (:) a konfiguráció neve a konfigurációs API-val minden támogatott platformon használható. Lásd: [konfiguráció környezet alapján](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0).

4. Nyissa meg a *program.cs*, és adjon hozzá egy hivatkozást a .net Core app Configuration Provider szolgáltatáshoz.

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

5. A `config.AddAzureAppConfiguration()` metódus meghívásával frissítse a `CreateWebHostBuilder` metódust az alkalmazás konfigurációjának használatához.
    
    > [!IMPORTANT]
    > a `CreateHostBuilder` a .NET Core 3,0 `CreateWebHostBuilder` helyettesíti.  Válassza ki a megfelelő szintaxist a környezet alapján.

    ### <a name="update-createwebhostbuilder-for-net-core-2x"></a>A .NET Core 2. x `CreateWebHostBuilder` frissítése

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
            })
            .UseStartup<Startup>();
    ```

    ### <a name="update-createhostbuilder-for-net-core-3x"></a>A .NET Core 3. x `CreateHostBuilder` frissítése

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
        })
        .UseStartup<Startup>());
    ```

6. Nyissa meg az *index. cshtml* a views > Home könyvtárban, és cserélje le a tartalmát a következő kódra:

    ```HTML
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"];
        }
    </style>

    <h1>@Configuration["TestApp:Settings:Message"]</h1>
    ```

7. Nyissa meg a *_Layout. cshtml* mappát a megosztott könyvtár nézeteiben >, és cserélje le a tartalmát a következő kódra:

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>@ViewData["Title"] - hello_world</title>

        <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.css" />
        <link rel="stylesheet" href="~/css/site.css" />
    </head>
    <body>
        <div class="container body-content">
            @RenderBody()
        </div>

        <script src="~/lib/jquery/dist/jquery.js"></script>
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>

        @RenderSection("Scripts", required: false)
    </body>
    </html>
    ```

## <a name="build-and-run-the-app-locally"></a>Az alkalmazás helyi létrehozása és futtatása

1. Ha az alkalmazást a a .NET Core parancssori felülete használatával szeretné felépíteni, futtassa a következő parancsot a parancs-rendszerhéjban:

    ```CLI
       dotnet build
    ```

2. A létrehozás sikeres befejezése után futtassa a következő parancsot a webalkalmazás helyi futtatásához:

    ```CLI
        dotnet run
    ```

3. Nyisson meg egy böngészőablakot, és lépjen a `http://localhost:5000`elemre, amely a helyileg üzemeltetett webalkalmazás alapértelmezett URL-címe.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy új alkalmazás-konfigurációs tárolót, és azt egy ASP.NET Core webalkalmazással használta az [alkalmazás-konfigurációs szolgáltatón](https://go.microsoft.com/fwlink/?linkid=2074664)keresztül. Ha szeretné megtudni, hogyan konfigurálhatja a ASP.NET Core alkalmazást a konfigurációs beállítások dinamikus frissítéséhez, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Dinamikus konfiguráció engedélyezése](./enable-dynamic-configuration-aspnet-core.md)
