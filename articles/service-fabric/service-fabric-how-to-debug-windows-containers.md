---
title: Hibakeresés Windows-tárolókon a Service Fabric és a VS használatával
description: Megtudhatja, hogy miként debugelhat windowsos tárolókat az Azure Service Fabricben a Visual Studio 2019 használatával.
ms.topic: article
ms.date: 02/14/2019
ms.author: mikhegn
ms.openlocfilehash: 2a00a352d09562ffe46dc8e6e63a5d4963ac3a3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127607"
---
# <a name="how-to-debug-windows-containers-in-azure-service-fabric-using-visual-studio-2019"></a>Útmutató: Windows-tárolók hibakeresése az Azure Service Fabric ben a Visual Studio 2019 használatával

A Visual Studio 2019 segítségével szervizhálós szolgáltatásként debugolhatja a .NET alkalmazásokat a tárolókban. Ez a cikk bemutatja, hogyan konfigurálhatja a környezetet, majd hibakeresést egy .NET-alkalmazás egy helyi Service Fabric-fürtben futó tárolóban.

## <a name="prerequisites"></a>Előfeltételek

* Windows 10-en kövesse ezt a rövid útmutatót [a Windows 10 konfigurálása windowsos tárolók futtatásához](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10)
* Windows Server 2016 rendszeren kövesse ezt a rövid útmutatót [a Windows 2016 konfigurálása Windows-tárolók futtatásához](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-server)
* A helyi Service Fabric-környezet beállítása [a Fejlesztői környezet előkészítése a Windows rendszeren](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started) című szöveget követve

## <a name="configure-your-developer-environment-to-debug-containers"></a>A fejlesztői környezet konfigurálása tárolók hibakeresésére

1. Győződjön meg arról, hogy a Docker for Window szolgáltatás fut, mielőtt folytatná a következő lépést.

1. A tárolók közötti DNS-feloldás támogatásához be kell állítania a helyi fejlesztési fürtöt a számítógép nevével. Ezekre a lépésekre akkor is szükség van, ha a fordított proxyn keresztül szeretné kezelni a szolgáltatásokat.
   1. A PowerShell megnyitása rendszergazdaként
   2. Keresse meg az SDK-fürt `C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup`telepítőmappáját, általában .
   3. A parancsfájl futtatása`DevClusterSetup.ps1`

      ``` PowerShell
        C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1
      ```

      > [!NOTE]
      > Az egycsomópontos `-CreateOneNodeCluster` fürt beállításával egy csomót is létrehozhat. Az alapértelmezett egy helyi ötcsomópontos fürtöt hoz létre.
      >

      Ha többet szeretne megtudni a DNS-szolgáltatás service fabric, olvassa el [a DNS-szolgáltatás az Azure Service Fabric.](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice) Ha többet szeretne tudni a Service Fabric fordított proxy használatáról a tárolóban futó szolgáltatásokból, olvassa el a [Proxy speciális kezelése a tárolókban futó szolgáltatások fordított kezelése.](service-fabric-reverseproxy.md#special-handling-for-services-running-in-containers)

### <a name="known-limitations-when-debugging-containers-in-service-fabric"></a>Ismert korlátozások a Service Fabric tárolóinak hibakereséséekekénél

Az alábbiakban felsoroljuk a Service Fabric tárolóinak hibakeresésére vonatkozó ismert korlátozásokat és a lehetséges megoldásokat:

* A localhost használata a ClusterFQDNorIP protokollhoz nem támogatja a DNS-feloldást a tárolókban.
    * Megoldás: A helyi fürt beállítása számítógépnévvel (lásd fent)
* A Windows10 virtuális gépen való futtatása nem kapja meg a DNS-választ a tárolóhoz.
    * Megoldás: Az IPv4 UDP-ellenőrzőösszeg-kiszervezésének letiltása a virtuális gépek hálózati adapterén
    * A Windows10 futtatása csökkenti a hálózati teljesítményt a számítógépen.
    * https://github.com/Azure/service-fabric-issues/issues/1061
* A szolgáltatások feloldása ugyanabban az alkalmazásban dns-szolgáltatásnév használatával nem működik a Windows10 rendszeren, ha az alkalmazást a Docker Compose használatával telepítették
    * Megoldás: A szolgáltatásvégpontok feloldásához használja a servicename.applicationname-t
    * https://github.com/Azure/service-fabric-issues/issues/1062
* Ha a ClusterFQDNorIP IP-címét használja, az elsődleges IP-cím módosítása az állomáson megszakítja a DNS-funkciókat.
    * Megoldás: Hozza létre újra a fürtöt az új elsődleges IP-cím használatával az állomáson, vagy használja a számítógép nevét. Ez a törés szándékos.
* Ha a fürt létrehozásához használni tervezett teljes tartományhálózat nem feloldható a hálózaton, a DNS sikertelen lesz.
    * Megoldás: Hozza létre újra a helyi fürtöt az állomás elsődleges IP-címével. Ez a hiba szándékos.
* Tároló hibakeresésekénél a docker-naplók csak a Visual Studio kimeneti ablakában lesznek elérhetők, a Service Fabric API-kon keresztül nem, beleértve a Service Fabric Explorert is.

## <a name="debug-a-net-application-running-in-docker-containers-on-service-fabric"></a>Docker-tárolókban futó .NET-alkalmazás hibakeresése a Service Fabric-en

1. Futtassa a Visual Studio alkalmazást rendszergazdaként.

1. Nyisson meg egy meglévő .NET alkalmazást, vagy hozzon létre egy újat.

1. Kattintson a jobb gombbal a projektre, és válassza **a ->-tárolóvongó támogatás hozzáadása -> Service Fabric parancsot.**

1. Nyomja **le az F5** billentyűt az alkalmazás hibakeresésének megkezdéséhez.

    A Visual Studio támogatja a konzol- és ASP.NET projekttípusokat a .NET és a .NET Core számára.

## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni a Service Fabric és a tárolók képességeiről, olvassa el a [Service Fabric-tárolók áttekintése című témakört.](service-fabric-containers-overview.md)
