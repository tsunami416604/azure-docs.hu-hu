---
title: Windows-tárolókat Service Fabric és a VS hibakeresési |} A Microsoft Docs
description: Megtudhatja, hogyan hibakeresése a Visual Studio 2017 használatával az Azure Service Fabric Windows-tárolók.
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/14/2018
ms.author: mikhegn
ms.openlocfilehash: 437c38a8e674fcdf06e26a7191ceecef9d901470
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38968320"
---
# <a name="how-to-debug-windows-containers-in-azure-service-fabric-using-visual-studio-2017"></a>Útmutató: Windows-tárolókhoz az Azure Service Fabric használatával a Visual Studio 2017 hibakeresése

A Visual Studio 2017 Update 7 (15.7) a tárolókban lévő .NET-alkalmazások, a Service Fabric-szolgáltatások is hibakeresési. Ez a cikk bemutatja, hogyan konfigurálja a környezetet, és ezután hibakeresése egy helyi Service Fabric-fürtön futó tárolóban lévő .NET-alkalmazás.

## <a name="prerequisites"></a>Előfeltételek

* A Windows 10-es, kövesse az ebben a rövid útmutatóban a [konfigurálása a Windows 10-es Windows-tárolók futtatásához](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10)
* Windows Server 2016 rendszerben, kövesse az ebben a rövid útmutatóban a [Windows 2016 konfigurálása a Windows-tárolók futtatásához](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-server)
* A helyi Service Fabric-környezet beállítása a következő [a Windows fejlesztési környezet előkészítése](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)

## <a name="configure-your-developer-environment-to-debug-containers"></a>Tárolók hibakeresése a fejlesztői környezet beállítása

1. Győződjön meg arról, hogy a Docker ablak szolgáltatás fut-e a következő lépés végrehajtása előtt.

1. Annak érdekében, hogy támogatja a DNS-feloldás tárolók között, akkor a helyi fejlesztési fürt beállításához a számítógépnevet használja.
    1. Rendszergazdaként nyissa meg PowerShell
    1. Keresse meg a fürt SDK-telepítési mappa, általában `C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup`
    1. Futtassa a szkriptet `DevClusterSetup.ps1` paraméterrel `-UseMachineName`

    ``` PowerShell
      C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1 -UseMachineName
    ```

    > [!NOTE]
    > Használhatja a `-CreateOneNodeCluster` , állítson be egy egycsomópontos fürtöt. Alapértelmezés szerint létrehoz egy helyi ötcsomópontos fürt.
    >

    A Service fabric DNS szolgáltatással kapcsolatos további tudnivalókért lásd: [DNS-szolgáltatás az Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice).

### <a name="known-limitations-when-debugging-containers-in-service-fabric"></a>Tárolók Service Fabricben történő hibakeresése során ismert korlátozások

Alább az ismert korlátozások hibakeresési tárolók a Service Fabric és a lehetséges megoldások listája:

* Localhost ClusterFQDNorIP használatával nem támogatja DNS-feloldás tárolókban.
    * Megoldás: A gép nevét (lásd fent) használatával helyi fürt beállítása
* Egy virtuális gépen futó Windows 10-es nem kap vissza a tárolót a DNS-válaszban.
    * Megoldás: UDP ellenőrzőösszeg-kiszervezés IPv4 a virtuális gépek hálózati Adapteren tiltsa le
    * Ne feledje, ez csökkenti a hálózati teljesítmény a gépen.
    * https://github.com/Azure/service-fabric-issues/issues/1061
* Feloldása szolgáltatások DNS-sel ugyanazt az alkalmazást a szolgáltatásnév nem működik a Windows 10-es, ha az alkalmazás üzemel, a Docker Compose használatával
    * Megoldás: A Szolgáltatásvégpontok megoldásához servicename.applicationname használja
    * https://github.com/Azure/service-fabric-issues/issues/1062
* ClusterFQDNorIP IP-címet használja, ha a gazdagép elsődleges IP módosítása megszakítja a DNS-funkciók.
    * Megoldás: Hozza létre újra a fürtöt az új elsődleges IP használata a gazdagépen, vagy használja a gép nevét. Ez az elvárt működés.
* Ha a fürt létrejött, az FQDN nem oldható fel a hálózaton, a DNS sikertelen lesz.
    * Megoldás: Hozza létre újra a helyi fürtöt, az elsődleges IP-címe a host használatával. Ez az elvárt működés.
* Amikor hibakeresést egy tároló, docker-naplók csak akkor jelenik meg a Visual Studio kimeneti ablakában, nem Service Fabric API-kon keresztül, beleértve a Service Fabric Explorer

## <a name="debug-a-net-application-running-in-docker-containers-on-service-fabric"></a>A Service Fabric docker-tárolókban futó .NET-alkalmazás hibakeresése

1. Futtassa a Visual Studiót rendszergazdaként.

1. Nyissa meg a meglévő .NET-alkalmazás, vagy hozzon létre egy újat.

1. Kattintson a jobb gombbal a projektre, és válassza ki **Hozzáadás -> tároló az Orchestrator-támogatás a Service Fabric ->**

1. Nyomja meg **F5** az alkalmazás hibakeresésének elkezdéséhez.

    A Visual Studio támogatja a .NET és .NET Core konzol és az ASP.NET-projekt típusa.

## <a name="next-steps"></a>További lépések
További információ ezekről a képességekről, a Service Fabric és a tárolók, a hivatkozásra: [a Service Fabric-tárolók áttekintése](service-fabric-containers-overview.md).