---
title: Linux ASP.NET Core alkalmazások konfigurálása
description: Ismerje meg, hogyan konfigurálhat egy előre elkészített ASP.NET Core tárolót az alkalmazáshoz. Ez a cikk a leggyakoribb konfigurációs feladatokat mutatja be.
ms.devlang: dotnet
ms.topic: article
ms.date: 08/13/2019
ms.openlocfilehash: b1d9e59109f5ace25abb9840b48e44ff03d394e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255912"
---
# <a name="configure-a-linux-aspnet-core-app-for-azure-app-service"></a>Linux ASP.NET Core alkalmazás konfigurálása az Azure App Service-hez

ASP.NET Core alkalmazásokat lefordított bináris fájlként kell telepíteni. A Visual Studio közzétételi eszköz létrehozza a megoldást, majd közvetlenül telepíti a lefordított bináris fájlokat, míg az App Service központi telepítési motorja először telepíti a kódtárházat, majd lefordítja a bináris fájlokat.

Ez az útmutató kulcsfontosságú fogalmakat és utasításokat tartalmaz ASP.NET Core fejlesztők számára, akik beépített Linux-tárolót használnak az App Service-ben. Ha még soha nem használta az Azure App Service-t, kövesse a [ASP.NET Core rövid útmutatót,](quickstart-dotnetcore.md) és [ASP.NET Core-t az SQL Database oktatóanyaggal.](tutorial-dotnetcore-sqldb-app.md)

## <a name="show-net-core-version"></a>A .NET Core verzió megjelenítése

Az aktuális .NET Core verzió megjelenítéséhez futtassa a következő parancsot a [Cloud Shell](https://shell.azure.com)ben:

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Az összes támogatott .NET Core verzió megjelenítéséhez futtassa a következő parancsot a [Cloud Shell](https://shell.azure.com)ben:

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

## <a name="set-net-core-version"></a>A .NET Core verzió beállítása

A [.NET](https://shell.azure.com) Core verzió 2.1-es beállításához futtassa a következő parancsot a Cloud Shellben:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|2.1"
```

## <a name="customize-build-automation"></a>Build-automatizálás testreszabása

Ha git- vagy zip-csomagok használatával telepíti az alkalmazást, és be van kapcsolva az alkalmazás, az App Service a következő sorrendben építi fel az automatizálási lépéseket:

1. Ha a program ezt `PRE_BUILD_SCRIPT_PATH`a.
1. Futtassa `dotnet restore` a NuGet-függőségek visszaállításához.
1. Futtassa `dotnet publish` a bináris rendszer létrehozásához.
1. Ha a program ezt `POST_BUILD_SCRIPT_PATH`a.

`PRE_BUILD_COMMAND`és `POST_BUILD_COMMAND` olyan környezeti változók, amelyek alapértelmezés szerint üresek. Az előbuild parancsok futtatásához definiálja a programot. `PRE_BUILD_COMMAND` A létrehozás utáni parancsok `POST_BUILD_COMMAND`futtatásához definiálja a programot.

A következő példa a két változót egy parancssorozatra adja meg, vesszővel elválasztva.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

A buildautomatizálás testreszabásához további környezeti változókat az [Oryx konfigurációja](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)című témakörben tava látható.

Az App Service windowsos ASP.NET alkalmazások futásáról és felépítéséről az [Oryx dokumentációja: A .NET Core alkalmazások észlelése és létrehozása](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/dotnetcore.md)című témakörben olvashat bővebben.

## <a name="access-environment-variables"></a>Hozzáférés a környezeti változókhoz

Az App Service-ben az [alkalmazáskódokon kívül is megadhat alkalmazásbeállításokat.](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) Ezután elérheti őket bármely osztályban a standard ASP.NET Core függőségi injekciós minta használatával:

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
            // retrieve App Service app setting
            var myAppSetting = _configuration["MySetting"];
            // retrieve App Service connection string
            var myConnString = _configuration.GetConnectionString("MyDbConnection");
        }
    }
}
```

Ha például az App Service-ben és az *appsettings.json*ban azonos nevű alkalmazásbeállítást állít be, az App Service-érték elsőbbséget élvez az *appsettings.json* értékkel szemben. A helyi *appsettings.json* érték lehetővé teszi az alkalmazás helyi hibakeresését, de az App Service-érték lehetővé teszi az alkalmazás futtatását a termékben az éles beállításokkal. A kapcsolati karakterláncok ugyanúgy működnek. Ily módon az alkalmazás titkos kulcsokat a kódtárházon kívül, és a kód módosítása nélkül érheti el a megfelelő értékeket.

## <a name="get-detailed-exceptions-page"></a>Részletes kivételek lap

Amikor a ASP.NET alkalmazás kivételt hoz létre a Visual Studio hibakeresőjében, a böngésző egy részletes kivételoldalt jelenít meg, de az App Service-ben ezt a lapot egy általános **HTTP 500-as** hiba váltja fel, vagy **hiba történt a kérés feldolgozása közben.** üzenetet küldi vissza. Ha meg szeretné jeleníteni a részletes `ASPNETCORE_ENVIRONMENT` kivételoldalt az App Service-ben, adja hozzá az alkalmazásbeállítást az alkalmazáshoz a következő parancs futtatásával a <a target="_blank" href="https://shell.azure.com" >Cloud Shellben.</a>

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>HTTPS-munkamenet észlelése

Az App Service-ben [az SSL-végződtetés](https://wikipedia.org/wiki/TLS_termination_proxy) a hálózati terheléselosztóknál történik, így minden HTTPS-kérelem titkosítatlan HTTP-kérelemként éri el az alkalmazást. Ha az alkalmazás logikájának tudnia kell, hogy a felhasználói kérelmek titkosítva vannak-e vagy sem, konfigurálja a továbbított fejlécek köztes szoftverét *Startup.cs:*

- Konfigurálja a middleware-t [forwardedHeadersOptions kapcsolóval](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) a és `X-Forwarded-For` `X-Forwarded-Proto` a fejlécek továbbítására a alkalmazásban. `Startup.ConfigureServices`
- Adjon hozzá privát IP-címtartományokat az ismert hálózatokhoz, hogy a köztes szoftver megbízhat az App Service terheléselosztójában.
- Hívja meg a [UseForwardedHeaders metódust,](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) `Startup.Configure` mielőtt más köztes szoftvereket hívna meg.

A kód a következő példához hasonlóan néz ki:

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

További információ: [ASP.NET Core konfigurálása a proxykiszolgálókkal és terheléselosztókkal való együttműködésre.](https://docs.microsoft.com/aspnet/core/host-and-deploy/proxy-load-balancer)

## <a name="deploy-multi-project-solutions"></a>Többprojektes megoldások üzembe helyezése

Amikor egy ASP.NET-tárházat telepít a központi telepítési motorra egy *.csproj* fájllal a gyökérkönyvtárban, a motor telepíti a projektet. Amikor egy *.sln* fájlt tartalmazó ASP.NET tárházat telepít a gyökérkönyvtárban, a motor az első olyan webhelyet vagy webalkalmazás-projektet választja, amelyet Az App Service alkalmazásként talál. Lehetséges, hogy a motor nem válassza ki a kívánt projektet.

Többprojektes megoldás üzembe helyezéséhez két különböző módon adhatja meg az App Service-ben használandó projektet:

### <a name="using-deployment-file"></a>A .deployment fájl használata

Vegyen fel egy *.deployment* fájlt a tárház gyökeréhez, és adja hozzá a következő kódot:

```
[config]
project = <project-name>/<project-name>.csproj
```

### <a name="using-app-settings"></a>Az alkalmazásbeállítások használata

Az <a target="_blank" href="https://shell.azure.com">Azure Cloud Shellben</a>adjon hozzá egy alkalmazásbeállítást az App Service-alkalmazáshoz a következő CLI-parancs futtatásával. Cserélje le * \<az alkalmazásnév>*, * \<az erőforráscsoport-név>* és * \<* a projektnév>a megfelelő értékekre.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>Diagnosztikai naplók elérése

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>SSH-munkamenet megnyitása a böngészőben

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: ASP.NET Core alkalmazás SQL-adatbázissal](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Az App Service Linux – gyakori kérdések](app-service-linux-faq.md)
