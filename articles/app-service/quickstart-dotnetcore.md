---
title: 'Rövid útmutató: C# ASP.NET Core-alkalmazás létrehozása'
description: Megtudhatja, hogyan futtathat webalkalmazásokat Azure App Service az első ASP.NET Core-alkalmazás üzembe helyezésével.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 08/01/2020
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 5604153d31a2828bcefbeccbd2f4fea428e878b5
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2020
ms.locfileid: "88961533"
---
# <a name="quickstart-create-an-aspnet-core-web-app-in-azure"></a>Rövid útmutató: ASP.NET Core Webalkalmazás létrehozása az Azure-ban

::: zone pivot="platform-windows"  

Ebből a rövid útmutatóból megtudhatja, hogyan hozhatja létre és helyezheti üzembe az első ASP.NET Core webalkalmazását [Azure app Service](overview.md). 

Ha elkészült, egy App Service üzemeltetési csomagból és egy üzembe helyezett webalkalmazással rendelkező App Service álló Azure-erőforráscsoport lesz.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/dotnet/).
- Ez a rövid útmutató üzembe helyez egy alkalmazást a Windows App Service. A _linuxon_app Service telepítéséhez lásd: [.net Core-Webalkalmazás létrehozása a app Serviceban](./quickstart-dotnetcore.md).
- Telepítse a <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> alkalmazást a **ASP.net és a webes fejlesztési** munkaterheléssel.

  Ha már telepítette a Visual Studio 2019-et:

  - A **Help**  >  **frissítések keresése**lehetőség kiválasztásával telepítse a legújabb frissítéseket a Visual Studióban.
  - A számítási feladat hozzáadásához **válassza az eszközök**  >  **beolvasása eszközök és szolgáltatások**lehetőséget.


## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core-webalkalmazás létrehozása

Hozzon létre egy ASP.NET Core webalkalmazást a Visual Studióban a következő lépések végrehajtásával:

1. Nyissa meg a Visual studiót, és válassza **az új projekt létrehozása**lehetőséget.

1. Az **új projekt létrehozása**területen válassza ki **ASP.net Core webalkalmazás** elemet, és győződjön meg arról, hogy a **C#** szerepel a választott nyelveken, majd válassza a **tovább**lehetőséget.

1. Az **új projekt konfigurálása**lapon nevezze el a webalkalmazás-projekt *myFirstAzureWebApp*, és válassza a **Létrehozás**lehetőséget.

   ![A webalkalmazás-projekt konfigurálása](./media/quickstart-dotnetcore/configure-web-app-project.png)

1. Bármilyen típusú ASP.NET Core webalkalmazást üzembe helyezhet az Azure-ban, de ebben a rövid útmutatóban a **webalkalmazás** sablont is kiválaszthatja. Győződjön meg arról, hogy a **hitelesítés** **Nincs hitelesítés**, és nincs más lehetőség kiválasztva. Ezután kattintson a **Létrehozás** elemre.

   ![Új ASP.NET Core Webalkalmazás létrehozása](./media/quickstart-dotnetcore/create-aspnet-core-web-app.png) 
   
1. A Visual Studio menüjében válassza a **hibakeresés**  >  **Indítás hibakeresés nélkül** lehetőséget a webalkalmazás helyi futtatásához.

   ![Helyileg futó webalkalmazás](./media/quickstart-dotnetcore/web-app-running-locally.png)

## <a name="publish-your-web-app"></a>Webalkalmazás közzététele

A webalkalmazás közzétételéhez először létre kell hoznia és konfigurálnia kell egy új App Service, amelyre közzé teheti az alkalmazást. 

A App Service beállításának részeként a következőket fogja létrehozni:

- Egy új [erőforráscsoport](../azure-resource-manager/management/overview.md#terminology) , amely a szolgáltatáshoz tartozó összes Azure-erőforrást tartalmazza.
- Új [üzemeltetési csomag](./overview-hosting-plans.md) , amely meghatározza az alkalmazást futtató webkiszolgáló-Farm helyét, méretét és funkcióit.

Kövesse az alábbi lépéseket a App Service létrehozásához és a webalkalmazás közzétételéhez:

1. A **megoldáskezelő**kattintson a jobb gombbal a **myFirstAzureWebApp** projektre, és válassza a **Közzététel**lehetőséget. Ha még nem jelentkezett be Azure-fiókjába a Visual studióból, válassza **a fiók hozzáadása** vagy a **Bejelentkezés**lehetőséget. Létrehozhat egy ingyenes Azure-fiókot is.

1. A **Közzététel célhelyének** kiválasztása párbeszédpanelen válassza a **app Service**lehetőséget, válassza az **új létrehozása**lehetőséget, majd válassza a **profil létrehozása**lehetőséget.

   ![Közzétételi cél kiválasztása](./media/quickstart-dotnetcore/pick-publish-target-vs2019.png)

1. A **app Service: hozzon létre új** párbeszédpanelt, adjon meg egy globálisan egyedi **nevet** az alkalmazásnak az alapértelmezett név elfogadásával vagy egy új név megadásával. Az érvényes karakterek a következők: `a-z` ,, `A-Z` `0-9` és `-` . A rendszer ezt a **nevet** használja a webalkalmazáshoz tartozó URL-előtagként a következő formátumban: `http://<app_name>.azurewebsites.net` .

1. Az **előfizetés**mezőben fogadja el a felsorolt előfizetést, vagy válasszon ki egy újat a legördülő listából.

1. Az **erőforráscsoport**területen válassza az **új**lehetőséget. Az **Új erőforráscsoport neve**mezőbe írja be a *myResourceGroup* nevet, majd kattintson **az OK gombra**. 

1. **Üzemeltetési csomag**esetén válassza az **új**lehetőséget. 

1. A **üzemeltetési tervben: hozzon létre új** párbeszédpanelt, és adja meg a következő táblázatban megadott értékeket:

   | Beállítás  | Ajánlott érték | Leírás |
   | -------- | --------------- | ----------- |
   | **Szolgáltatási csomag**  | *myFirstAzureWebAppPlan* | Az App Service-csomag neve. |
   | **Hely**      | *Nyugat-Európa* | Az adatközpont, ahol a webalkalmazást üzemeltetik. |
   | **Méret**          | *Ingyenes* | A [tarifacsomag](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) meghatározza az üzemeltetési funkciókat. |
   
   ![Új üzemeltetési csomag létrehozása](./media/quickstart-dotnetcore/create-new-hosting-plan-vs2019.png)

1. Hagyja **Application Insights** beállítást a *none*értékre.

1. Az **app Service: új létrehozása** párbeszédpanelen válassza a **Létrehozás** lehetőséget az Azure-erőforrások létrehozásának megkezdéséhez.

   ![Új App Service létrehozása](./media/quickstart-dotnetcore/create-new-app-service-vs2019.png)

1. A varázsló befejezése után válassza a **Közzététel**lehetőséget.

   ![Webalkalmazás közzététele az Azure-ban](./media/quickstart-dotnetcore/publish-web-app-vs2019.png)

   A Visual Studio közzéteszi ASP.NET Core webalkalmazását az Azure-ban, és elindítja az alkalmazást az alapértelmezett böngészőben. 

   ![Az Azure-ban futó közzétett ASP.NET-webalkalmazás](./media/quickstart-dotnetcore/web-app-running-live.png)

**Gratulálunk!** A ASP.NET Core webalkalmazása Azure App Serviceon fut.

## <a name="update-the-app-and-redeploy"></a>Az alkalmazás frissítése és ismételt üzembe helyezése

A webalkalmazás frissítéséhez és újbóli üzembe helyezéséhez kövesse az alábbi lépéseket:

1. **Megoldáskezelő**a projekt alatt nyissa meg a **Pages**  >  **index. cshtml**lapot.

1. Cserélje le a teljes `<div>` címkét a következő kódra:

   ```html
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Az Azure-beli ismételt üzembe helyezéshez kattintson a jobb gombbal a **myFirstAzureWebApp** projektre a **Solution Explorer** (Megoldáskezelő) lapon, és válassza a **Publish** (Közzététel) elemet.

1. Az összefoglalás **közzététele** lapon válassza a **Közzététel**lehetőséget.

   ![Frissítés közzététele a webalkalmazásban](./media/quickstart-dotnetcore/publish-update-to-web-app-vs2019.png)

Miután a közzététel befejeződött, a Visual Studio tallózza a webalkalmazás URL-címét.

![Az Azure-ban futó ASP.NET-webalkalmazás frissítve](./media/quickstart-dotnetcore/updated-web-app-running-live.png)

## <a name="manage-the-azure-app"></a>Az Azure-alkalmazás kezelése

A webalkalmazás kezeléséhez nyissa meg a [Azure Portal](https://portal.azure.com), és keresse meg, majd válassza a **app Services**lehetőséget.

![App Services kiválasztása](./media/quickstart-dotnetcore/app-services.png)

A **app Services** lapon válassza ki a webalkalmazás nevét.

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/quickstart-dotnetcore/select-app-service.png)

A webalkalmazás **áttekintő** lapja olyan alapszintű felügyeleti lehetőségeket tartalmaz, mint a Tallózás, Leállítás, indítás, újraindítás és törlés. A bal oldali menü további lapokat biztosít az alkalmazás konfigurálásához.

![App Service a Azure Portal](./media/quickstart-dotnetcore/web-app-overview-page.png)

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban a Visual studiót használta egy ASP.NET Core webalkalmazás létrehozásához és üzembe helyezéséhez Azure App Service.

A következő cikkből megtudhatja, hogyan hozhat létre .NET Core-alkalmazást, és hogyan csatlakoztatható SQL Databasehoz:

> [!div class="nextstepaction"]
> [ASP.NET Core és SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [ASP.NET Core alkalmazás konfigurálása](configure-language-dotnetcore.md)

::: zone-end  

::: zone pivot="platform-linux"
A [linuxon app Service](overview.md#app-service-on-linux) a Linux operációs rendszert használó, jól méretezhető, önjavító webes üzemeltetési szolgáltatást nyújt. Ez a rövid útmutató bemutatja, hogyan hozhat létre [.NET Core](/aspnet/core/)-alkalmazást Linuxon futó App Service-ben. Az alkalmazást az [Azure CLI](/cli/azure/get-started-with-azure-cli)-vel hozza létre, és a git használatával helyezi üzembe a .net Core-kódot az alkalmazásban.

![Az Azure-ban futó mintaalkalmazás](media/quickstart-dotnetcore/dotnet-browse-azure.png)

A cikk lépéseit Mac, Windows vagy Linux rendszert futtató gépen is követheti.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

* <a href="https://git-scm.com/" target="_blank">A Git telepítése</a>
* <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">A legújabb .NET Core 3,1 SDK telepítése</a>

## <a name="create-the-app-locally"></a>Az alkalmazás helyi létrehozása

A gép egy terminálablakában hozzon létre egy `hellodotnetcore` nevű könyvtárat, és módosítsa erre a jelenlegi könyvtárat.

```bash
mkdir hellodotnetcore
cd hellodotnetcore
```

Hozzon létre egy új .NET Core-alkalmazást.

```bash
dotnet new web
```

## <a name="run-the-app-locally"></a>Az alkalmazás futtatása helyben

Futtassa helyileg az alkalmazást, hogy lássa, hogyan fog kinézni az Azure-ban üzembe helyezve. 

Állítsa vissza a NuGet-csomagokat, és futtassa az alkalmazást.

```bash
dotnet run
```

Nyisson meg egy webböngészőt, majd keresse fel az alkalmazást a következő címen: `http://localhost:5000`.

Az oldalon látható mintaalkalmazáson ekkor a **Hello World** üzenetnek kell megjelennie.

![Tesztelés böngészővel](media/quickstart-dotnetcore/dotnet-browse-local.png)

A terminálablakban nyomja le a **Ctrl+C** billentyűkombinációt a webkiszolgálóból történő kilépéshez. Inicializáljon egy Git-adattárat a .NET Core-projekthez.

```bash
git init
git add .
git commit -m "first commit"
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Webalkalmazás létrehozása

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-linux-no-h.md)]

Tallózással keresse meg az újonnan létrehozott alkalmazást. Cserélje le az _ &lt; app-Name>_ az alkalmazás nevére.

```bash
https://<app-name>.azurewebsites.net
```

Az új alkalmazásnak így kell kinéznie:

![Alkalmazás üres lapja](media/quickstart-dotnetcore/dotnet-browse-created.png)

[!INCLUDE [Push to Azure](../../includes/app-service-web-git-push-to-azure.md)] 

<pre>
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 285 bytes | 95.00 KiB/s, done.
Total 3 (delta 2), reused 0 (delta 0), pack-reused 0
remote: Deploy Async
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'd6b54472f7'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
remote:
remote: Oryx Version      : 0.2.20200114.13, Commit: 204922f30f8e8d41f5241b8c218425ef89106d1d, ReleaseTagName: 20200114.13
remote: Build Operation ID: |imoMY2y77/s=.40ca2a87_
remote: Repository Commit : d6b54472f7e8e9fd885ffafaa64522e74cf370e1
.
.
.
remote: Deployment successful.
remote: Deployment Logs : 'https://&lt;app-name&gt;.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/d6b54472f7e8e9fd885ffafaa64522e74cf370e1/log'
To https://&lt;app-name&gt;.scm.azurewebsites.net:443/&lt;app-name&gt;.git
   d87e6ca..d6b5447  master -> master
</pre>

## <a name="browse-to-the-app"></a>Az alkalmazás megkeresése tallózással

Tallózással keresse meg az üzembe helyezett alkalmazást a webböngésző használatával.

```bash
http://<app_name>.azurewebsites.net
```

A .NET Core mintakód App Service Linux rendszeren fut, beépített képpel.

![Az Azure-ban futó mintaalkalmazás](media/quickstart-dotnetcore/dotnet-browse-azure.png)

**Gratulálunk!** Elvégezte az első .NET Core-alkalmazás üzembe helyezését a Linuxon futó App Service-ben.

## <a name="update-and-redeploy-the-code"></a>A kód frissítése és ismételt üzembe helyezése

A helyi könyvtárban nyissa meg a _Startup.cs_ fájlt. Végezzen el egy kis módosítást a `context.Response.WriteAsync` metódushívás szövegében:

```csharp
await context.Response.WriteAsync("Hello Azure!");
```

Mentse a módosításokat a Gitben, majd továbbítsa a kód módosításait az Azure-ba.

```bash
git commit -am "updated output"
git push azure master
```

Az üzembe helyezés befejezése után váltson vissza a böngészőablakban megnyíló böngészőablakra, és kattintson a frissítés **gombra** .

![Az Azure-ban futó frissített mintaalkalmazás](media/quickstart-dotnetcore/dotnet-browse-azure-updated.png)

## <a name="manage-your-new-azure-app"></a>Az új Azure-alkalmazás kezelése

A létrehozott alkalmazás kezeléséhez lépjen a <a href="https://portal.azure.com" target="_blank">Azure Portal</a> .

A bal oldali menüben kattintson a **app Services**elemre, majd kattintson az Azure-alkalmazás nevére.

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/quickstart-dotnetcore/portal-app-service-list.png)

Megtekintheti az alkalmazás áttekintés lapját. Itt elvégezhet olyan alapszintű felügyeleti feladatokat, mint a tallózás, leállítás, elindítás, újraindítás és törlés. 

![Az App Service lap az Azure Portalon](media/quickstart-dotnetcore/portal-app-overview.png)

A bal oldali menü az alkalmazás konfigurálásához biztosít különböző oldalakat. 

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: ASP.NET Core alkalmazás SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [ASP.NET Core alkalmazás konfigurálása](configure-language-dotnetcore.md)

::: zone-end