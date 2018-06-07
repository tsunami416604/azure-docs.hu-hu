---
title: Windows-tárolók a Service Fabric és a Visual STUDIO hibakeresési |} Microsoft Docs
description: Útmutató a hibakereséshez a Windows Azure Service Fabric használatával a Visual Studio 2017 a tárolók.
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
ms.openlocfilehash: bca33fe187668d38d4451b2de5b9e54d86e40ba9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655249"
---
# <a name="how-to-debug-windows-containers-in-azure-service-fabric-using-visual-studio-2017"></a>Útmutató: Windows tárolókat az Azure Service Fabric használatával a Visual Studio 2017 hibakeresése

A Visual Studio 2017 frissítés 7 (15.7) a tárolókban lévő .NET-alkalmazások hibakeresését is a Service Fabric szolgáltatásként. Ez a cikk bemutatja, hogyan konfigurálja a környezetet, és ezután a futó helyi Service Fabric-fürt tároló .NET alkalmazások hibakeresése.

## <a name="prerequisites"></a>Előfeltételek

* Windows 10, hajtsa végre ezt a gyors üzembe helyezés [konfigurálása Windows 10-es Windows tárolók futtatásához](https://docs.microsoft.com/en-us/virtualization/windowscontainers/quick-start/quick-start-windows-10)
* A Windows Server 2016 hajtsa végre ezt a gyors üzembe helyezés [Windows 2016 konfigurálása a Windows-tárolók futtatásához](https://docs.microsoft.com/en-us/virtualization/windowscontainers/quick-start/quick-start-windows-server)
* Állítsa be a helyi Service Fabric-környezetet a következő [Windows a fejlesztőkörnyezet előkészítése](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started)

## <a name="configure-your-developer-environment-to-debug-containers"></a>A fejlesztői környezet hibakeresési tárolók konfigurálása

1. Ellenőrizze, hogy a Docker ablak szolgáltatás fut a következő lépés végrehajtása előtt.

1. DNS-feloldás tárolók közötti támogatásához akkor állíthatja be a helyi fejlesztési fürtöt, a számítógépnév használatával.
    1. Rendszergazdaként nyissa meg PowerShell
    1. Keresse meg a fürt SDK telepítési mappáját, általában `C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup`
    1. Futtassa a parancsfájlt `DevClusterSetup.ps1` paraméterrel `-UseMachineName`

    ``` PowerShell
      C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1 -UseMachineName
    ```

    > [!NOTE]
    > Használhatja a `-CreateOneNodeCluster` beállítani egy egy csomópontos fürtre. Az alapértelmezett helyi öt csomópontból álló fürt hoz létre.
    >

    A DNS-szolgáltatás a Service Fabric kapcsolatos további információkért lásd: [DNS-szolgáltatás az Azure Service Fabric](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-dnsservice).

### <a name="known-limitations-when-debugging-containers-in-service-fabric"></a>Amikor a hibakeresést a Service Fabric a tárolók ismert korlátozásai

Alább az ismert korlátozásai a Service Fabric és a lehetséges megoldások a hibakeresési tárolók listája:

* Localhost használ ClusterFQDNorIP nem támogatják az DNS-feloldás tárolókban lévő.
    * Megoldás: A helyi gép nevét (lásd fent) használatával fürt beállítása
* A virtuális gépen futó Windows10 nem kap vissza a tároló a DNS-válaszban.
    * Megoldás: UDP ellenőrzőösszeg-kiszervezés a virtuális gépek hálózati adapter az IPv4 protokoll letiltása
    * Megjegyzés: Ez csökkenti a hálózati teljesítmény a számítógépen.
    * https://github.com/Azure/service-fabric-issues/issues/1061
* A DNS-sel az alkalmazás szolgáltatások feloldása szolgáltatásnév nem működik a Windows10, ha az alkalmazás lett telepítve, használja a Docker Compose
    * Megoldás: Servicename.applicationname végpontok feloldásához használja.
    * https://github.com/Azure/service-fabric-issues/issues/1062
* IP-cím ClusterFQDNorIP használja, ha a gazdagép elsődleges IP módosításakor megszakadnak DNS funkciót.
    * Megoldás: Hozza létre újra a fürtöt a gazdagépen az új elsődleges IP-cím használatával, vagy használja a számítógép nevét. Ez az elvárt működés.
* Ha a hozták létre a fürt FQDN nem oldható fel a hálózaton, a DNS sikertelen lesz.
    * Megoldás: A helyi fürt elsődleges IP-címe a host használatával hozza létre. Ez az elvárt működés.
* Egy tároló nyomkövetésére docker naplók csak akkor jelenik meg a Visual Studio kimeneti ablakában nem Service Fabric API-k segítségével, beleértve a Service Fabric Explorer

## <a name="debug-a-net-application-running-in-docker-containers-on-service-fabric"></a>A Service Fabric docker-tárolókban lévő futó .NET alkalmazások hibakeresése

1. Visual Studio futtassa rendszergazdaként.

1. Nyisson meg egy létező .NET-alkalmazás, vagy hozzon létre egy újat.

1. Kattintson jobb gombbal a projektre, és válassza ki **Hozzáadás -> tároló Orchestrator támogatás -> Service Fabric**

1. Nyomja le az **F5** az alkalmazás hibakeresését végzi el.

    Visual Studio .NET és a .NET Core konzol és az ASP.NET-projekt típusokat támogatja.

## <a name="next-steps"></a>További lépések
További információt a Service Fabric és a tárolók képességeit, a hivatkozásra: [Service Fabric-tárolók áttekintése](service-fabric-containers-overview.md).