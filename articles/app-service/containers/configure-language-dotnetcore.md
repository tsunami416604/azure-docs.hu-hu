---
title: Linux ASP.NET Core-alkalmazások konfigurálása
description: Megtudhatja, hogyan konfigurálhat egy előre elkészített ASP.NET Core tárolót az alkalmazáshoz. Ez a cikk a leggyakoribb konfigurációs feladatokat ismerteti.
ms.devlang: dotnet
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: e009f5b1fc656f700b3f0e76dda6e545aed535d2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84905765"
---
# <a name="configure-a-linux-aspnet-core-app-for-azure-app-service"></a>Linux ASP.NET Core-alkalmazás konfigurálása Azure App Servicehoz

ASP.NET Core alkalmazásokat lefordított bináris fájlként kell telepíteni. A Visual Studio közzétételi eszköze létrehozza a megoldást, majd közvetlenül telepíti a lefordított bináris fájlokat, míg a App Service üzembe helyezési motor először a kódrészletet telepíti, majd lefordítja a bináris fájlokat.

Ez az útmutató a App Service beépített Linux-tárolóját használó ASP.NET Core fejlesztők számára biztosít főbb fogalmakat és útmutatást. Ha még soha nem használta a Azure App Servicet, először kövesse a [ASP.net Core](quickstart-dotnetcore.md) rövid útmutató és a [ASP.net Core SQL Database oktatóanyagot](tutorial-dotnetcore-sqldb-app.md) .

## <a name="show-net-core-version"></a>.NET Core verzió megjelenítése

Az aktuális .NET Core-verzió megjelenítéséhez futtassa a következő parancsot a [Cloud Shellban](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Az összes támogatott .NET Core-verzió megjelenítéséhez futtassa a következő parancsot a [Cloud Shellban](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

## <a name="set-net-core-version"></a>A .NET Core verziójának beállítása

Futtassa a következő parancsot a [Cloud Shell](https://shell.azure.com) a .net Core verzió 2,1-re való beállításához:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|2.1"
```

## <a name="customize-build-automation"></a>A Build Automation testreszabása

Ha a Build Automation használatával git vagy zip csomagok segítségével helyezi üzembe az alkalmazást, akkor a App Service az alábbi lépésekkel hozhat létre automatizálási lépéseket:

1. Futtassa az egyéni parancsfájlt, ha a meg van adva `PRE_BUILD_SCRIPT_PATH` .
1. Futtassa `dotnet restore` a parancsot a NuGet-függőségek visszaállításához.
1. A futtatásával `dotnet publish` hozzon létre egy bináris fájlt éles környezetben.
1. Futtassa az egyéni parancsfájlt, ha a meg van adva `POST_BUILD_SCRIPT_PATH` .

`PRE_BUILD_COMMAND``POST_BUILD_COMMAND`a és a környezeti változók, amelyek alapértelmezés szerint üresek. Az előkészítő parancsok futtatásához adja meg a következőt: `PRE_BUILD_COMMAND` . A létrehozás utáni parancsok futtatásához adja meg a következőt: `POST_BUILD_COMMAND` .

A következő példa a két változót adja meg egy több parancshoz, vesszővel elválasztva.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

További környezeti változók a Build Automation testreszabásához: [Oryx-konfiguráció](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

A ASP.NET Core alkalmazások Linux rendszeren való futtatásáról és App Serviceáról további információt a [Oryx dokumentációjában talál: a .net Core-alkalmazások észlelése és építése](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/dotnetcore.md).

## <a name="access-environment-variables"></a>Hozzáférés a környezeti változókhoz

App Service az [Alkalmazásbeállítások](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) az alkalmazás kódján kívül is megadhatók. Ezt követően bármely osztályban elérheti őket a standard ASP.NET Core függőségi befecskendezési minta használatával:

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

## <a name="get-detailed-exceptions-page"></a>Részletes kivételek oldalának beolvasása

Ha a ASP.NET-alkalmazás kivételt hoz létre a Visual Studio debuggerben, a böngésző egy részletes kivétel lapot jelenít meg, de App Service a lapot általános **HTTP 500** -hiba váltja fel, vagy **hiba történt a kérelem feldolgozása során.** üzenetet küldi vissza. A App Service részletes kivétel lapjának megjelenítéséhez adja hozzá az alkalmazás `ASPNETCORE_ENVIRONMENT` beállításait az alkalmazáshoz a következő parancs futtatásával a <a target="_blank" href="https://shell.azure.com" >Cloud Shellban</a>.

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

## <a name="deploy-multi-project-solutions"></a>Több projektből álló megoldások üzembe helyezése

Amikor egy ASP.NET-tárházat telepít a központi telepítési motorba egy *. csproj* fájllal a gyökérkönyvtárban, a motor üzembe helyezi a projektet. Ha egy ASP.NET-tárházat telepít egy *. SLN* fájllal a gyökérkönyvtárban, a motor kiválasztja az első webhelyt vagy webalkalmazás-projektet, amelyet app Service alkalmazásként talál. Lehetséges, hogy a motor nem válassza ki a kívánt projektet.

Többprojektes megoldás üzembe helyezéséhez két különböző módon adhatja meg a App Service használandó projektet:

### <a name="using-deployment-file"></a>A. Deployment fájl használata

Adjon hozzá egy *. Deployment* fájlt az adattár gyökeréhez, és adja hozzá a következő kódot:

```
[config]
project = <project-name>/<project-name>.csproj
```

### <a name="using-app-settings"></a>Alkalmazásbeállítások használata

A <a target="_blank" href="https://shell.azure.com">Azure Cloud Shell</a>az alábbi CLI-parancs futtatásával adjon hozzá egy alkalmazást az App Service alkalmazáshoz. Cserélje *\<app-name>* le *\<resource-group-name>* a, a és a *\<project-name>* értéket a megfelelő értékekre.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PROJECT="<project-name>/<project-name>.csproj"
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

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-linux-no-h.md)]

A App Service ASP.NET Core alkalmazások hibaelhárításával kapcsolatos további információkért lásd: [a Azure app Service-és IIS-ASP.net Core](https://docs.microsoft.com/aspnet/core/test/troubleshoot-azure-iis) hibaelhárítása

## <a name="open-ssh-session-in-browser"></a>SSH-munkamenet megnyitása böngészőben

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: ASP.NET Core alkalmazás SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [App Service Linux – gyakori kérdések](app-service-linux-faq.md)
