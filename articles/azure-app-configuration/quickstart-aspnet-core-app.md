---
title: Gyors útmutató az Azure-alkalmazások konfigurálásához a ASP.NET Corekal | Microsoft Docs
description: Gyors útmutató az Azure-alkalmazások konfigurációjának ASP.NET Core alkalmazásokkal való használatához
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.date: 01/21/2020
ms.author: lcozzens
ms.openlocfilehash: 657edbac67a53952ab79aee74b63c59a44b7de18
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76898661"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>Gyors útmutató: ASP.NET Core-alkalmazás létrehozása az Azure-alkalmazás konfigurálásával

Ebben a rövid útmutatóban az Azure-alkalmazások konfigurációját fogja használni az ASP.NET Core alkalmazások alkalmazási beállításainak tárolásához és kezeléséhez. A ASP.NET Core egy kulcs-érték alapú konfigurációs objektumot hoz létre, amely egy vagy több alkalmazás által megadott adatforrásból származó beállításokat használ. Ezeket az adatforrásokat *konfigurációs szolgáltatóknak*nevezzük. Mivel az alkalmazás konfigurációja .NET Core-ügyfele konfigurációs szolgáltatóként van implementálva, a szolgáltatás egy másik adatforráshoz hasonlóan jelenik meg.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
- [.NET Core SDK](https://dotnet.microsoft.com/download)

>[!TIP]
> A Azure Cloud Shell egy ingyenes interaktív felület, amellyel a cikkben ismertetett parancssori utasításokat futtathatja.  A közös Azure-eszközök előre telepítve vannak, beleértve a .NET Core SDK. Ha bejelentkezett az Azure-előfizetésbe, indítsa el a [Azure Cloud shellt](https://shell.azure.com) a shell.Azure.com webhelyről.  A Azure Cloud Shellről a [dokumentáció beolvasásával](../cloud-shell/overview.md) tájékozódhat bővebben

## <a name="create-an-app-configuration-store"></a>Alkalmazás-konfigurációs tároló létrehozása

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Válassza a **Configuration Explorer** > **Létrehozás** lehetőséget a következő kulcs-érték párok hozzáadásához:

    | Jelmagyarázat | Value (Díj) |
    |---|---|
    | TestApp: beállítások: BackgroundColor | Fehér |
    | TestApp: beállítások: FontSize | 24 |
    | TestApp: beállítások: FontColor | Fekete |
    | TestApp: beállítások: üzenet | Adatok az Azure-alkalmazás konfigurációjától |

    Most hagyja üresen a **címke** és a **tartalom típusát** .

## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core-webalkalmazás létrehozása

A [.net Core parancssori felület (CLI)](https://docs.microsoft.com/dotnet/core/tools/) használatával hozzon létre egy új ASP.net Core MVC webalkalmazás-projektet. A [Azure Cloud Shell](https://shell.azure.com) ezeket az eszközöket biztosítja Önnek.  A Windows, a macOS és a Linux platformokon is elérhetők.

1. Hozzon létre egy új mappát a projekthez. Ebben a rövid útmutatóban nevezze el *TestAppConfig*.

1. Az új mappában futtassa az alábbi parancsot egy új ASP.NET Core MVC webalkalmazás-projekt létrehozásához:

```dotnetcli
dotnet new mvc --no-https
```

## <a name="add-secret-manager"></a>Secret Manager hozzáadása

A Secret Manager használatához adjon hozzá egy `UserSecretsId` elemet a *. csproj* fájlhoz.

Nyissa meg a *. csproj* fájlt. Vegyen fel egy `UserSecretsId` elemet az itt látható módon. Használhatja ugyanazt a GUID azonosítót, vagy lecserélheti ezt az értéket a saját értékére is. Mentse a fájlt.

> [!IMPORTANT]
> a `CreateHostBuilder` a .NET Core 3,0 `CreateWebHostBuilder` helyettesíti.  Válassza ki a megfelelő szintaxist a környezet alapján.

#### <a name="net-core-2xtabcore2x"></a>[.NET Core 2. x](#tab/core2x)

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

#### <a name="net-core-3xtabcore3x"></a>[.NET Core 3. x](#tab/core3x)

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    
    <PropertyGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
        <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
    </PropertyGroup>

</Project>
```
---

A Secret Manager eszköz tárolja a projektfán kívüli fejlesztési feladatokhoz tartozó bizalmas adatokat. Ez a módszer megakadályozza, hogy véletlenül megossza az alkalmazás forráskódbeli titkos kódjait. A Secret Managerrel kapcsolatos további információkért lásd: az [alkalmazás-titkok biztonságos tárolása a fejlesztésben ASP.net Core](https://docs.microsoft.com/aspnet/core/security/app-secrets)

## <a name="connect-to-an-app-configuration-store"></a>Kapcsolódás alkalmazás-konfigurációs tárolóhoz

1. Adja hozzá a `Microsoft.Azure.AppConfiguration.AspNetCore` NuGet-csomagra mutató hivatkozást a következő parancs futtatásával:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore --version 3.0.0-preview-011100002-1192
    ```

1. Futtassa a következő parancsot a projekt csomagjainak visszaállításához:

    ```dotnetcli
    dotnet restore
    ```

1. Adjon hozzá egy *ConnectionStrings: AppConfig* nevű titkos kulcsot a Secret Managerhez.

    Ez a titok tartalmazza a kapcsolati karakterláncot az alkalmazás konfigurációs tárolójának eléréséhez. Cserélje le a következő parancs értékét az alkalmazás konfigurációs tárolójának a kapcsolatok karakterláncára.

    Ezt a parancsot abban a könyvtárban kell végrehajtani, ahol a *.csproj* fájl található.

    ```dotnetcli
    dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>
    ```

    > [!IMPORTANT]
    > Egyes rendszerhéjak megcsonkítják a kapcsolatok karakterláncát, kivéve, ha idézőjelek közé vannak zárva. Győződjön meg arról, hogy az `dotnet user-secrets` parancs kimenete a teljes hálózati karakterláncot jeleníti meg. Ha nem, futtassa újra a parancsot, és foglalja bele a kapcsolatok karakterláncát idézőjelek közé.

    A Secret Manager csak a webalkalmazás helyi tesztelésére szolgál. Ha az alkalmazás üzembe helyezése [Azure app Service](https://azure.microsoft.com/services/app-service/web), például a **kapcsolódási** karakterláncok alkalmazás beállítása a app Service helyett a Secret Manager a kapcsolódási karakterlánc tárolásához.

    Ezt a titkot a konfigurációs API használatával érheti el. Egy kettőspont (:) a konfiguráció neve a konfigurációs API-val minden támogatott platformon használható. Lásd: [konfiguráció környezet alapján](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0).

1. Nyissa meg a *program.cs*, és adjon hozzá egy hivatkozást a .net Core app Configuration Provider szolgáltatáshoz.

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. A `config.AddAzureAppConfiguration()` metódus meghívásával frissítse a `CreateWebHostBuilder` metódust az alkalmazás konfigurációjának használatához.

    > [!IMPORTANT]
    > a `CreateHostBuilder` a .NET Core 3,0 `CreateWebHostBuilder` helyettesíti.  Válassza ki a megfelelő szintaxist a környezet alapján.

    #### <a name="net-core-2xtabcore2x"></a>[.NET Core 2. x](#tab/core2x)

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

    #### <a name="net-core-3xtabcore3x"></a>[.NET Core 3. x](#tab/core3x)

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

    ---

1. Navigáljon *<app root>/views/Home* , és nyissa meg az *index. cshtml*. Cserélje le a tartalmát a következő kódra:

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

1. Navigáljon *<app root>/views/Shared* , és nyissa meg *_Layout. cshtml*. Cserélje le a tartalmát a következő kódra:

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

1. Ha az alkalmazást a a .NET Core parancssori felülete használatával szeretné felépíteni, navigáljon az alkalmazás gyökérkönyvtárához, és futtassa a következő parancsot a parancssorban:

    ```dotnetcli
    dotnet build
    ```

1. A létrehozás sikeres befejezése után futtassa a következő parancsot a webalkalmazás helyi futtatásához:

    ```dotnetcli
    dotnet run
    ```

1. Ha a helyi gépen dolgozik, a böngészőben navigáljon `http://localhost:5000`. Ez a helyileg üzemeltetett webalkalmazás alapértelmezett URL-címe.  

Ha dolgozik a Azure Cloud Shellban, válassza a *webes előnézet* gombot, majd a *configure (Konfigurálás*) lehetőséget.  

![A webes előnézet gomb megkeresése](./media/quickstarts/cloud-shell-web-preview.png)

Amikor a rendszer kéri, hogy konfigurálja a portot az előzetes verzióhoz, írja be a "5000" értéket, és válassza a *Megnyitás és Tallózás*lehetőséget.  A weblap beolvassa az "adatok az Azure-alkalmazás konfigurációjától" című lapot.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy új alkalmazás-konfigurációs tárolót, és azt egy ASP.NET Core webalkalmazással használta az [alkalmazás-konfigurációs szolgáltatón](https://go.microsoft.com/fwlink/?linkid=2074664)keresztül. Ha szeretné megtudni, hogyan konfigurálhatja a ASP.NET Core alkalmazást a konfigurációs beállítások dinamikus frissítéséhez, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Dinamikus konfiguráció engedélyezése](./enable-dynamic-configuration-aspnet-core.md)
