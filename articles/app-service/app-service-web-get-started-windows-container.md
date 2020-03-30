---
title: 'Gyorsindítás: Windows-tároló (előzetes verzió)'
description: Telepítse az első egyéni Windows-tárolót az Azure App Service-be. Használja ki a tárolók előnyeit, és szabja testre a Windows-tárolót a kívánt módon.
ms.topic: quickstart
ms.date: 08/30/2019
ms.custom: mvc, seodec18
ms.openlocfilehash: cd6b78e5fd824cc013cc946d23677237923f485e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80047104"
---
# <a name="run-a-custom-windows-container-in-azure-preview"></a>Egyéni Windows-tároló futtatása az Azure-ban (előzetes verzió)

Az [Azure App Service](overview.md) előre meghatározott, IIS-en futó alkalmazáscsoportokat biztosít Windows rendszeren, például az ASP.NET-et vagy a Node.js-t. Az előre konfigurált Windows-környezet zárolja az operációs rendszert a felügyeleti hozzáféréstől, a szoftvertelepítésektől, a globális összeállítási gyorsítótár változásaitól és így tovább. További információt az [Azure App Service operációs rendszerfunkciói című témakörben talál.](operating-system-functionality.md) Ha az alkalmazás az előre konfigurált környezet által engedélyezettnél nagyobb mértékű hozzáférést igényel, üzembe helyezhet egy egyéni Windows-tárolót.

Ez a rövid útmutató bemutatja, hogyan telepíthet egy ASP.NET alkalmazást egy Windows-lemezképben a Visual Studio [Docker Hub](https://hub.docker.com/) szolgáltatásába. Az alkalmazást egy egyéni tárolóban futtatja az Azure App Service-ben.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

- <a href="https://hub.docker.com/" target="_blank">Regisztráció Docker Hub-fiókra</a>
- <a href="https://docs.docker.com/docker-for-windows/install/" target="_blank">Windows rendszerhez készült Docker telepítése</a>.
- <a href="https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10" target="_blank">A Docker átváltása Windows-tárolók futtatására</a>.
- <a href="https://www.visualstudio.com/downloads/" target="_blank">Telepítse a Visual Studio 2019-et</a> a **ASP.NET és a webfejlesztés,** valamint az **Azure fejlesztési** munkaterheléseivel. Ha már telepítette a Visual Studio 2019-et:

    - Telepítse a legújabb frissítéseket a Visual Studióban a **Súgó** > **frissítések keresése**lehetőséget választva.
    - Adja hozzá a számítási feladatokat a Visual Studióban az **Eszközök** > **be- és szolgáltatások**lehetőséget választva.

## <a name="create-an-aspnet-web-app"></a>ASP.NET-webapp létrehozása

Hozzon létre egy ASP.NET webalkalmazást az alábbi lépésekkel:

1. Nyissa meg a Visual Studio alkalmazást, és válassza **az Új projekt létrehozása**lehetőséget.

1. Az **Új projekt létrehozása**csoportban keresse meg és válassza ki ASP.NET Web Application (.NET Framework) for C#(Web Application **(.NET Framework)** lehetőséget, majd válassza a **Tovább**gombot.

1. Az **Új projekt konfigurálása**területen nevezze el az alkalmazást a _myFirstAzureWebApp_alkalmazásnak, majd válassza a **Létrehozás lehetőséget.**

   ![A webalkalmazás-projekt konfigurálása](./media/app-service-web-get-started-windows-container/configure-web-app-project-container.png)

1. Bármilyen ASP.NET-webappot üzembe helyezhet az Azure-ban. Ehhez a rövid útmutatóhoz válassza az **MVC** sablont.

1. Válassza a **Docker-támogatás**lehetőséget, és győződjön meg arról, hogy a hitelesítés **nincs hitelesítés.** Kattintson a **Létrehozás** gombra.

   ![ASP.NET webalkalmazás létrehozása](./media/app-service-web-get-started-windows-container/select-mvc-template-for-container.png)

1. Ha a _Dockerfile_ fájl nem nyílik meg automatikusan, nyissa meg a **Solution Explorer** (Megoldáskezelő) lapról.

1. [Támogatott szülőlemezképre](#use-a-different-parent-image)van szükség. Módosítsa a szülőrendszerképet a `FROM` sor a következő kódra való lecserélésével, majd mentse a fájlt:

   ```Dockerfile
   FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-ltsc2019
   ```

1. A Visual Studio menüjében válassza a **Hibakeresés hibakeresés** > nélküli hibakeresés**parancsát** a webalkalmazás helyi futtatásához.

   ![Az alkalmazás futtatása helyileg](./media/app-service-web-get-started-windows-container/local-web-app.png)

## <a name="publish-to-docker-hub"></a>Közzététel a Docker Hubon

1. A **Megoldáskezelőben**kattintson a jobb gombbal a **myFirstAzureWebApp** projektre, és válassza **a Közzététel parancsot.**

1. Válassza **az App Service,** majd **a Közzététel**lehetőséget.

1. A **Közzétételi cél**kiválasztása csoportban válassza **a Tárolóbeállításés** **a Docker-központ**lehetőséget, majd kattintson **a Közzététel gombra.**

   ![Közzététel a projekt áttekintő oldaláról](./media/app-service-web-get-started-windows-container/publish-to-docker-vs2019.png)

1. Adja meg a Docker Hub-fiók hitelesítő adatait, és válassza a **Mentés lehetőséget.**

   Várjon, amíg az üzembe helyezés befejeződik. A **Közzététel** lap mostantól a később használandó tárház nevét jeleníti meg.

   ![Közzététel a projekt áttekintő oldaláról](./media/app-service-web-get-started-windows-container/published-docker-repository-vs2019.png)

1. Másolja az adattár nevét későbbi felhasználásra.

## <a name="create-a-windows-container-app"></a>Tárolóalkalmazás létrehozása Windowshoz

1. Jelentkezzen be az [Azure Portalra.]( https://portal.azure.com)

1. Válassza az Azure Portal bal felső sarkában az **Erőforrás létrehozása** lehetőséget.

1. Az Azure Marketplace-erőforrások listájának keresőmezőjében keresse meg a **Web App for Containers**elemet, és válassza a **Létrehozás gombot.**

1. A **Web App Létrehozása**alkalmazásban válassza ki az előfizetést és az **erőforráscsoportot.** Szükség esetén új erőforráscsoportot is létrehozhat.

1. Adjon meg egy alkalmazásnevet, például *a win-container-demo parancsot,* és válassza a **Windows** operációs **rendszerhez**lehetőséget. Válassza a **Tovább: Docker** a folytatáshoz.

   ![Webalkalmazás létrehozása tárolókhoz](media/app-service-web-get-started-windows-container/create-web-app-continer.png)

1. A **Képforrás**hoz válassza a **Docker Hub** és a **Image and tag**lehetőséget, írja be a másolatot a Közzététel a [Docker Hubba](#publish-to-docker-hub)mezőbe.

   ![Konfigurálja, hogy Web App tárolókhoz](media/app-service-web-get-started-windows-container/configure-web-app-continer.png)

    Ha az egyéni lemezkép nem a webalkalmazásban, hanem például az [Azure Container Registryben](/azure/container-registry/) vagy bármely egyéb privát adattárban található, itt konfigurálhatja.

1. Válassza **a Véleményezés és a Létrehozás lehetőséget,** majd a **Létrehozás** és várakozás, amíg az Azure létrehozza a szükséges erőforrásokat.

## <a name="browse-to-the-container-app"></a>A tárolóalkalmazás megkeresése

Értesítés jelenik meg, ha az Azure befejezte a művelet végrehajtását.

![A telepítés sikerült](media/app-service-web-get-started-windows-container/portal-create-finished.png)

1. Kattintson az **Erőforrás megnyitása** elemre.

1. Az erőforrás áttekintésében kövesse az **URL**melletti hivatkozást.

Egy új böngészőlap nyílik meg a következő oldalon:

![A Windows tárolóalkalmazás indítása](media/app-service-web-get-started-windows-container/app-starting.png)

Várjon néhány percet, és próbálkozzon újra, amíg meg nem jelenik az ASP.NET alapértelmezett kezdőlapja:

![Futó Windows Container Alkalmazás](media/app-service-web-get-started-windows-container/app-running-vs.png)

**Gratulálok!** Első egyéni Windows-tárolója immár fut az Azure App Service-ben.

## <a name="see-container-start-up-logs"></a>A tároló rendszerindítási naplóinak megtekintése

A Windows-tároló betöltése hosszabb időbe telhet. A folyamat megtekintéséhez keresse meg a következő URL-címet * \<úgy,* hogy lecseréli app_name>az alkalmazás nevére.
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

1. A Visual Studio **Megoldáskezelőjében**nyissa meg a **Nézetek** > **kezdőlapja** > **Index.cshtml fájlt.**

1. Keresse meg a `<div class="jumbotron">` HTML-címkét felül, és cserélje le az egész elemet az alábbi kódra:

   ```HTML
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Az Azure-ba való újratelepítéshez kattintson a jobb gombbal a **myFirstAzureWebApp** projektre a **Megoldáskezelőben,** és válassza a **Közzététel parancsot.**

1. A közzétételi oldalon válassza a **Publish** (Közzététel) elemet, és várja meg, amíg a közzététel végbemegy.

1. Ahhoz, hogy az App Service-nek előírhassa az új rendszerkép a Docker Hubból történő lekérését, az alkalmazás újraindítása szükséges. A portál alkalmazáslapján kattintson az **Igen újraindítás gombra.** > **Yes**

   ![Webalkalmazás újraindítása az Azure-ban](./media/app-service-web-get-started-windows-container/portal-restart-app.png)

[A tárolóalkalmazás megkeresése](#browse-to-the-container-app) ismét. A weboldal frissítésekor az alkalmazásnak először vissza kell térnie az „Indítás” lapra, majd pár perc elteltével ismét meg kell jelenítenie a frissített weboldalt.

![Frissített webalkalmazás az Azure-ban](./media/app-service-web-get-started-windows-container/azure-web-app-updated.png)

## <a name="use-a-different-parent-image"></a>Másik szülőrendszerkép használata

Az alkalmazás futtatásához egy másik egyéni Docker-lemezképet használhat. A kívánt keretrendszerhez azonban ki kell választania a megfelelő [szülőképet (alapképet):](https://docs.docker.com/develop/develop-images/baseimages/)

- A . [Long-Term Servicing Channel (LTSC)](https://docs.microsoft.com/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc) 
- A .NET Core alkalmazások telepítéséhez használjon szülőlemezképet a Windows Server Nano 1809 [féléves karbantartási csatorna (SAC)](https://docs.microsoft.com/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) kiadása alapján. 

Az alkalmazás indításakor a szülőrendszerkép letöltése hosszabb időbe telhet. Az indítási időt azonban lecsökkentheti az alábbi, az Azure App Service-ben már gyorsítótárazott szülőrendszerképek egyikének használatával:

- [mcr.microsoft.com/dotnet/framework/aspnet](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/):4.7.2-windowsservercore-ltsc2019
- [mcr.microsoft.com/windows/nanoserver](https://hub.docker.com/_/microsoft-windows-nanoserver/):1809 – ez a lemezkép a Microsoft [ASP.NET Core](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/) Microsoft Windows Nano Server lemezképekalaptárolója.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Áttelepítés az Azure-ban található Windows-tárolókba](app-service-web-tutorial-windows-containers-custom-fonts.md)
