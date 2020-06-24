---
title: Windows ASP.NET Core-alkalmazások konfigurálása
description: Megtudhatja, hogyan konfigurálhat egy ASP.NET Core alkalmazást a App Service natív Windows-példányaiban. Ez a cikk a leggyakoribb konfigurációs feladatokat ismerteti.
ms.devlang: dotnet
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 5819fc5b2d6e64d1812dacd88a2a4f840f6e03c5
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2020
ms.locfileid: "84908107"
---
# <a name="configure-a-windows-aspnet-core-app-for-azure-app-service"></a>Windows ASP.NET Core-alkalmazás konfigurálása Azure App Servicehoz

> [!NOTE]
> A .NET-keretrendszer ASP.NET tekintse meg a [ASP.NET alkalmazás konfigurálása a Azure app Servicehoz](configure-language-dotnet-framework.md) című témakört.

ASP.NET Core alkalmazásokat lefordított bináris fájlként kell telepíteni Azure App Service. A Visual Studio közzétételi eszköze létrehozza a megoldást, majd közvetlenül telepíti a lefordított bináris fájlokat, míg a App Service üzembe helyezési motor először a kódrészletet telepíti, majd lefordítja a bináris fájlokat. A Linux-alkalmazásokkal kapcsolatos információkért lásd: [linux ASP.net Core alkalmazás konfigurálása Azure app Servicehoz](containers/configure-language-dotnetcore.md).

Ez az útmutató a ASP.NET Core fejlesztőknek szóló főbb fogalmakat és útmutatást tartalmazza. Ha még soha nem használta a Azure App Servicet, kövesse a [ASP.net](app-service-web-get-started-dotnet.md) rövid útmutatót, és [ASP.net Core az SQL Database oktatóanyaggal](app-service-web-tutorial-dotnetcore-sqldb.md) .

## <a name="show-supported-net-core-runtime-versions"></a>Támogatott .NET Core Runtime-verziók megjelenítése

App Service a Windows-példányok már telepítve vannak az összes támogatott .NET Core-verzióval. A .NET Core futtatókörnyezet és az SDK elérhető verzióinak megjelenítéséhez navigáljon a `https://<app-name>.scm.azurewebsites.net/DebugConsole` következő parancsra a böngészőalapú konzolon:

```azurecli-interactive
dotnet --info
```

## <a name="set-net-core-version"></a>A .NET Core verziójának beállítása

Állítsa be a célként megadott keretrendszert a ASP.NET Core projekthez tartozó Project-fájlban. További információ: [válassza ki a](https://docs.microsoft.com/dotnet/core/versions/selection) .net Core-ban használandó verziót a .net Core dokumentációjában.

## <a name="access-environment-variables"></a>Hozzáférés a környezeti változókhoz

App Service az [Alkalmazásbeállítások](configure-common.md#configure-app-settings) az alkalmazás kódján kívül is megadhatók. Ezt követően bármely osztályban elérheti őket a standard ASP.NET Core függőségi befecskendezési minta használatával:

```csharp
using Microsoft.Extensions.Configuration;

namespace SomeNamespace 
{
    public class SomeClass
    {
        private IConfiguration _configuration;
    
        public SomeClass(IConfiguration configuration)
        {
            _configuration = configuration;
        }
    
        public SomeMethod()
        {
            // retrieve nested App Service app setting
            var myHierarchicalConfig = _configuration["My:Hierarchical:Config:Data"];
            // retrieve App Service connection string
            var myConnString = _configuration.GetConnectionString("MyDbConnection");
        }
    }
}
```

Ha például a App Service és a *appsettings.js*megegyező nevű Alkalmazásbeállítás van konfigurálva, akkor az App Service érték elsőbbséget élvez az *appsettings.js* értéknél. A helyi *appsettings.json* Value lehetővé teszi az alkalmazás helyi hibakeresését, de a app Service érték lehetővé teszi az alkalmazás futtatását a termékben az éles beállításokkal. A kapcsolatok karakterláncai ugyanúgy működnek. Így megtarthatja az alkalmazási titkokat a Code repositoryn kívül, és a kód módosítása nélkül is elérheti a megfelelő értékeket.

> [!NOTE]
> Figyelje meg, hogy a *appsettings.json* lévő [hierarchikus konfigurációs adatai](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/#hierarchical-configuration-data) a `:` .net Core szabványnak megfelelő elválasztóval érhetők el. A App Service adott hierarchikus konfigurációs beállításainak felülbírálásához állítsa az Alkalmazásbeállítások nevét ugyanazzal a tagolt formátummal a kulcsban. a következő példát futtathatja a [Cloud Shellban](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings My:Hierarchical:Config:Data="some value"
```

## <a name="deploy-multi-project-solutions"></a>Több projektből álló megoldások üzembe helyezése

Ha egy Visual Studio-megoldás több projektet is tartalmaz, a Visual Studio közzétételi folyamata már tartalmazza a telepítendő projekt kiválasztását. Ha üzembe helyezi a App Service üzembe helyezési motort, például a git vagy a ZIP üzembe helyezését, a Build Automation bekapcsolva állapotba kerül, akkor a App Service üzembe helyezési motor kiválasztja az App Service alkalmazásként megtalált első webhelyt vagy webalkalmazás-projektet. Megadhatja, hogy melyik projektet App Service kell használnia az `PROJECT` alkalmazás beállításainak megadásával. Futtassa például a következőt a [Cloud Shellban](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>Diagnosztikai naplók elérése

A ASP.NET Core [beépített naplózási szolgáltatót biztosít a app Servicehoz](https://docs.microsoft.com/aspnet/core/fundamentals/logging/#azure-app-service). A projekt *program.cs* adja hozzá a szolgáltatót az alkalmazáshoz a `ConfigureLogging` kiterjesztési módszer segítségével, az alábbi példában látható módon:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureLogging(logging =>
        {
            logging.AddAzureWebAppDiagnostics();
        })
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

Ezután a [standard .net Core mintával](https://docs.microsoft.com/aspnet/core/fundamentals/logging)konfigurálhatja és létrehozhatja a naplókat.

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

A App Service ASP.NET Core alkalmazások hibaelhárításával kapcsolatos további információkért lásd: [a Azure app Service-és IIS-ASP.net Core](https://docs.microsoft.com/aspnet/core/test/troubleshoot-azure-iis) hibaelhárítása

## <a name="get-detailed-exceptions-page"></a>Részletes kivételek oldalának beolvasása

Ha a ASP.NET Core-alkalmazás kivételt hoz létre a Visual Studio debuggerben, a böngésző egy részletes kivételt jelenít meg, de a App Service a lapot általános **HTTP 500** -hiba váltja fel, vagy **hiba történt a kérelem feldolgozása során.** üzenetet küldi vissza. A App Service részletes kivétel lapjának megjelenítéséhez adja hozzá az alkalmazás `ASPNETCORE_ENVIRONMENT` beállításait az alkalmazáshoz a következő parancs futtatásával a <a target="_blank" href="https://shell.azure.com" >Cloud Shellban</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>HTTPS-munkamenet észlelése

App Service az [SSL-megszakítás](https://wikipedia.org/wiki/TLS_termination_proxy) a hálózati terheléselosztó esetében történik, így minden HTTPS-kérelem titkosítatlan http-kérésként éri el az alkalmazást. Ha az alkalmazás logikájának tudnia kell, hogy a felhasználói kérések titkosítva vannak-e, vagy sem, konfigurálja a továbbított fejlécek middleware-t a *Startup.cs*-ben:

- Konfigurálja a middleware-t a [ForwardedHeadersOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) , hogy továbbítsa a `X-Forwarded-For` és a `X-Forwarded-Proto` fejléceket a alkalmazásban `Startup.ConfigureServices` .
- Adjon hozzá magánhálózati IP-címtartományt az ismert hálózatokhoz, hogy a köztes kapcsolat megbízható legyen a App Service Load balancerben.
- A [UseForwardedHeaders](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) metódus meghívása az `Startup.Configure` egyéb köztes közbenső funkciók meghívása előtt.

A három elem együttes elhelyezésével a kód a következő példához hasonlóan néz ki:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders =
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:10.0.0.0"), 104));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:192.168.0.0"), 112));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:172.16.0.0"), 108));
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    ...

    app.UseMvc();
}
```

További információkért lásd: [a ASP.net Core konfigurálása a proxykiszolgáló és a terheléselosztó működéséhez](https://docs.microsoft.com/aspnet/core/host-and-deploy/proxy-load-balancer).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: ASP.NET Core alkalmazás SQL Database](app-service-web-tutorial-dotnetcore-sqldb.md)
