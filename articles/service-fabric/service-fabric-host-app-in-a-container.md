---
title: "A tárolóban lévő .NET alkalmazás telepítése az Azure Service Fabric |} Microsoft Docs"
description: "Útmutatást ad meg egy Docker-tároló a Visual Studio .NET-alkalmazás csomagolása. Az új \"container\" az alkalmazást majd telepíti a Service Fabric-fürt."
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/19/2017
ms.author: mikhegn
ms.openlocfilehash: 31c1cee5ddc4c8893da729af884ae7b7b8a58093
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2017
---
# <a name="deploy-a-net-application-in-a-windows-container-to-azure-service-fabric"></a>Azure Service Fabric Windows tároló .NET-alkalmazás telepítése

Ez az oktatóanyag bemutatja, hogyan Windows Azure tároló meglévő ASP.NET alkalmazás központi telepítése.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy Docker-projektet a Visual Studióban
> * Egy meglévő alkalmazást containerize
> * A telepítő folyamatos integráció a Visual Studio és a VSTS

## <a name="prerequisites"></a>Előfeltételek

1. Telepítés [Docker CE Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description) , hogy a Windows 10-tárolók is futtathatja.
2. Ismerje meg, a [Windows 10-tárolók gyors üzembe helyezés][link-container-quickstart].
3. Töltse le a [Fabrikam Fiber CallCenter] [ link-fabrikam-github] mintaalkalmazást.
4. Telepítés [Azure PowerShell][link-azure-powershell-install]
5. Telepítse a [Visual Studio 2017 folyamatos kézbesítési eszközök kiterjesztése][link-visualstudio-cd-extension]
6. Hozzon létre egy [Azure-előfizetés] [ link-azure-subscription] és egy [Visual Studio Team Services-fiók][link-vsts-account]. 
7. [Fürt létrehozása az Azure-on](service-fabric-tutorial-create-vnet-and-windows-cluster.md)

## <a name="create-a-cluster-on-azure"></a>Fürt létrehozása az Azure-ban
Service Fabric-alkalmazások futtatása egy fürtön, virtuális vagy fizikai gépek hálózathoz csatlakozó halmaza. [A telepítő egy Azure-ban futó Service Fabric-fürt](service-fabric-tutorial-create-vnet-and-windows-cluster.md) létrehozása és az alkalmazás központi telepítése előtt. A fürt létrehozásakor válassza ki a Termékváltozat, amely támogatja a futó tárolók (például Windows Server 2016 adatközpont tárolók).

## <a name="containerize-the-application"></a>Az alkalmazás containerize

Most, hogy egy Azure-ban futó Service Fabric-fürt készen áll a tárolóalapú alkalmazás létrehozását és telepítését. Indítsa el az alkalmazást futtató tároló, hozzá kell **Docker támogatási** a projektre a Visual Studióban. Amikor felvesz **Docker támogatási** az alkalmazás két dolog történik. Először egy _Dockerfile_ hozzáadódik a projektet. Ez az új fájl leírja, hogy a tároló kép kialakítani. Majd a második, egy új _docker compose_ Projekt hozzáadódik a megoldás. Az új projekt tartalmaz néhány docker compose fájlokat. Docker-compose fájlok segítségével a tároló futtatásának módját ismerteti.

További információ a használata [Visual Studio tároló eszközök][link-visualstudio-container-tools].

>[!NOTE]
>Ha az első alkalommal futtatja a Windows-tároló lemezképek a számítógépen, Docker CE kell lekérik a tárolók skálázása alap lemezképei. A jelen oktatóanyagban használt lemezkép 14 GB. Lépjen tovább, és a következő parancsot a Terminálszolgáltatások való lekérésére az alap lemezképek:
>```cmd
>docker pull microsoft/mssql-server-windows-developer
>docker pull microsoft/aspnet:4.6.2
>```

### <a name="add-docker-support"></a>Adja hozzá a Docker-támogatás

Nyissa meg a [FabrikamFiber.CallCenter.sln] [ link-fabrikam-github] fájlt a Visual Studióban.

Kattintson a jobb gombbal a **FabrikamFiber.Web** project > **Hozzáadás** > **Docker támogatási**.

### <a name="add-support-for-sql"></a>Az SQL támogatását

Az alkalmazás adatokat szolgáltató SQL akkor használ, ezért az alkalmazás futtatásához szükség egy SQL-kiszolgálóra. Egy SQL Server tároló lemezképet a docker-compose.override.yml fájl hivatkozik.

A Visual Studióban nyissa meg a **Megoldáskezelőben**, található **docker compose**, és nyissa meg a fájlt **docker-compose.override.yml**.

Keresse meg a `services:` csomópont, nevű csomópont hozzáadása `db:` , amely meghatározza, hogy az SQL Server bejegyzést ahhoz a tárolóhoz.

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
>Bármely SQL Server helyi hibakeresési inkább használható, amíg a gazdagép elérhető legyen. Azonban **localdb** nem támogatja a `container -> host` kommunikációt.

>[!WARNING]
>A tárolóban lévő SQL Server szoftvert futtató nem támogatja az adatait. Ha nem a tárolóban, az adat törlődik. Termelési környezetben ne használja ezt a konfigurációt.

Keresse meg a `fabrikamfiber.web:` csomópont, és adja hozzá a nevű gyermekcsomópontja `depends_on:`. Ez biztosítja, hogy a `db` (az SQL Server-tároló) szolgáltatás elindulása előtt a webalkalmazást (fabrikamfiber.web).

```yml
  fabrikamfiber.web:
    depends_on:
      - db
```

### <a name="update-the-web-config"></a>A webes konfiguráció frissítése

Vissza a **FabrikamFiber.Web** projektre, frissítse a kapcsolati karakterlánc a **web.config** fájlt úgy, hogy az SQL Server, a tároló mutasson.

```xml
<add name="FabrikamFiber-Express" connectionString="Data Source=db,1433;Database=FabrikamFiber;User Id=sa;Password=Password1;MultipleActiveResultSets=True" providerName="System.Data.SqlClient" />

<add name="FabrikamFiber-DataWarehouse" connectionString="Data Source=db,1433;Database=FabrikamFiber;User Id=sa;Password=Password1;MultipleActiveResultSets=True" providerName="System.Data.SqlClient" />
```

>[!NOTE]
>Ha egy másik használni kívánt SQL Server kiadási fejlesztéskor a webalkalmazás létrehozása, adja hozzá egy másik kapcsolati karakterláncot a web.release.config fájlhoz.

### <a name="test-your-container"></a>A tároló tesztelése

Nyomja le az **F5** futtatásához, és a tároló az alkalmazás hibakeresése.

Peremhálózati az alkalmazás meghatározott indítási lap használata az IP-cím, a tároló a belső NAT-hálózaton (általában 172.x.x.x) megnyitása. A Visual Studio 2017 használatával tárolókban lévő alkalmazások hibakeresése kapcsolatos további információkért lásd: [Ez a cikk][link-debug-container].

![fabrikam tároló – példa][image-web-preview]

A tároló készen áll a beépített és a Service Fabric-alkalmazás csomagolva. Miután a tároló kép, összeállítása a számítógépre, hogy a tároló beállításjegyzék, és lekérni a le minden állomás futtatásához.

## <a name="get-the-application-ready-for-the-cloud"></a>A felhő Felkészülés az alkalmazás

Ahhoz, hogy az alkalmazás készen áll az Azure Service Fabric futó, végezze el két lépésben kell:

1. Elérhetővé tenni a port, ahol azt szeretnénk tudni érnie a webes alkalmazás a Service Fabric-fürt.
2. Adja meg az alkalmazás üzemi készen SQL-adatbázis.

### <a name="expose-the-port-for-the-app"></a>A port, az alkalmazás elérhetővé
A Service Fabric-fürt rendelkezik konfigurált,-porttal rendelkezik *80* nyissa meg az Azure Load Balancer alapértelmezés szerint, elosztja a fürt bejövő forgalmat. Is elérhetővé kell tenni a tárolót a porton keresztül a docker-compose.yml fájlt.

A Visual Studióban nyissa meg a **Megoldáskezelőben**, található **docker compose**, és nyissa meg a fájlt **docker-compose.yml**.

Módosítsa a `fabrikamfiber.web:` csomópont, nevű alárendelt csomópont hozzáadása `ports:`.

Adjon hozzá egy karakterlánc-bejegyzést `- "80:80"`. Ez az, hogy mi a docker-compose.yml fájlt hasonlóan kell kinéznie:

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

### <a name="use-a-production-sql-database"></a>SQL-adatbázis használata
Ha éles környezetben fut, igazolnia kell-e az adatok maradnak az adatbázisban. Jelenleg nincs mód állandó adatokat tároló biztosítása érdekében, ezért nem tárolhatók termelési adatok a tárolóban lévő SQL Server.

Azt javasoljuk, hogy egy Azure SQL Database használják. Telepítsen és futtasson egy kezelt SQL Server az Azure-ban, látogasson el a [Azure SQL Database – Gyorsútmutatók] [ link-azure-sql] cikk.

>[!NOTE]
>Ne felejtse el a kapcsolati karakterláncok módosítsa az SQL-kiszolgálót a **web.release.config** fájlt a **FabrikamFiber.Web** projekt.
>
>Ez az alkalmazás szabályosan meghiúsul, ha az SQL-adatbázis nem érhető el. Ha szeretné, lépjen tovább, és a nem SQL server alkalmazás központi telepítését.

## <a name="deploy-with-visual-studio-team-services"></a>A Visual Studio Team Services telepítéséhez

Visual Studio Team Services használatával üzembe helyezés beállítása, telepíteni kell a [folyamatos kézbesítési eszközök bővítményt a Visual Studio 2017][link-visualstudio-cd-extension]. A bővítmény megkönnyíti, hogy telepítse az Azure úgy konfigurálja a Visual Studio Team Services, és az alkalmazás központi telepítése a Service Fabric-fürt.

A kezdéshez a kódot a verziókövetési rendszerrel kell futnia. Ez a szakasz a többi azt feltételezi, hogy **git** használatban van.

### <a name="set-up-a-vsts-repo"></a>Egy VSTS-tárház beállítása
Kattintson a jobb alsó sarkában, a Visual Studio, **adja hozzá a verziókövetési** > **Git** (vagy célszerű beállítás).

![az adatforrás-vezérlő gombra][image-source-control]

Az a _Team Explorer_ ablaktáblában kattintson az **Git-tárház közzététele**.

Válassza ki a VSTS-tárház nevét, és nyomja le az **tárház**.

![tárház VSTS közzététele][image-publish-repo]

Most, hogy a kód egy VSTS-forrás tárházat szinkronizálva van, beállíthatja a folyamatos integrációt és folyamatos kézbesítését.

### <a name="setup-continuous-delivery"></a>A telepítő folyamatos kézbesítés

A _Megoldáskezelőben_, kattintson a jobb gombbal a **megoldás** > **konfigurálása a folyamatos kézbesítési**.

Válassza ki az Azure-előfizetés.

Állítsa be **típus gazdagép** való **Service Fabric-fürt**.

Állítsa be **célállomás** az előző szakaszban létrehozott service fabric-fürt számára.

Válasszon egy **tároló beállításjegyzék** a tárolóban való közzétételéhez.

>[!TIP]
>Használja a **szerkesztése** gomb segítségével hozza létre a tároló beállításkulcs.

Kattintson az **OK** gombra.

![a telepítő service fabric folyamatos integrációt][image-setup-ci]
   
   Ha a konfiguráció befejeződött, a tároló a Service Fabric rendszer. Amikor Ön leküldéses frissíti a tárház új buildverziót, és kiadás végrehajtása.
   
   >[!NOTE]
   >A tároló képek hajtsa végre a megfelelő felépítése körülbelül 15 perc.
   >A Service Fabric-fürt első telepített hatására a Windows Server Core alap tároló képek le kell tölteni. A letöltés további 5-10 percet is igénybe vehet.

Keresse meg a Fabrikam ügyfélszolgálatával alkalmazást a fürt URL-címe: például *http://mycluster.westeurope.cloudapp.azure.com*

Most, hogy indexelése, és a Fabrikam ügyfélszolgálatával megoldás telepített, akkor nyissa meg a [Azure-portálon] [ link-azure-portal] és tekintse meg a Service Fabric futó alkalmazások. Próbálja meg az alkalmazás, nyisson meg egy webböngészőt, és nyissa meg a Service Fabric-fürt URL-címét.

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Hozzon létre egy Docker-projektet a Visual Studióban
> * Egy meglévő alkalmazást containerize
> * A telepítő folyamatos integráció a Visual Studio és a VSTS

Az oktatóanyag következő része, megtudhatja, hogyan állíthat be [a tároló figyelési](service-fabric-tutorial-monitoring-wincontainers.md).

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
[link-azure-pricing-calculator]: https://azure.microsoft.com/en-us/pricing/calculator/
[link-azure-subscription]: https://azure.microsoft.com/en-us/free/
[link-vsts-account]: https://www.visualstudio.com/team-services/pricing/
[link-azure-sql]: /azure/sql-database/

[image-web-preview]: media/service-fabric-host-app-in-a-container/fabrikam-web-sample.png
[image-source-control]: media/service-fabric-host-app-in-a-container/add-to-source-control.png
[image-publish-repo]: media/service-fabric-host-app-in-a-container/publish-repo.png
[image-setup-ci]: media/service-fabric-host-app-in-a-container/configure-continuous-integration.png
