---
title: Gyors útmutató az Azure-alkalmazások konfigurálásához a ASP.NET Corekal | Microsoft Docs
description: Hozzon létre egy ASP.NET Core alkalmazást az Azure-alkalmazás konfigurálásával, hogy központilag központosítsa egy ASP.NET Core alkalmazás alkalmazás-beállításainak tárolását és kezelését.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-csharp, contperfq1
ms.topic: quickstart
ms.date: 09/25/2020
ms.author: lcozzens
ms.openlocfilehash: dc886ae2e113b77a6c49219a61a905aaeeb7d2a5
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91372331"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>Gyors útmutató: ASP.NET Core-alkalmazás létrehozása az Azure-alkalmazás konfigurálásával

Ebben a rövid útmutatóban az Azure-alkalmazások konfigurációját fogja használni az ASP.NET Core alkalmazások beállításainak tárolásához és kezeléséhez. ASP.NET Core egyetlen, kulcs-érték alapú konfigurációs objektumot hoz létre, amely egy vagy több alkalmazás által megadott adatforrásból származó beállításokat használ. Ezeket az adatforrásokat *konfigurációs szolgáltatóknak*nevezzük. Mivel az alkalmazás konfigurációja .NET Core-ügyfele konfigurációs szolgáltatóként van implementálva, a szolgáltatás egy másik adatforráshoz hasonlóan jelenik meg.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/dotnet)
* [.NET Core SDK](https://dotnet.microsoft.com/download)

> [!TIP]
> A Azure Cloud Shell egy ingyenes, interaktív rendszerhéj, amellyel a cikkben ismertetett parancssori utasításokat futtathatja. A közös Azure-eszközök előre telepítve vannak, beleértve a .NET Core SDK. Ha bejelentkezett az Azure-előfizetésbe, indítsa el a [Azure Cloud shellt](https://shell.azure.com) a shell.Azure.com webhelyről. A Azure Cloud Shellről a [dokumentáció beolvasásával](../cloud-shell/overview.md) tájékozódhat bővebben

## <a name="create-an-app-configuration-store"></a>Alkalmazás-konfigurációs tároló létrehozása

[!INCLUDE[Azure App Configuration resource creation steps](../../includes/azure-app-configuration-create.md)]

7. **Operations**  >  **Configuration explorer**  >  **Create**  >  A következő kulcs-érték párok hozzáadásához válassza az Operations Configuration Explorer**kulcs-érték** létrehozása elemet:

    | Kulcs                                | Érték                               |
    |------------------------------------|-------------------------------------|
    | `TestApp:Settings:BackgroundColor` | *#FFF*                              |
    | `TestApp:Settings:FontColor`       | *#000*                              |
    | `TestApp:Settings:FontSize`        | *24*                                |
    | `TestApp:Settings:Message`         | *Adatok az Azure-alkalmazás konfigurációjától* |

    Most hagyja üresen a **címke** és a **tartalom típusát** . Kattintson az **Alkalmaz** gombra.

## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core-webalkalmazás létrehozása

Új ASP.NET Core MVC-projekt létrehozásához használja a [.net Core parancssori felületet (CLI)](/dotnet/core/tools) . A [Azure Cloud Shell](https://shell.azure.com) ezeket az eszközöket biztosítja Önnek. A Windows, a macOS és a Linux platformokon is elérhetők.

Futtassa a következő parancsot egy ASP.NET Core MVC-projekt létrehozásához egy új *TestAppConfig* mappában:

```dotnetcli
dotnet new mvc --no-https --output TestAppConfig
```

## <a name="add-secret-manager"></a>Secret Manager hozzáadása

A Secret Manager nevű eszköz bizalmas adatokat tárol a projekt fáján kívüli fejlesztési munkához. Ez a módszer megakadályozza, hogy véletlenül megossza az alkalmazás forráskódbeli titkos kódjait. A következő lépések végrehajtásával engedélyezheti a Secret Manager használatát a projektben:

#### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

Futtassa a következő parancsot a titkos kulcsok tárolásának engedélyezéséhez a projektben:

```dotnetcli
dotnet user-secrets init
```

A `UserSecretsId` rendszer hozzáadja a GUID azonosítót tartalmazó elemet a *. csproj* fájlhoz:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    
    <PropertyGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
        <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
    </PropertyGroup>

</Project>
```

#### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

1. Nyissa meg a *. csproj* fájlt.

1. Adjon hozzá egy `UserSecretsId` elemet a *. csproj* fájlhoz az itt látható módon. Használhatja ugyanazt a GUID azonosítót, vagy lecserélheti ezt az értéket a saját értékére is.

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
    
1. Mentse a *. csproj* fájlt.

---

> [!TIP]
> A Secret Managerrel kapcsolatos további információkért lásd: az [alkalmazás-titkok biztonságos tárolása a fejlesztésben ASP.net Core](/aspnet/core/security/app-secrets).

## <a name="connect-to-the-app-configuration-store"></a>Kapcsolódás az alkalmazás-konfigurációs tárolóhoz

1. Futtassa a következő parancsot egy [Microsoft. Azure. AppConfiguration. AspNetCore](https://www.nuget.org/packages/Microsoft.Azure.AppConfiguration.AspNetCore) NuGet-csomag hivatkozásának hozzáadásához:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

1. Futtassa a következő parancsot ugyanabban a könyvtárban, mint a *. csproj* fájlt. A parancs a Secret Manager használatával tárolja a nevű titkos kulcsot `ConnectionStrings:AppConfig` , amely az alkalmazás konfigurációs tárolójához tartozó kapcsolódási karakterláncot tárolja. Cserélje le a `<your_connection_string>` helyőrzőt az alkalmazás konfigurációs tárolójának összekapcsolási karakterláncára. A kapcsolati karakterláncot a Azure Portal **elérési kulcsa** alatt találja.

    ```dotnetcli
    dotnet user-secrets set ConnectionStrings:AppConfig "<your_connection_string>"
    ```

    > [!IMPORTANT]
    > Egyes rendszerhéjak levágja a kapcsolatok karakterláncát, kivéve, ha idézőjelek közé vannak zárva. Győződjön meg arról, hogy a `dotnet user-secrets` parancs kimenete a teljes kapcsolatok sztringjét jeleníti meg. Ha nem, futtassa újra a parancsot, és foglalja bele a kapcsolatok karakterláncát idézőjelek közé.

    A Secret Manager csak a webalkalmazás helyi tesztelésére szolgál. Ha az alkalmazás telepítve van a [Azure app Servicere](https://azure.microsoft.com/services/app-service/web), a Secret Manager helyett használja a app Service **a kapcsolódási karakterláncok alkalmazása beállítást** a kapcsolódási sztring tárolásához.

    Ezt a titkot a .NET Core konfigurációs API használatával érheti el. A kettőspont ( `:` ) a konfigurációs névvel együtt a konfigurációs API-val működik az összes támogatott platformon. További információ: [konfigurációs kulcsok és értékek](/aspnet/core/fundamentals/configuration#configuration-keys-and-values).

1. A *program.cs*-ben adjon hozzá egy hivatkozást a .net Core Configuration API-névtérhez:

    ```csharp
    using Microsoft.Extensions.Configuration;
    ```

1. Frissítse a `CreateWebHostBuilder` metódust az alkalmazás konfigurációjának használatára a metódus meghívásával `AddAzureAppConfiguration` .

    > [!IMPORTANT]
    > `CreateHostBuilder``CreateWebHostBuilder`a .net Core 3. x helyére kerül. Válassza ki a megfelelő szintaxist a környezet alapján.

    #### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration(config =>
                {
                    var settings = config.Build();
                    var connection = settings.GetConnectionString("AppConfig");
                    config.AddAzureAppConfiguration(connection);
                }).UseStartup<Startup>());
    ```

    #### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration(config =>
            {
                var settings = config.Build();
                var connection = settings.GetConnectionString("AppConfig");
                config.AddAzureAppConfiguration(connection);
            })
            .UseStartup<Startup>();
    ```

    ---

    Az előző módosítással az alkalmazás konfigurációjának [szolgáltatója](https://go.microsoft.com/fwlink/?linkid=2074664) regisztrálva van a .net Core konfigurációs API-val.

## <a name="read-from-the-app-configuration-store"></a>Olvasás az alkalmazás konfigurációs tárolójából

A következő lépések végrehajtásával olvashatja és megjelenítheti az alkalmazás konfigurációs tárolójában tárolt értékeket. A rendszer a .NET Core konfigurációs API-t használja az áruház eléréséhez. A kulcsok értékeinek megjelenítéséhez a borotva szintaxisa lesz használva.

Nyissa meg a * \<app root> /views/Home/index.cshtml*, és cserélje le a tartalmát a következő kódra:

```cshtml
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<style>
    body {
        background-color: @Configuration["TestApp:Settings:BackgroundColor"]
    }
    h1 {
        color: @Configuration["TestApp:Settings:FontColor"];
        font-size: @Configuration["TestApp:Settings:FontSize"]px;
    }
</style>

<h1>@Configuration["TestApp:Settings:Message"]</h1>
```

Az előző kódban az alkalmazás konfigurációs tárolójának kulcsait a következőképpen kell használni:

* A `TestApp:Settings:BackgroundColor` kulcs értékét a CSS tulajdonsághoz rendeli a rendszer `background-color` .
* A `TestApp:Settings:FontColor` kulcs értékét a CSS tulajdonsághoz rendeli a rendszer `color` .
* A `TestApp:Settings:FontSize` kulcs értékét a CSS tulajdonsághoz rendeli a rendszer `font-size` .
* A `TestApp:Settings:Message` kulcs értéke fejlécként jelenik meg.

## <a name="build-and-run-the-app-locally"></a>Az alkalmazás helyi létrehozása és futtatása

1. Ha az alkalmazást a a .NET Core parancssori felülete használatával szeretné felépíteni, navigáljon a projekt gyökérkönyvtárához. Futtassa a következő parancsot a parancsfelületen:

    ```dotnetcli
    dotnet build
    ```

1. A létrehozás sikeres befejezése után futtassa a következő parancsot a webalkalmazás helyi futtatásához:

    ```dotnetcli
    dotnet run
    ```

1. Ha a helyi gépen dolgozik, nyissa meg a böngészőt `http://localhost:5000` . Ez a cím a helyileg üzemeltetett webalkalmazás alapértelmezett URL-címe. Ha dolgozik a Azure Cloud Shellban, válassza a **webes előnézet** gombot, majd a **configure (Konfigurálás**) lehetőséget.

    ![A webes előnézet gomb megkeresése](./media/quickstarts/cloud-shell-web-preview.png)

    Amikor a rendszer kéri, hogy konfigurálja a portot az előzetes verzióhoz, írja be a *5000* értéket, és válassza a **Megnyitás és Tallózás**lehetőséget. A weblap beolvassa az "adatok az Azure-alkalmazás konfigurációjától" című lapot.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE[Azure App Configuration cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban a következőket hajtja végre:

* Új alkalmazás-konfigurációs tároló üzembe helyezése.
* Regisztrálta az alkalmazás konfigurációs tárolójának .NET Core-konfigurációs szolgáltatóját.
* Olvassa el az alkalmazás konfigurációs tárolójának kulcsait a konfigurációs szolgáltatónál.
* Megjeleníti az alkalmazás konfigurációs tárolójának kulcsának értékeit a borotva szintaxisának használatával.

Ha szeretné megtudni, hogyan konfigurálhatja a ASP.NET Core alkalmazást a konfigurációs beállítások dinamikus frissítéséhez, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Dinamikus konfiguráció engedélyezése](./enable-dynamic-configuration-aspnet-core.md)
