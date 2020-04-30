---
title: 'Gyors útmutató: Linux ASP.NET Core-alkalmazás futtatása'
description: Ismerkedjen meg a Linux-alkalmazásokkal Azure App Service az első ASP.NET Core alkalmazás üzembe helyezésével a App Service Linux-tárolójában.
keywords: azure app service, webalkalmazás, dotnet, core, linux, oss
ms.assetid: c02959e6-7220-496a-a417-9b2147638e2e
ms.tgt_pltfrm: linux
ms.topic: quickstart
ms.date: 04/22/2020
ms.custom: mvc, cli-validate, seodec18
ms.openlocfilehash: 1eeb5bbd4b10ef660a50f40d6c1300b0ca214561
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82206670"
---
# <a name="create-an-aspnet-core-app-in-app-service-on-linux"></a>ASP.NET Core-alkalmazás létrehozása Linux rendszeren App Service

> [!NOTE]
> Ebben a cikkben egy alkalmazást helyezünk üzembe a Linuxon futó App Service-ben. Ha _Windows_rendszeren szeretné telepíteni a app Servicet, tekintse meg a [ASP.net Core alkalmazás létrehozása az Azure-ban](../app-service-web-get-started-dotnet.md)című témakört.
>

A [linuxon app Service](app-service-linux-intro.md) a Linux operációs rendszert használó, jól méretezhető, önjavító webes üzemeltetési szolgáltatást nyújt. Ez a rövid útmutató bemutatja, hogyan hozhat létre [.NET Core](https://docs.microsoft.com/aspnet/core/)-alkalmazást Linuxon futó App Service-ben. Az alkalmazást az [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)-vel hozza létre, és a git használatával helyezi üzembe a .net Core-kódot az alkalmazásban.

![Az Azure-ban futó mintaalkalmazás](media/quickstart-dotnetcore/dotnet-browse-azure.png)

A cikk lépéseit Mac, Windows vagy Linux rendszert futtató gépen is követheti.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

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

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Webalkalmazás létrehozása

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-dotnetcore-linux-no-h.md)]

Tallózással keresse meg az újonnan létrehozott alkalmazást. Cserélje le _ &lt;az App-Name>_ az alkalmazás nevére.

```bash
https://<app-name>.azurewebsites.net
```

Az új alkalmazásnak így kell kinéznie:

![Alkalmazás üres lapja](media/quickstart-dotnetcore/dotnet-browse-created.png)

[!INCLUDE [Push to Azure](../../../includes/app-service-web-git-push-to-azure.md)] 

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

**Gratulálok!** Elvégezte az első .NET Core-alkalmazás üzembe helyezését a Linuxon futó App Service-ben.

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

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: ASP.NET Core alkalmazás SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [ASP.NET Core alkalmazás konfigurálása](configure-language-dotnetcore.md)
