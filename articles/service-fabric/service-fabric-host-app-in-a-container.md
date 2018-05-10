---
title: Tárolóban lévő .NET-alkalmazás telepítése Azure Service Fabricre | Microsoft Docs
description: A cikk bemutatja, hogyan lehet egy Visual Studióban lévő .NET-alkalmazást egy Docker-tárolóba csomagolni. Ezt az új tárolóalkalmazást a rendszer ezután egy Service Fabric-fürtre telepíti.
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 04a6fbc56d3c65cfb53339c4178dfa36e2aeb4ea
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="deploy-a-net-application-in-a-windows-container-to-azure-service-fabric"></a>Windows-tárolóban lévő .NET-alkalmazás telepítése Azure Service Fabricre

Ez az oktatóanyag bemutatja, hogyan telepíthet egy meglévő ASP.NET-alkalmazást egy Azure-on lévő Windows-tárolóban.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Docker-projekt létrehozása Visual Studióban
> * Meglévő alkalmazás tárolóba helyezése
> * Folyamatos integráció beállítása a Visual Studio és a VSTS használatával

## <a name="prerequisites"></a>Előfeltételek

1. A [Docker CE for Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description) telepítése tárolók Windows 10 rendszeren való futtatásához.
2. Ismerkedjen meg a [Windows 10-tárolók gyors üzembe helyezési útmutatójával][link-container-quickstart].
3. Töltse le a [Fabrikam Fiber CallCenter][link-fabrikam-github] mintaalkalmazást.
4. Az [Azure PowerShell][link-azure-powershell-install] telepítése
5. A [Visual Studio 2017 Continuous Delivery Tools bővítményének][link-visualstudio-cd-extension] telepítése
6. Hozzon létre egy [Azure-előfizetést][link-azure-subscription] és egy [Visual Studio Team Services-fiókot][link-vsts-account]. 
7. [Fürt létrehozása az Azure-ban](service-fabric-tutorial-create-vnet-and-windows-cluster.md)

## <a name="create-a-cluster-on-azure"></a>Fürt létrehozása az Azure-ban
A Service Fabric-alkalmazások egy fürtön, azaz virtuális vagy fizikai gépek hálózaton keresztül csatlakozó halmazán futnak. Az alkalmazása létrehozása és üzembe helyezése előtt [állítson be egy Azure-ban futó Service Fabric-fürtöt](service-fabric-tutorial-create-vnet-and-windows-cluster.md). A fürt létrehozásakor válassza ki a tárolók futtatását támogató termékváltozatot (például: Windows Server 2016 Datacenter tárolókkal).

## <a name="containerize-the-application"></a>Az alkalmazás tárolóba helyezése

Most, hogy már rendelkezik egy Azure-ban futó Service Fabric-fürttel, létrehozhat és üzembe helyezhet tárolóba helyezett alkalmazásokat. Ahhoz, hogy elindíthassuk az alkalmazásunkat egy tárolóban, hozzá kell adnunk a **Docker-támogatást** a Visual Studióban a projekthez. Amikor **Docker-támogatást** adunk az alkalmazáshoz, két dolog történik. Először is a rendszer hozzáad egy _Dockerfile_-t a projekthez. Ez az új fájl leírja, hogyan kell létrehozni a tároló rendszerképét. Ezután a rendszer hozzáad egy új _docker-compose_ projektet a megoldáshoz. Az új projekt tartalmaz néhány docker-compose-fájlt. A docker-compose-fájlok segítségével leírhatja a tárolók futtatásának módját.

További információ a [Visual Studio tárolóeszközeinek][link-visualstudio-container-tools] használatáról.

### <a name="add-docker-support"></a>Docker-támogatás hozzáadása

Nyissa meg a [FabrikamFiber.CallCenter.sln][link-fabrikam-github] fájlt a Visual Studióban.

Kattintson a jobb gombbal a **FabrikamFiber.Web** projektre, és válassza a **Hozzáadás** > **Docker-támogatás** elemet.

### <a name="add-support-for-sql"></a>SQL-támogatás hozzáadása

Ez az alkalmazás SQL-adatszolgáltatást használ, ezért egy SQL-kiszolgáló szükséges az alkalmazás futtatásához. Jelöljön meg egy SQL Server-tárolórendszerképet a docker-compose.override.yml fájlunkban.

A Visual Studióban keresse meg a **Megoldáskezelőben** található **docker-compose** elemet, és nyissa meg a **docker-compose.override.yml** fájlt.

Lépjen a `services:` csomóponthoz, és vegyen fel egy `db:` nevű, a tárolóhoz az SQL Server-bejegyzést meghatározó csomópontot.

```yml
  db:
    image: microsoft/mssql-server-windows-developer
    environment:
      sa_password: "Password1"
      ACCEPT_EULA: "Y"
    ports:
      - "1433"
    healthcheck:
      test: [ "CMD", "sqlcmd", "-U", "sa", "-P", "Password1", "-Q", "select 1" ]
      interval: 1s
      retries: 20
```

>[!NOTE]
>A helyi hibakereséshez tetszése szerinti SQL Servert választhat, csak legyen elérhető a gazdagépről. A **localdb** azonban nem támogatja a `container -> host` irányú kommunikációt.

>[!WARNING]
>Az SQL Server tárolóban való futtatása nem támogatja az adatok megtartását. Ha a tároló leáll, a rendszer törli az adatokat. Éles környezetben ne használja ezt a konfigurációt.

Lépjen a `fabrikamfiber.web:` csomóponthoz, és adjon hozzá egy `depends_on:` nevű gyermekcsomópontot. Ez biztosítja, hogy a `db` (az SQL Server-tároló) szolgáltatás a webalkalmazás (fabrikamfiber.web) előtt elindul.

```yml
  fabrikamfiber.web:
    depends_on:
      - db
```

### <a name="update-the-web-config"></a>A webes konfiguráció frissítése

Lépjen vissza a **FabrikamFiber.Web** projektre, frissítse a kapcsolati karakterláncot a **web.config** fájlban, hogy az a tárolóban lévő SQL Serverre mutasson.

```xml
<add name="FabrikamFiber-Express" connectionString="Data Source=db,1433;Database=FabrikamFiber;User Id=sa;Password=Password1;MultipleActiveResultSets=True" providerName="System.Data.SqlClient" />

<add name="FabrikamFiber-DataWarehouse" connectionString="Data Source=db,1433;Database=FabrikamFiber;User Id=sa;Password=Password1;MultipleActiveResultSets=True" providerName="System.Data.SqlClient" />
```

>[!NOTE]
>Ha egy másik SQL Servert kíván használni a webes alkalmazása kiadásra szánt verziójában, adjon hozzá egy másik kapcsolati karakterláncot a web.release.config fájlhoz.

### <a name="test-your-container"></a>A tároló tesztelése

Az alkalmazás tárolóban való futtatásához és a hibakereséshez nyomja le az **F5** billentyűt.

Az Edge megnyitja az alkalmazás megadott kezdőoldalát a tároló belső NAT-hálózatán lévő IP-címe segítségével (ez általában a 172.x.x.x). További információt a tárolókban található alkalmazások Visual Studio 2017 használatával történő hibakereséséről [ebben a cikkben][link-debug-container] találhat.

![Példa tárolóban lévő Fabrikamra][image-web-preview]

A tároló készen áll a Service Fabric-alkalmazásban való létrehozásra és csomagolásra. Ha a tárolórendszerkép létrejött a számítógépén, továbbíthatja bármely más tárolójegyzékbe, és lekérheti bármelyik gazdagépre, majd futtathatja.

## <a name="get-the-application-ready-for-the-cloud"></a>Az alkalmazás felkészítése a felhőre

Ahhoz, hogy az alkalmazás készen álljon az Azure-on lévő Service Fabricen való futásra, két lépést kell végrehajtania:

1. Tegye elérhetővé azt a portot, ahol el szeretné éri a Service Fabric-fürtön lévő webes alkalmazást.
2. Adjon meg egy éles üzemre felkészített SQL-adatbázist az alkalmazáshoz.

### <a name="expose-the-port-for-the-app"></a>A port elérhetővé tétele az alkalmazáshoz
A már konfigurált Service Fabric-fürt *80*-as portja alapértelmezés szerint meg van nyitva a fürtre beérkező adatforgalmat elosztó Azure Load Balancerben. A tárolónkat ezen a porton keresztül tehetjük elérhetővé a docker-compose.yml fájl segítségével.

A Visual Studióban keresse meg a **Megoldáskezelőben** található **docker-compose** elemet, és nyissa meg a **docker-compose.yml** fájlt.

Módosítsa a `fabrikamfiber.web:` csomópontot, és adjon hozzá egy `ports:` nevű gyermekcsomópontot.

Adjon hozzá egy `- "80:80"` karakterlánc-bejegyzést. A docker-compose.yml fájlnak a következőképpen kell kinéznie:

```yml
  version: '3'

  services:
    fabrikamfiber.web:
      image: fabrikamfiber.web
      build:
        context: .\FabrikamFiber.Web
        dockerfile: Dockerfile
      ports:
        - "80:80"
```

### <a name="use-a-production-sql-database"></a>Éles SQL Database használata
Éles környezetben való futtatáskor szükség van arra, hogy az adatok megmaradjanak az adatbázisban. Jelenleg nincs mód az állandó adatok biztosítására egy tárolóban, ezért nem tárolhat éles adatokat az SQL Serveren egy tárolóban.

Javasoljuk, hogy használjon egy Azure SQL Database-adatbázist. Egy felügyelt SQL Server Azure-ban való beállításához és futtatásához tekintse meg az [Azure SQL Database rövid útmutatóját][link-azure-sql].

>[!NOTE]
>Ne felejtse el módosítani az SQL-kiszolgálóra mutató kapcsolati karakterláncokat a **FabrikamFiber.Web** projekt **web.release.config** fájljában.
>
>Ez az alkalmazás szabályosan leáll, ha az SQL-adatbázis nem érhető el. Ha szeretné, lépjen tovább, és telepítse az alkalmazást SQL-kiszolgáló nélkül.

## <a name="deploy-with-visual-studio-team-services"></a>Üzembe helyezés a Visual Studio Team Services használatával

A Visual Studio Team Services segítségével végzett üzembe helyezéshez telepítenie kell a [Visual Studio 2017 Continuous Delivery Tools bővítményét][link-visualstudio-cd-extension]. Ez a bővítmény megkönnyíti az Azure-ra végzett telepítést a Visual Studio Team Services konfigurálásával és az alkalmazás Service Fabric-fürtön való telepítésével.

A kezdéshez a kódot verziókövetési rendszerben kell futtatnia. A szakasz további része feltételezi a **git** használatát.

### <a name="set-up-a-vsts-repo"></a>VSTS-adattár beállítása
A Visual Studio jobb alsó sarkában kattintson a **Hozzáadás a forráskezelőhöz** > **Git** (vagy a kívánt) lehetőségre.

![a forráskezelés gomb megnyomása][image-source-control]

A _Team Explorer_ ablaktáblán válassza a **Git-adattár közzététele** lehetőséget.

Válassza ki a VSTS-adattár nevét, és nyomja meg az **Adattár** gombot.

![adattár közzététele a VSTS-ben][image-publish-repo]

Most, hogy a kód szinkronizálva van egy VSTS-forrásadattárral, konfigurálhatja a folyamatos integrációt és a folyamatos teljesítést.

### <a name="setup-continuous-delivery"></a>Folyamatos teljesítés beállítása

A _Megoldáskezelőben_ kattintson a jobb gombbal a **megoldás** > **Folyamatos teljesítés konfigurálása** lehetőségre.

Válassza az Azure-előfizetés lehetőséget.

A **Gazdagép típusa** beállításnál a **Service Fabric-fürtöt** adja meg.

A **Célgazdagép** beállításnál az előző szakaszban létrehozott Service Fabric-fürtöt adja meg.

Válasszon egy **Tárolóregisztrációs adatbázist**, ahol közzé szeretné tenni a tárolót.

>[!TIP]
>A **Szerkesztés** gomb segítségével hozzon létre egy tárolójegyzéket.

Kattintson az **OK** gombra.

![Folyamatos Service Fabric-integráció beállítása][image-setup-ci]
   
   Ha a konfigurálás befejeződött, a tároló telepítve van a Service Fabricben. Valahányszor frissítést küld az adattárba, a rendszer végrehajt egy új létrehozást és kiadást.
   
   >[!NOTE]
   >A tárolórendszerkép létrehozása körülbelül 15 percet vesz igénybe.
   >Amikor először végez telepítést a Service Fabric-fürtön, az alapszintű Windows Server Core-tárolórendszerképeket le kell tölteni. A letöltés további 5–10 percet is igénybe vehet.

Keresse meg a Fabrikam Call Center alkalmazást a fürt URL-címe segítségével (például: *http://mycluster.westeurope.cloudapp.azure.com*).

Most, hogy tárolóba helyezte és telepítette a Fabrikam Call Center-megoldást, az [Azure Portalt][link-azure-portal] megnyitva láthatja, hogy az alkalmazása a Service Fabricben fut. Az alkalmazás kipróbálásához nyisson meg egy webböngészőt, és keresse fel a Service Fabric-fürtje URL-címét.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Docker-projekt létrehozása Visual Studióban
> * Meglévő alkalmazás tárolóba helyezése
> * Folyamatos integráció beállítása a Visual Studio és a VSTS használatával

Az oktatóanyag következő részéből megtudhatja, hogyan állíthat be [figyelést a tárolójához](service-fabric-tutorial-monitoring-wincontainers.md).

<!--   NOTE SURE WHAT WE SHOULD DO YET HERE

Advance to the next tutorial to learn how to bind a custom SSL certificate to it.

> [!div class="nextstepaction"]
> [Bind an existing custom SSL certificate to Azure Web Apps](app-service-web-tutorial-custom-ssl.md)

## Next steps

- [Container Tooling in Visual Studio][link-visualstudio-container-tools]
- [Get started with containers in Service Fabric][link-servicefabric-containers]
- [Creating Service Fabric applications][link-servicefabric-createapp]
-->

[link-debug-container]: /dotnet/articles/core/docker/visual-studio-tools-for-docker
[link-fabrikam-github]: https://aka.ms/fabrikamcontainer
[link-container-quickstart]: /virtualization/windowscontainers/quick-start/quick-start-windows-10
[link-visualstudio-container-tools]: /dotnet/articles/core/docker/visual-studio-tools-for-docker
[link-azure-powershell-install]: /powershell/azure/install-azurerm-ps
[link-servicefabric-create-secure-clusters]: service-fabric-cluster-creation-via-arm.md
[link-visualstudio-cd-extension]: https://aka.ms/cd4vs
[link-servicefabric-containers]: service-fabric-get-started-containers.md
[link-servicefabric-createapp]: service-fabric-create-your-first-application-in-visual-studio.md
[link-azure-portal]: https://portal.azure.com
[link-sf-clustertemplate]: https://aka.ms/securepreviewonelineclustertemplate
[link-azure-pricing-calculator]: https://azure.microsoft.com/pricing/calculator/
[link-azure-subscription]: https://azure.microsoft.com/free/
[link-vsts-account]: https://www.visualstudio.com/team-services/pricing/
[link-azure-sql]: /azure/sql-database/

[image-web-preview]: media/service-fabric-host-app-in-a-container/fabrikam-web-sample.png
[image-source-control]: media/service-fabric-host-app-in-a-container/add-to-source-control.png
[image-publish-repo]: media/service-fabric-host-app-in-a-container/publish-repo.png
[image-setup-ci]: media/service-fabric-host-app-in-a-container/configure-continuous-integration.png
