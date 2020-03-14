---
title: 'Gyors útmutató: Windows-tároló (előzetes verzió)'
description: Az első egyéni Windows-tároló üzembe helyezése Azure App Service. Használja ki a tárolókra bontás előnyeit, és szabja testre a Windows-tárolót.
ms.topic: quickstart
ms.date: 08/30/2019
ms.custom: seodec18
ms.openlocfilehash: 7901498772b8e746fb2c87a5237f06ab279e3b64
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79241471"
---
# <a name="run-a-custom-windows-container-in-azure-preview"></a>Egyéni Windows-tároló futtatása az Azure-ban (előzetes verzió)

Az [Azure App Service](overview.md) előre meghatározott, IIS-en futó alkalmazáscsoportokat biztosít Windows rendszeren, például az ASP.NET-et vagy a Node.js-t. Az előre konfigurált Windows-környezet zárolja az operációs rendszert a rendszergazdai hozzáférés, a Szoftvertelepítés, a globális szerelvény-gyorsítótár változásai és így tovább. További információ: [operációs rendszer funkciójának Azure app Service](operating-system-functionality.md). Ha az alkalmazás az előre konfigurált környezet által engedélyezettnél nagyobb mértékű hozzáférést igényel, üzembe helyezhet egy egyéni Windows-tárolót.

Ez a rövid útmutató bemutatja, hogyan helyezhet üzembe egy ASP.NET-alkalmazást egy Windows-lemezképben a [Docker hub](https://hub.docker.com/) -ból a Visual studióból. Az alkalmazást a Azure App Service egy egyéni tárolójában futtatja.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

- <a href="https://hub.docker.com/" target="_blank">Regisztráció Docker Hub-fiókra</a>
- <a href="https://docs.docker.com/docker-for-windows/install/" target="_blank">Windows rendszerhez készült Docker telepítése</a>.
- <a href="https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10" target="_blank">A Docker átváltása Windows-tárolók futtatására</a>.
- <a href="https://www.visualstudio.com/downloads/" target="_blank">Telepítse a Visual Studio 2019</a> -et a **ASP.net, valamint a webes fejlesztési** és az **Azure-fejlesztési** számítási feladatokkal. Ha már telepítette a Visual Studio 2019-et:

    - Telepítse a legújabb frissítéseket a Visual Studióban a **súgó** > **frissítések keresése**lehetőség kiválasztásával.
    - Vegye fel a számítási feladatokat a Visual Studióban az eszközök > **eszközök és szolgáltatások lekérése** **eszköz** kiválasztásával.

## <a name="create-an-aspnet-web-app"></a>ASP.NET-webapp létrehozása

Hozzon létre egy ASP.NET-webalkalmazást a következő lépések végrehajtásával:

1. Nyissa meg a Visual studiót, majd válassza **az új projekt létrehozása**lehetőséget.

1. Az **új projekt létrehozása**lapon keresse meg és válassza a **ASP.net webalkalmazás (.NET-keretrendszer)** elemet, majd kattintson a C# **tovább**gombra.

1. Az **új projekt konfigurálása**lapon nevezze el az alkalmazás _myFirstAzureWebApp_, majd válassza a **Létrehozás**lehetőséget.

   ![A webalkalmazás-projekt konfigurálása](./media/app-service-web-get-started-windows-container/configure-web-app-project-container.png)

1. Bármilyen ASP.NET-webappot üzembe helyezhet az Azure-ban. Ebben a rövid útmutatóban válassza az **MVC** sablont.

1. Válassza a **Docker-támogatás**lehetőséget, és győződjön meg arról, hogy a hitelesítés beállítása **Nincs hitelesítés**. Kattintson a **Létrehozás** gombra.

   ![ASP.NET-Webalkalmazás létrehozása](./media/app-service-web-get-started-windows-container/select-mvc-template-for-container.png)

1. Ha a _Dockerfile_ fájl nem nyílik meg automatikusan, nyissa meg a **Solution Explorer** (Megoldáskezelő) lapról.

1. Szüksége van egy [támogatott szülő rendszerképre](#use-a-different-parent-image). Módosítsa a szülőrendszerképet a `FROM` sor a következő kódra való lecserélésével, majd mentse a fájlt:

   ```Dockerfile
   FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-ltsc2019
   ```

1. A Visual Studio menüjében válassza a **hibakeresés** > **Indítás hibakeresés nélkül** lehetőséget a webalkalmazás helyi futtatásához.

   ![Az alkalmazás futtatása helyileg](./media/app-service-web-get-started-windows-container/local-web-app.png)

## <a name="publish-to-docker-hub"></a>Közzététel a Docker Hubon

1. A **megoldáskezelő**kattintson a jobb gombbal a **myFirstAzureWebApp** projektre, és válassza a **Közzététel**lehetőséget.

1. Válassza a **app Service** lehetőséget, majd válassza a **Közzététel**lehetőséget.

1. A **közzétételi cél**kiválasztása lapon válassza ki a **Container Registry** és a **Docker hub**elemet, majd kattintson a **Közzététel**elemre.

   ![Közzététel a projekt áttekintő oldaláról](./media/app-service-web-get-started-windows-container/publish-to-docker-vs2019.png)

1. Adja meg a Docker hub-fiók hitelesítő adatait, majd válassza a **Mentés**lehetőséget.

   Várjon, amíg az üzembe helyezés befejeződik. A **közzétételi** oldal mostantól megjeleníti az adattár nevét, amelyet később használni szeretne.

   ![Közzététel a projekt áttekintő oldaláról](./media/app-service-web-get-started-windows-container/published-docker-repository-vs2019.png)

1. Másolja az adattár nevét későbbi felhasználásra.

## <a name="create-a-windows-container-app"></a>Tárolóalkalmazás létrehozása Windowshoz

1. Jelentkezzen be az [Azure Portal]( https://portal.azure.com).

1. Válassza az Azure Portal bal felső sarkában az **Erőforrás létrehozása** lehetőséget.

1. Az Azure Marketplace-erőforrások listájának megadásához keresse meg a **Web App for containers**, és válassza a **Létrehozás**lehetőséget.

1. A **Webalkalmazás létrehozása lapon**válassza ki az előfizetését és egy **erőforráscsoportot**. Szükség esetén új erőforráscsoportot is létrehozhat.

1. Adja meg az alkalmazás nevét, például a *Win-Container-demót* , és válassza a **Windows** **operációs rendszer**lehetőséget. A folytatáshoz kattintson a **Tovább gombra: Docker** .

   ![Web App for Containers létrehozása](media/app-service-web-get-started-windows-container/create-web-app-continer.png)

1. A **Képforráshoz**válassza a **Docker hub** lehetőséget, majd a **rendszerkép és a címke**mezőben adja meg a [Közzététel a Docker hub](#publish-to-docker-hub)-ban másolt adattár nevét.

   ![A Web App for Containers konfigurálása](media/app-service-web-get-started-windows-container/configure-web-app-continer.png)

    Ha az egyéni lemezkép nem a webalkalmazásban, hanem például az [Azure Container Registryben](/azure/container-registry/) vagy bármely egyéb privát adattárban található, itt konfigurálhatja.

1. Válassza az **Áttekintés és létrehozás** , majd **a létrehozás lehetőséget, és várja** meg, amíg az Azure létrehozza a szükséges erőforrásokat.

## <a name="browse-to-the-container-app"></a>A tárolóalkalmazás megkeresése

Értesítés jelenik meg, ha az Azure befejezte a művelet végrehajtását.

![Az üzembe helyezés sikerült](media/app-service-web-get-started-windows-container/portal-create-finished.png)

1. Kattintson az **Erőforrás megnyitása** elemre.

1. Az erőforrás áttekintésében kövesse az **URL-cím**melletti hivatkozást.

Megnyílik egy új böngésző oldal a következő lapra:

![Windows-tároló alkalmazás indítása](media/app-service-web-get-started-windows-container/app-starting.png)

Várjon néhány percet, és próbálkozzon újra, amíg meg nem jelenik az ASP.NET alapértelmezett kezdőlapja:

![Windows-tároló alkalmazás fut](media/app-service-web-get-started-windows-container/app-running-vs.png)

**Gratulálunk!** Első egyéni Windows-tárolója immár fut az Azure App Service-ben.

## <a name="see-container-start-up-logs"></a>A tároló rendszerindítási naplóinak megtekintése

A Windows-tároló betöltése hosszabb időbe telhet. A folyamat előrehaladásának nyomon követéséhez nyissa meg az alábbi URL-címet úgy, hogy az *\<app_name>* sztringet az alkalmazás nevére cseréli.
```
https://<app_name>.scm.azurewebsites.net/api/logstream
```

A streamelt naplók a következőképpen néznek ki:

```
2018-07-27T12:03:11  Welcome, you are now connected to log-streaming service.
27/07/2018 12:04:10.978 INFO - Site: win-container-demo - Start container succeeded. Container: facbf6cb214de86e58557a6d073396f640bbe2fdec88f8368695c8d1331fc94b
27/07/2018 12:04:16.767 INFO - Site: win-container-demo - Container start complete
27/07/2018 12:05:05.017 INFO - Site: win-container-demo - Container start complete
27/07/2018 12:05:05.020 INFO - Site: win-container-demo - Container started successfully
```

## <a name="update-locally-and-redeploy"></a>Frissítés helyileg és ismételt üzembe helyezés

1. A Visual Studióban **Megoldáskezelőban**nyissa meg a **views** > **Home** > **index. cshtml**.

1. Keresse meg a `<div class="jumbotron">` HTML-címkét felül, és cserélje le az egész elemet az alábbi kódra:

   ```HTML
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we’ve built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Az Azure-ba történő ismételt üzembe helyezéshez kattintson a jobb gombbal a **myFirstAzureWebApp** projektre **megoldáskezelő** , majd válassza a **Közzététel**lehetőséget.

1. A közzétételi oldalon válassza a **Publish** (Közzététel) elemet, és várja meg, amíg a közzététel végbemegy.

1. Ahhoz, hogy az App Service-nek előírhassa az új rendszerkép a Docker Hubból történő lekérését, az alkalmazás újraindítása szükséges. A portál alkalmazáslapján kattintson az **Újraindítás** > **Igen** elemre.

   ![Webalkalmazás újraindítása az Azure-ban](./media/app-service-web-get-started-windows-container/portal-restart-app.png)

[A tárolóalkalmazás megkeresése](#browse-to-the-container-app) ismét. A weboldal frissítésekor az alkalmazásnak először vissza kell térnie az „Indítás” lapra, majd pár perc elteltével ismét meg kell jelenítenie a frissített weboldalt.

![Frissített webalkalmazás az Azure-ban](./media/app-service-web-get-started-windows-container/azure-web-app-updated.png)

## <a name="use-a-different-parent-image"></a>Másik szülőrendszerkép használata

Egy másik egyéni Docker-rendszerképet is használhat az alkalmazás futtatásához. A kívánt keretrendszerhez azonban ki kell választania a megfelelő [szülő rendszerképet (alaprendszerkép)](https://docs.docker.com/develop/develop-images/baseimages/) :

- A .NET-keretrendszerbeli alkalmazások telepítéséhez a Windows Server Core 2019 [hosszú távú karbantartási csatorna (LTSC)](https://docs.microsoft.com/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc) kiadásán alapuló szülő lemezképet használjon. 
- A .NET Core-alkalmazások telepítéséhez a Windows Server Nano 1809 [féléves karbantartási csatorna (SAC)](https://docs.microsoft.com/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) kiadásán alapuló szülő lemezképet használjon. 

Az alkalmazás indításakor a szülőrendszerkép letöltése hosszabb időbe telhet. Az indítási időt azonban lecsökkentheti az alábbi, az Azure App Service-ben már gyorsítótárazott szülőrendszerképek egyikének használatával:

- [MCR.microsoft.com/DotNet/Framework/ASPNET](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/): 4.7.2-windowsservercore-ltsc2019
- [MCR.microsoft.com/Windows/nanoserver](https://hub.docker.com/_/microsoft-windows-nanoserver/): 1809 – ez a rendszerkép a Microsoft [ASP.net Core](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/) Microsoft Windows Nano Server rendszerképein használt alaptároló.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Áttelepítés az Azure-ban található Windows-tárolókba](app-service-web-tutorial-windows-containers-custom-fonts.md)
