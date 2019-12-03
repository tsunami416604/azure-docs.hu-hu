---
title: 'Gyors útmutató: Linux ASP.NET Core-alkalmazás futtatása'
description: Ismerkedjen meg a Linux-alkalmazásokkal Azure App Service az első ASP.NET Core alkalmazás üzembe helyezésével a App Service Linux-tárolójában.
keywords: azure app service, webalkalmazás, dotnet, core, linux, oss
ms.assetid: c02959e6-7220-496a-a417-9b2147638e2e
ms.tgt_pltfrm: linux
ms.topic: quickstart
ms.date: 03/27/2019
ms.custom: seodec18
ms.openlocfilehash: ea9449a89bcfbdb1378f0cceb31a07a288661bf7
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74687559"
---
# <a name="create-an-aspnet-core-app-in-app-service-on-linux"></a>ASP.NET Core-alkalmazás létrehozása Linux rendszeren App Service

> [!NOTE]
> Ebben a cikkben egy alkalmazást helyezünk üzembe a Linuxon futó App Service-ben. Ha _Windows_rendszeren szeretné telepíteni a app Servicet, tekintse meg a [ASP.net Core alkalmazás létrehozása az Azure-ban](../app-service-web-get-started-dotnet.md)című témakört.
>

A [Linuxon futó App Service](app-service-linux-intro.md) hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatást nyújt a Linux operációs rendszer használatával. Ez a rövid útmutató bemutatja, hogyan hozhat létre [.NET Core](https://docs.microsoft.com/aspnet/core/)-alkalmazást Linuxon futó App Service-ben. Az alkalmazást az [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)-vel hozza létre, és a git használatával helyezi üzembe a .net Core-kódot az alkalmazásban.

![Az Azure-ban futó mintaalkalmazás](media/quickstart-dotnetcore/dotnet-browse-azure.png)

A cikk lépéseit Mac, Windows vagy Linux rendszert futtató gépen is követheti.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

A gyorsútmutató elvégzéséhez:

* <a href="https://git-scm.com/" target="_blank">A Git telepítése</a>
* <a href="https://www.microsoft.com/net/core/" target="_blank">A .NET Core telepítése</a>

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

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Webalkalmazás létrehozása

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-dotnetcore-linux-no-h.md)]

Tallózással keresse meg az újonnan létrehozott alkalmazást. Cserélje le _&lt;app-name >_ az alkalmazás nevére.

```bash
http://<app-name>.azurewebsites.net
```

Az új alkalmazásnak így kell kinéznie:

![Alkalmazás üres lapja](media/quickstart-dotnetcore/dotnet-browse-created.png)

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
To https://<app-name>.scm.azurewebsites.net/<app-name>.git
 * [new branch]      master -> master
```

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

Az üzembe helyezés befejezését követően váltson vissza **Az alkalmazás megkeresése tallózással** lépésben megnyitott böngészőablakra, és frissítse azt.

![Az Azure-ban futó frissített mintaalkalmazás](media/quickstart-dotnetcore/dotnet-browse-azure-updated.png)

## <a name="manage-your-new-azure-app"></a>Az új Azure-alkalmazás kezelése

A létrehozott alkalmazás kezeléséhez lépjen a <a href="https://portal.azure.com" target="_blank">Azure Portal</a> .

A bal oldali menüben kattintson a **app Services**elemre, majd kattintson az Azure-alkalmazás nevére.

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/quickstart-dotnetcore/portal-app-service-list.png)

Megtekintheti az alkalmazás áttekintés lapját. Itt elvégezhet olyan alapszintű felügyeleti feladatokat, mint a tallózás, leállítás, elindítás, újraindítás és törlés. 

![Az App Service lap az Azure Portalon](media/quickstart-dotnetcore/portal-app-overview.png)

A bal oldali menü az alkalmazás konfigurálásához biztosít különböző oldalakat. 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: ASP.NET Core alkalmazás SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [ASP.NET Core alkalmazás konfigurálása](configure-language-dotnetcore.md)
