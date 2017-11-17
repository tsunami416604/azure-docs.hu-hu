---
title: "A .NET Core-webalkalmazás létrehozása és telepítése az App Service Linux |} Microsoft Docs"
description: "Alkalmazás telepítése az első .NET Core Hello World App Service Linux perc múlva."
keywords: "az Azure app service, webalkalmazás, dotnet, mag, linux, oss"
services: app-service
documentationCenter: 
author: cephalin
manager: syntaxc4
editor: 
ms.assetid: c02959e6-7220-496a-a417-9b2147638e2e
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: linux
ms.devlang: na
ms.topic: quickstart
ms.date: 08/30/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 1638c652c3508e9bc0e6ea95545d07a71d9e1b97
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2017
---
# <a name="create-a-net-core-web-app-in-app-service-on-linux"></a>A .NET Core-webalkalmazás létrehozása az App Service Linux rendszeren

[App Service Linux](app-service-linux-intro.md) biztosít egy jól skálázható, önálló javítási webhelyszolgáltató a Linux operációs rendszert használ. A gyors üzembe helyezés bemutatja, hogyan hozhat létre egy [.NET Core](https://docs.microsoft.com/aspnet/core/) alkalmazást az App Service Linux rendszeren. A webes alkalmazás használata a [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli), és a Git használatával telepíti a .NET Core kód a webalkalmazásban.

![Az Azure-ban futó mintaalkalmazás](media/quickstart-dotnetcore/dotnet-browse-azure.png)

Az alábbi lépéseket Mac, Windows vagy Linux rendszert futtató gépen is követheti.

## <a name="prerequisites"></a>Előfeltételek

A gyorsútmutató elvégzéséhez:

* [A Git telepítése](https://git-scm.com/)
* [Telepítse a .NET SDK](https://www.microsoft.com/net/download/core)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-app-locally"></a>Az alkalmazás helyileg létrehozása

Egy terminálablakot a gépen, hozzon létre egy nevű könyvtár `hellodotnetcore` , és módosítsa azt az aktuális könyvtár.

```bash
md hellodotnetcore
cd hellodotnetcore
```

Új a .NET Core-webalkalmazás létrehozása.

```bash
dotnet new web
```

## <a name="run-the-app-locally"></a>Az alkalmazás futtatása helyben

Állítsa vissza a NuGet-csomagok, és futtassa az alkalmazást.

```bash
dotnet restore
dotnet run
```

Nyisson meg egy webböngészőt, és keresse meg az alkalmazást `http://localhost:5000`.

Az oldalon látható mintaalkalmazáson ekkor a **Hello World** üzenetnek kell megjelennie.

![A böngésző tesztelése](media/quickstart-dotnetcore/dotnet-browse-local.png)

A terminálablakban nyomja le a **Ctrl+C** billentyűkombinációt a webkiszolgálóból történő kilépéshez. A .NET Core projekt egy Git-tárház inicializálása.

```bash
git init
git add .
git commit -m "first commit"
```

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Webalkalmazás létrehozása

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-dotnetcore-no-h.md)]

Keresse meg az újonnan létrehozott webalkalmazáshoz. Cserélje le  _&lt;alkalmazás neve >_ és a webes alkalmazás neve.

```bash
http://<app name>.azurewebsites.net
```

![Üres webalkalmazás oldal](media/quickstart-dotnetcore/dotnet-browse-created.png)

[!INCLUDE [Push to Azure](../../../includes/app-service-web-git-push-to-azure.md)] 

```bash
Counting objects: 22, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (18/18), done.
Writing objects: 100% (22/22), 51.21 KiB | 3.94 MiB/s, done.
Total 22 (delta 1), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '741f16d1db'.
remote: Generating deployment script.
remote: Project file path: ./hellodotnetcore.csproj
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment.
remote: ...............................................................................................
remote:   Restoring packages for /home/site/repository/hellodotnetcore.csproj...
remote: ....................................
remote:   Installing System.Xml.XPath 4.0.1.
remote:   Installing System.Diagnostics.Tracing 4.1.0.
remote:   Installing System.Threading.Tasks.Extensions 4.0.0.
remote:   Installing System.Reflection.Emit.ILGeneration 4.0.1.
remote:   ...
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://cephalin-dotnetcore.scm.azurewebsites.net/cephalin-dotnetcore.git
 * [new branch]      master -> master
```

## <a name="browse-to-the-app"></a>Az alkalmazás megkeresése tallózással

Tallózással keresse meg az üzembe helyezett alkalmazást a webböngésző használatával.

```bash
http://<app_name>.azurewebsites.net
```

A Node.js mintakód egy webes alkalmazás beépített kép fut.

![Az Azure-ban futó mintaalkalmazás](media/quickstart-dotnetcore/dotnet-browse-azure.png)

**Gratulálunk!** Az első Node.js-alkalmazás az App Service Linux telepítése.

## <a name="update-and-redeploy-the-code"></a>A kód frissítése és ismételt üzembe helyezése

A helyi címtárban, nyissa meg a _Startup.cs_ fájlt. Kis módosítja a metódus hívása a szöveggé `context.Response.WriteAsync`:

```csharp
await context.Response.WriteAsync("Hello Azure!");
```

Mentse a módosításokat a Gitben, majd továbbítsa a kód módosításait az Azure-ba.

```bash
git commit -am "updated output"
git push azure master
```

Az üzembe helyezés befejezését követően váltson vissza **Az alkalmazás megkeresése tallózással** lépésben megnyitott böngészőablakra, és frissítse azt.

![Az Azure-ban futó frissített mintaalkalmazás](media/quickstart-dotnetcore/dotnet-browse-azure-updated.png)

## <a name="manage-your-new-azure-web-app"></a>Az új Azure-webapp kezelése

A létrehozott webalkalmazás felügyeletéhez ugorjon az <a href="https://portal.azure.com" target="_blank">Azure Portalra</a>.

A bal oldali menüben kattintson az **App Services** lehetőségre, majd az Azure-webalkalmazás nevére.

![Navigálás a portálon az Azure-webapphoz](./media/quickstart-dotnetcore/portal-app-service-list.png)

Megtekintheti a webalkalmazás Áttekintés oldalát. Itt elvégezhet olyan alapszintű felügyeleti feladatokat, mint a tallózás, leállítás, elindítás, újraindítás és törlés. 

![Az App Service lap az Azure Portalon](media/quickstart-dotnetcore/portal-app-overview.png)

A bal oldali menü az alkalmazás konfigurálásához biztosít különböző oldalakat. 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [A .NET Core és az SQL-adatbázis webalkalmazás az Azure App Service létrehozása Linux-kiszolgálón](tutorial-dotnetcore-sqldb-app.md)
