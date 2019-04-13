---
title: Az ASP.NET Core-alkalmazások – az Azure App Service konfigurálása |} A Microsoft Docs
description: Ismerje meg, hogyan működik az Azure App Service-ben az ASP.NET Core-alkalmazások konfigurálása
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnock
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2019
ms.author: cephalin
ms.openlocfilehash: e203877b2bc939c1d7fb9390df39f3e2451d12d3
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59551112"
---
# <a name="configure-a-linux-aspnet-core-app-for-azure-app-service"></a>Linux konfigurálása az Azure App Service az ASP.NET Core-alkalmazás

ASP.NET Core-alkalmazások lefordított bináris fájljai, telepíteni kell. A Visual Studio közzétételi eszköz követően felépíti a megoldást, és majd üzembe helyezi a lefordított bináris fájlokat közvetlenül, mivel az App Service üzembe helyezési motorban először üzembe helyezi a kódtárat, és ezután lefordítja a bináris fájlokat.

Ez az útmutató alapfogalmakat és útmutatást nyújt az ASP.NET Core App Service-ben egy beépített Linux-tárolót használó fejlesztők számára. Ha korábban nem használta az Azure App Service, kövesse a [ASP.NET Core rövid](quickstart-dotnetcore.md) és [ASP.NET Core és SQL Database-oktatóanyag](tutorial-dotnetcore-sqldb-app.md) első.

## <a name="show-net-core-version"></a>.NET Core-verzió megjelenítése

A jelenlegi verzió a .NET Core megjelenítéséhez futtassa a következő parancsot a [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Minden támogatott .NET Core-verziók megjelenítéséhez futtassa a következő parancsot a [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

## <a name="set-net-core-version"></a>.NET Core-verzió beállítása

Futtassa a következő parancsot a [Cloud Shell](https://shell.azure.com) , 2.1-es verzióját szeretné a .NET Core-verzió:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|2.1"
```

## <a name="access-environment-variables"></a>Hozzáférés a környezeti változókhoz

Az App Service-ben is [állítsa be az alkalmazásbeállításokat](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings) kívül a kódját. Ezután elérheti azokat az ASP.NET szokásos mintájának használatával:

```csharp
include Microsoft.Extensions.Configuration;
// retrieve App Service app setting
System.Configuration.ConfigurationManager.AppSettings["MySetting"]
// retrieve App Service connection string
Configuration.GetConnectionString("MyDbConnection")
```

Ha ilyen nevű App Service-ben és a egy alkalmazásbeállításhoz konfigurálja *Web.config*, az App Service-ben elsőbbséget élvez a Web.config érték. A Web.config érték lehetővé teszi az alkalmazás helyi hibakeresése, de az App Service-ben érték lehetővé teszi, hogy a Futtatás éles beállításokkal termékben az alkalmazást. Kapcsolati karakterláncok ugyanúgy működik. Így megtarthatja a titkos alkalmazáskulcsok kívül a kódtárat, és a kód módosítása nélkül férhetnek hozzá a megfelelő értékeket.

## <a name="get-detailed-exceptions-page"></a>Részletes kivételek lapját beolvasása

Ha az ASP.NET-alkalmazást a Visual Studio hibakereső funkcióját kivételt hoz létre, a böngésző egy részletes kivétel oldalát jeleníti meg, de az App Service lap váltotta fel egy általános **HTTP 500-as** hiba vagy **hiba történt, miközben a kérelem feldolgozása.** az üzenet. A részletes kivétel lap megjelenítéséhez az App Service-ben, adja hozzá a `ASPNETCORE_ENVIRONMENT` alkalmazásbeállítást az alkalmazásához a következő parancs futtatásával a <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>Észleli a HTTPS-KAPCSOLATON keresztül

Az App Service-ben [SSL-lezárást](https://wikipedia.org/wiki/TLS_termination_proxy) történik, ha a hálózati terheléselosztók, így az összes HTTPS-kérelmek elérni az alkalmazás nem titkosított HTTP-kérések. Ha az alkalmazáslogika tudnia kell, ha a felhasználó kér vagy nem titkosítottak, konfigurálja a továbbított fejlécek közbenső szoftver az *Startup.cs*:

- Az a közbenső szoftver konfigurálása [ForwardedHeadersOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) továbbítsa a `X-Forwarded-For` és `X-Forwarded-Proto` a fejlécek `Startup.ConfigureServices`.
- Adja hozzá a magánhálózati IP-címtartományok a ismert hálózatok úgy, hogy az a közbenső szoftver az App Service-ben load balancer megbízható.
- Meghívása a [UseForwardedHeaders](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) metódus az `Startup.Configure` más middlewares hívása előtt.

Az összes három elem bármik lehetnek, a kód hasonlít az alábbi példában:

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

További információkért lásd: [konfigurálhatja az ASP.NET Core proxykiszolgálók és terheléselosztók](https://docs.microsoft.com/aspnet/core/host-and-deploy/proxy-load-balancer).

## <a name="deploy-multi-project-solutions"></a>Több projektet megoldások üzembe helyezése

Telepítésekor meg kell adnia egy ASP.NET-tárházban, a telepítési motor az egy *.csproj* fájlt a gyökérkönyvtárban, az összetevő telepíti a projektet. Amikor telepít egy ASP.NET-tárunkat, ahol egy *.sln* fájlt a gyökérkönyvtárban a motor kiválasztja az első webhely vagy webalkalmazás projekt úgy találja, mint az App Service-alkalmazás. A motor nem válassza ki a kívánt projekt lehetőség.

Megoldás üzembe helyezése több projektet, adja meg a projektet az App Service-ben két különböző módon használhatja:

### <a name="using-deployment-file"></a>.Deployment fájl használatával

Adjon hozzá egy *.deployment* fájlt az adattár gyökérkönyvtárában, és adja hozzá a következő kódot:

```
[config]
project = <project-name>/<project-name>.csproj
```

### <a name="using-app-settings"></a>Alkalmazásbeállítások használatával

Az a <a target="_blank" href="https://shell.azure.com">Azure Cloud Shell</a>, az Alkalmazásbeállítás hozzáadása az App Service-alkalmazást a következő CLI-parancs futtatásával. Cserélje le  *\<alkalmazás-neve >*,  *\<erőforrás-csoport-neve >*, és  *\<projekt-neve >* a megfelelő értékekkel .

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>Diagnosztikai naplók elérése

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Nyissa meg böngészőben SSH-munkamenet

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Az ASP.NET Core-alkalmazás és SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Az App Service Linux – gyakori kérdések](app-service-linux-faq.md)