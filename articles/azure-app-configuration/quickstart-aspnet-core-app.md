---
title: Az Azure App Configuration with ASP.NET Core rövid útmutatója | Microsoft dokumentumok
description: Gyorsútmutató az Azure App Configuration és ASP.NET Core alkalmazások használatához
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: 537dabe09c41012b9e15998ce3af8198dcfb62d3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80245774"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>Rövid útmutató: Hozzon létre egy ASP.NET Core alkalmazást az Azure App konfigurációjával

Ebben a rövid útmutatóban az Azure App Configuration használatával központosíthatja a ASP.NET Core alkalmazás tárolási és felügyeleti beállításait. ASP.NET Core egyetlen kulcsérték-alapú konfigurációs objektumot hoz létre egy vagy több, egy alkalmazás által megadott adatforrás beállításaialapján. Ezeket az adatforrásokat *konfigurációszolgáltatóknak nevezzük.* Mivel az Alkalmazáskonfiguráció .NET Core ügyfele konfigurációs szolgáltatóként van megvalósítva, a szolgáltatás egy másik adatforrásként jelenik meg.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
- [.NET Core SDK](https://dotnet.microsoft.com/download)

>[!TIP]
> Az Azure Cloud Shell egy ingyenes interaktív rendszerhéj, amely segítségével futtathatja a parancssori utasításokat ebben a cikkben.  Előtelepített azure-eszközökkel rendelkezik, beleértve a .NET Core SDK-t is. Ha be van jelentkezve az Azure-előfizetésbe, indítsa el az [Azure Cloud Shell-t](https://shell.azure.com) shell.azure.com.  Az Azure Cloud Shellről a [dokumentáció elolvasásával](../cloud-shell/overview.md) tudhat meg többet.

## <a name="create-an-app-configuration-store"></a>Alkalmazáskonfigurációs tároló létrehozása

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Válassza a **Configuration Explorer** > **Kulcsérték** **létrehozása** > lehetőséget a következő kulcsérték-párok hozzáadásához:

    | Kulcs | Érték |
    |---|---|
    | TestApp:Beállítások:BackgroundColor | Fehér |
    | TestApp:Beállítások:Betűméret | 24 |
    | TestApp:Beállítások:Betűszín | Fekete |
    | TestApp:Beállítások:Üzenet | Az Azure App konfigurációjából származó adatok |

    Egyelőre hagyja üresen a **Címke** és **a Tartalomtípus** mezőt. Kattintson az **Alkalmaz** gombra.

## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core-webalkalmazás létrehozása

A [.NET Core parancssori felület (CLI)](https://docs.microsoft.com/dotnet/core/tools/) segítségével hozzon létre egy új ASP.NET Core MVC webalkalmazás-projektet. Az [Azure Cloud Shell](https://shell.azure.com) biztosítja ezeket az eszközöket az Ön számára.  Windows, macOS és Linux platformokon is elérhetők.

1. Hozzon létre egy új mappát a projekthez. Ehhez a rövid útmutatóhoz nevezze el *TestAppConfig*.

1. Az új mappában futtassa a következő parancsot egy új ASP.NET Core MVC webalkalmazás-projekt létrehozásához:

```dotnetcli
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
> Ha többet szeretne megtudni a Secret Managerről, olvassa el [az alkalmazástitkok biztonságos tárolása a fejlesztés során ASP.NET Core-ban](https://docs.microsoft.com/aspnet/core/security/app-secrets)

## <a name="connect-to-an-app-configuration-store"></a>Csatlakozás alkalmazáskonfigurációs tárolóhoz

1. Adjon hozzá hivatkozást a `Microsoft.Azure.AppConfiguration.AspNetCore` NuGet csomaghoz a következő parancs futtatásával:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

1. A következő parancs futtatásával állítsa vissza a projekt csomagjait:

    ```dotnetcli
    dotnet restore
    ```

1. Adjon hozzá egy *ConnectionStrings:AppConfig* nevű titkos kulcsot a Titkos kezelőhöz.

    Ez a titkos adata tartalmazza az alkalmazáskonfigurációs tároló eléréséhez a kapcsolati karakterláncot. Cserélje le a következő parancs ban lévő értéket az alkalmazáskonfigurációs tároló kapcsolati karakterláncára. A kapcsolati karakterlánc az **Access Keys** az Azure Portalon található.

    Ezt a parancsot abban a könyvtárban kell végrehajtani, ahol a *.csproj* fájl található.

    ```dotnetcli
    dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>
    ```

    > [!IMPORTANT]
    > Egyes rendszerhéjak csonkolják a kapcsolati karakterláncot, kivéve, ha idézőjelek közé van zárva. Győződjön meg arról, hogy a parancs kimenete a `dotnet user-secrets` teljes kapcsolati karakterláncot mutatja. Ha nem, futtassa újra a parancsot, és a kapcsolati karakterláncot idézőjelek közé zárja.

    A Secret Manager csak a webalkalmazás helyi tesztelésére szolgál. Ha például az alkalmazás telepítve van az [Azure App Service szolgáltatásban,](https://azure.microsoft.com/services/app-service/web)a **kapcsolati karakterláncok** alkalmazásbeállítását használja az App Service-ben a Secret Manager helyett a kapcsolati karakterlánc tárolásához.

    A titkos kulcs elérése a konfigurációs API használatával. A kettőspont (:) A konfigurációs névvel működik a konfigurációs API-val az összes támogatott platformon. Lásd: [Környezet szerint beállítás.](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0)

1. Nyissa *meg a Program.cs,* és adjon hozzá hivatkozást a .NET Core alkalmazáskonfigurációs szolgáltatóhoz.

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. Frissítse `CreateWebHostBuilder` a metódust az alkalmazáskonfiguráció használatához a `config.AddAzureAppConfiguration()` metódus hívásával.

    > [!IMPORTANT]
    > `CreateHostBuilder`a `CreateWebHostBuilder` .NET Core 3.0-ban.  Válassza ki a megfelelő szintaxist a környezet alapján.

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

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

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

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

1. Keresse meg a * <app root>/Views/Home* és a open *Index.cshtml könyvtárat.* A tartalom lecserélése a következő kódra:

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

1. Keresse meg a * <app root>/Views/Shared mappát,* és nyissa meg *a _Layout.cshtml*. A tartalom lecserélése a következő kódra:

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

1. Ha az alkalmazást a .NET Core CLI használatával szeretné felépíteni, keresse meg az alkalmazás gyökérkönyvtárát, és futtassa a következő parancsot a parancshéjban:

    ```dotnetcli
    dotnet build
    ```

1. A sikeres build befejezése után futtassa a következő parancsot a webalkalmazás helyi futtatásához:

    ```dotnetcli
    dotnet run
    ```

1. Ha a helyi számítógépen dolgozik, a böngésző `http://localhost:5000`segítségével keresse meg a t. Ez a helyileg üzemeltetett webalkalmazás alapértelmezett URL-címe.  

Ha az Azure Cloud Shellben dolgozik, válassza a *Webes előnézet* gombot, majd a *Configure*lehetőséget.  

![A Webes előnézet gomb megkeresése](./media/quickstarts/cloud-shell-web-preview.png)

Amikor a rendszer kéri a port előnézeti beállítását, írja be az "5000" értéket, és válassza a *Megnyitás és tallózás*lehetőséget.  A weboldalon az "Azure App Configuration adatai" felirat olvasható.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy új App Configuration Store-t, és egy ASP.NET Core webalkalmazással használta az [alkalmazáskonfigurációs szolgáltatón](https://go.microsoft.com/fwlink/?linkid=2074664)keresztül. Ha meg szeretné tudni, hogyan állíthatja be a ASP.NET Core alkalmazást a konfigurációs beállítások dinamikus frissítésére, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Dinamikus konfiguráció engedélyezése](./enable-dynamic-configuration-aspnet-core.md)
