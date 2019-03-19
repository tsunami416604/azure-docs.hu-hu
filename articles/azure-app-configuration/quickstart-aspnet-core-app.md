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
ms.openlocfilehash: 6d41b6e4ab6ea01017f39762e7d022d61350d21a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57998444"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>Gyors útmutató: ASP.NET Core-alkalmazás létrehozása az Azure-alkalmazás konfigurációja

Az Azure App konfigurálása felügyelt konfigurációs szolgáltatása az Azure-ban. Ez lehetővé teszi, hogy könnyedén tárolhatja és kezelheti a kódot egy helyet, amely választja el az alkalmazás beállításait. Ez a rövid útmutató bemutatja, hogyan belefoglalhatja a szolgáltatást az ASP.NET Core-webalkalmazás. ASP.NET Core összeállít egy egyetlen kulcs-érték alapján konfigurációs objektumot, egy vagy több adatforrásokból származó, más néven beállításokkal *konfigurációszolgáltatók*, az alkalmazások által megadott. Alkalmazások konfigurálása a .NET Core-ügyfél megvalósított ilyen egy szolgáltatót, mert a szolgáltatás egy másik adatforrás hasonlóan jelenik meg.

A rövid útmutató lépései bármilyen szövegszerkesztővel elvégezhetők. A [Visual Studio Code](https://code.visualstudio.com/) például jó választás lehet, és Windows, macOS és Linux platformokon is használható.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez telepítse a [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Hozzon létre egy alkalmazást a konfigurációs adattároló

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core-webalkalmazás létrehozása

Használhatja a [.NET Core parancssori felület (CLI)](https://docs.microsoft.com/dotnet/core/tools/) hozhat létre egy új ASP.NET Core MVC webalkalmazás projekthez. A .NET Core CLI előnye a Visual Studióval szemben az, hogy Windows, macOS és Linux platformokon is használható.

1. Hozzon létre egy új mappát a projekthez. Ebben a rövid útmutatóban a rendszer nevet használjuk *TestAppConfig*.

2. Az új mappában hozzon létre egy új ASP.NET Core MVC webalkalmazás-projektet a következő paranccsal:

        dotnet new mvc

## <a name="add-secret-manager"></a>Add Secret Manager

Hozzáadja a [Secret Manager eszköz](https://docs.microsoft.com/aspnet/core/security/app-secrets) a projekthez. A Secret Manager eszköz tárolja a projektfán kívüli fejlesztési feladatokhoz tartozó bizalmas adatokat. Ez a módszer megakadályozza, hogy véletlenül megossza az alkalmazás forráskódbeli titkos kódjait.

- Nyissa meg a *.csproj* fájlt. Adjon hozzá egy `UserSecretsId` elemet az alább látható módon, és cserélje le a saját, az értékét, amely általában egy GUID Azonosítót. Mentse a fájlt.

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

## <a name="connect-to-app-configuration-store"></a>Csatlakozhat az alkalmazás a konfigurációs adattároló

1. Adjon hozzá egy, a `Microsoft.Extensions.Configuration.AzureAppConfiguration` NuGet-csomagra mutató hivatkozást a következő parancs futtatásával:

        dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration

2. A projekt csomagjainak visszaállításához futtassa a következő parancsot.

        dotnet restore

3. Adja hozzá a titkos kulcs nevű *ConnectionStrings:AppConfig* Secret Manager.

    A titkos kód a kapcsolati karakterláncot a konfigurációs alkalmazásáruház elérésére szolgáló fogja tartalmazni. Az érték az alábbi parancsban cserélje le a kapcsolati karakterláncát az alkalmazás a konfigurációs adattároló.

    Ezt a parancsot abban a könyvtárban kell végrehajtani, ahol a *.csproj* fájl található.

        dotnet user-secrets set ConnectionStrings:AppConfig "Endpoint=<your_endpoint>;Id=<your_id>;Secret=<your_secret>"

    SECRET Manager csak a webes alkalmazás helyi teszteléséhez használható. Amikor az alkalmazás telepítve van (például, hogy [Azure App Service](https://azure.microsoft.com/services/app-service/web)), használhat alkalmazás-beállítás (például **kapcsolati karakterláncok** App Service-ben) helyett a titkos kulcsot a kapcsolati karakterlánc Kezelő.

    A titkos kód egy elérhető API-konfigurációval. A konfigurációs API-val a konfigurációk nevében használható a kettőspont (:) az összes támogatott platformon. Lásd [az egyes környezetek konfigurációs lehetőségeit](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0) bemutató cikket.

4. Nyissa meg *Program.cs* és frissítheti a `CreateWebHostBuilder` metódus meghívásával használhatja az Alkalmazáskonfigurációt az `config.AddAzureAppConfiguration()` metódust.

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

5. Nyissa meg *Index.cshtml* a a *nézetek* > *kezdőlap* könyvtárat, és cserélje le annak tartalmát az alábbira:

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

6. Nyissa meg *_Layout.cshtml* a a *nézetek* > *megosztott* könyvtárat, és cserélje le annak tartalmát az alábbira:

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

1. Ha az alkalmazást a .NET Core CLI használatával szeretné létrehozni, futtassa a következő parancsot a parancsfelületen:

        dotnet build

2. Ha a létrehozás sikeresen befejeződött, hajtsa végre a következő parancsot a webalkalmazás helyben való futtatásához:

        dotnet run

3. Egy böngészőablakot, és navigáljon a `http://localhost:5000`, azaz a helyileg üzemeltetett webes alkalmazás alapértelmezett URL-CÍMÉT.

    ![A rövid útmutató alkalmazások indítása helyi](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ez a rövid útmutatóban egy új alkalmazás-konfigurációs adattároló létrehozta és az ASP.NET Core-webalkalmazás használta azt. Alkalmazáskonfiguráció használatával kapcsolatos további információkért folytassa a következő oktatóanyag azt mutatja be a hitelesítést.

> [!div class="nextstepaction"]
> [Az Azure-erőforrások integráció felügyelt identitásokból](./integrate-azure-managed-service-identity.md)
