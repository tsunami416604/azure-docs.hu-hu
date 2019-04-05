---
title: 'Gyors útmutató: Azure-alkalmazás konfigurációja az ASP.NET Core használatának |} A Microsoft Docs'
description: A rövid útmutató az alkalmazások konfigurálása az Azure használata az ASP.NET Core-alkalmazásokkal
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
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 6c9b257ab88b3564253b0f48d953094f84a1d71d
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59051439"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>Gyors útmutató: ASP.NET Core-alkalmazás létrehozása az Azure-alkalmazás konfigurációja

Az Azure App konfigurálása felügyelt konfigurációs szolgáltatása az Azure-ban. Könnyedén tárolhatja, és a egy helyet, amely választja el az alkalmazás beállításait kezelheti a kódból használhatja azt. Ez a rövid útmutató bemutatja, hogyan belefoglalhatja a szolgáltatást az ASP.NET Core-webalkalmazás. 

ASP.NET Core egy vagy több olyan adatforrások, amelyek az alkalmazások által megadott beállítások használatával hoz létre egy egyetlen kulcs-érték-alapú konfigurációs objektumot. Ezeket az adatforrásokat nevezzük *konfigurációszolgáltatók*. Alkalmazások konfigurálása a .NET Core-ügyfél megvalósított ilyen egy szolgáltatót, mert a szolgáltatás egy másik adatforrás jelenik meg.

Bármely Kódszerkesztő segítségével ebben a rövid útmutatóban található lépések elvégzése. [A Visual Studio Code](https://code.visualstudio.com/) kiváló lehetőség a Windows, macOS és Linux platformokon az érhető el.

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató elvégzéséhez telepítse a [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Hozzon létre egy alkalmazást a konfigurációs adattároló

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core-webalkalmazás létrehozása

Használja a [.NET Core parancssori felület (CLI)](https://docs.microsoft.com/dotnet/core/tools/) hozhat létre egy új ASP.NET Core MVC webalkalmazás-projektet. A .NET Core parancssori felület a Visual Studio-val előnye, hogy legyen elérhető a Windows, macOS és Linux rendszerek között.

1. Hozzon létre egy új mappát a projekthez. Ez a rövid útmutatóhoz adja neki *TestAppConfig*.

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

1. Vegyen fel egy hivatkozást a `Microsoft.Extensions.Configuration.AzureAppConfiguration` NuGet-csomagot a következő parancs futtatásával:

        dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration --version 1.0.0-preview-007830001

2. Futtassa a következő parancsot a projekt-csomagok visszaállítására szolgáló:

        dotnet restore

3. Adja hozzá a titkos kulcs nevű *ConnectionStrings:AppConfig* Secret Manager.

    A titkos kód eléréséhez az alkalmazás a konfigurációs adattárolónál a kapcsolati karakterláncot tartalmazza. Az érték a következő parancsban cserélje le a kapcsolati karakterláncát az alkalmazás a konfigurációs adattároló.

    Ezt a parancsot abban a könyvtárban kell végrehajtani, ahol a *.csproj* fájl található.

        dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>

    SECRET Manager szolgál arra, hogy csak a webalkalmazás helyi tesztelése. Ha az alkalmazás telepítését, ha például a [Azure App Service](https://azure.microsoft.com/services/app-service/web), egy alkalmazás-beállításához, például használja **kapcsolati karakterláncok** App Service-ben. A kapcsolati karakterlánc Secret Manager helyett ezt a beállítást használja.

    A titkos kód API-konfigurációval érhető el. Egy kettőspontot (:) a konfiguráció nevét a konfigurációs API-t az összes támogatott platformon működik. Lásd: [környezet konfigurációjának](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0).

4. Nyissa meg *Program.cs*, és vegyen fel egy hivatkozást az alkalmazás konfigurációs .NET Core konfigurációszolgáltatót.

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
                           .SetOfflineCache(new OfflineFileCache());
                });
            })
            .UseStartup<Startup>();
    ```

6. Nyissa meg a nézetek Index.cshtml > directory otthoni, és cserélje le annak tartalmát az alábbira:

    ```html
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <!DOCTYPE html>
    <html lang="en">
    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"];
        }
    </style>
    <head>
        <title>Index View</title>
    </head>
    <body>
        <h1>@Configuration["TestApp:Settings:Message"]</h1>
    </body>
    </html>
    ```

7. Nyissa meg a nézetek _Layout.cshtml > directory megosztva, és cserélje le annak tartalmát az alábbira:

    ```html
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

## <a name="build-and-run-the-app-locally"></a>Hozhat létre, és az alkalmazás helyileg történő futtatása

1. Az alkalmazás létrehozása a .NET Core-CLI-vel, a következő parancsot az parancs-rendszerhéjba:

        dotnet build

2. A build sikeres befejezése után futtassa a következő parancsot a webalkalmazás helyi futtatásához:

        dotnet run

3. Nyisson meg egy böngészőablakot, és váltson `http://localhost:5000`, azaz a helyileg üzemeltetett webes alkalmazás alapértelmezett URL-CÍMÉT.

    ![A rövid útmutató alkalmazások indítása helyi](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy új alkalmazás-konfigurációs adattároló, és használta az ASP.NET Core-webalkalmazás-n keresztül a [alkalmazás konfigurációszolgáltató](https://go.microsoft.com/fwlink/?linkid=2074664). Ismerje meg, hogyan használhatja az Alkalmazáskonfigurációt, folytassa a következő oktatóanyag azt mutatja be a hitelesítést.

> [!div class="nextstepaction"]
> [Az Azure-erőforrások integráció felügyelt identitásokból](./integrate-azure-managed-service-identity.md)
