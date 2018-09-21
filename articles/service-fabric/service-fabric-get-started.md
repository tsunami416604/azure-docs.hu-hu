---
title: Az Azure mikroszolgáltatások Windows fejlesztési környezetének kialakítása | Microsoft Docs
description: Telepítse a futtatókörnyezetet, az SDK-t és az eszközöket, majd hozzon létre egy helyi fejlesztési fürtöt. A beállítás befejezése után készen áll az alkalmazások létrehozására Windows rendszeren.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: b94e2d2e-435c-474a-ae34-4adecd0e6f8f
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/23/2018
ms.author: ryanwi
ms.openlocfilehash: ef08168bc37b636c864207e066c575e31336f609
ms.sourcegitcommit: 8b694bf803806b2f237494cd3b69f13751de9926
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2018
ms.locfileid: "46497640"
---
# <a name="prepare-your-development-environment-on-windows"></a>A fejlesztőkörnyezet előkészítése Windowson
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md) 
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
> 
> 

Az [Azure Service Fabric-alkalmazásoknak][1] a Windows rendszerű fejlesztői gépen való létrehozásához és futtatásához telepítse a Service Fabric-futtatókörnyezetet, az SDK-t és az eszközöket. Továbbá [engedélyeznie kell az SDK-ban található Windows PowerShell-szkriptek](#enable-powershell-script-execution) végrehajtását.

## <a name="prerequisites"></a>Előfeltételek
### <a name="supported-operating-system-versions"></a>Támogatott operációsrendszer-verziók
A fejlesztéshez a következő operációsrendszer-verziók támogatottak:

* Windows 7
* Windows 8/Windows 8.1
* Windows Server 2012 R2
* Windows Server 2016
* Windows 10

> [!NOTE]
> Windows 7-támogatás:
> - Alapértelmezés szerint a Windows 7 csak a Windows PowerShell 2.0-t tartalmazza. A Service Fabric PowerShell-parancsmagokhoz a PowerShell 3.0 vagy újabb verziója szükséges. A [Windows PowerShell 5.0-t letöltheti][powershell5-download] a Microsoft letöltőközpontból.
> - A Service Fabric fordított proxyja nem érhető el Windows 7 rendszeren.
>

## <a name="install-the-sdk-and-tools"></a>Az SDK és az eszközök telepítése
### <a name="to-use-visual-studio-2017"></a>A Visual Studio 2017 használata
A Service Fabric-eszközök a Visual Studio 2017 Azure Development Workload munkafolyamatának részét képezik. A Visual Studio telepítésének részeként engedélyezze ezt a munkafolyamatot.
Emellett telepítenie kell a Microsoft Azure Service Fabric SDK-t és futtatókörnyezetet is a webplatform-telepítővel.

* [A Microsoft Azure Service Fabric SDK telepítése][core-sdk]

### <a name="to-use-visual-studio-2015-requires-visual-studio-2015-update-2-or-later"></a>A Visual Studio 2015 használata (Visual Studio 2015 2. frissítés vagy újabb szükséges)
A Visual Studio 2015 esetében a Service Fabric-eszközök az SDK-val és a futtatókörnyezettel együtt települnek a webplatform-telepítő használatával:

* [A Microsoft Azure Service Fabric SDK és eszközök telepítése][full-bundle-vs2015]

### <a name="sdk-installation-only"></a>Csak az SDK telepítése
Ha csak az SDK-ra van szükség, telepítse a következő csomagot:
* [A Microsoft Azure Service Fabric SDK telepítése][core-sdk]

Az aktuális verziók a következők:
* Service Fabric SDK és eszközök 3.2.176
* Service Fabric-futtatókörnyezet 6.3.176
* Service Fabric Tools Pro Visual Studio 2015 2.3.10710.3
* Visual Studio 2017 15.7 tartalmazza a Service Fabric Tools for Visual Studio 2.3.10710.1 

A támogatott verziók listáját lásd: [Service Fabric-támogatás](service-fabric-support.md).

> [!NOTE]
> Frissíti a fürtök (beépített) nem támogatottak az alkalmazás vagy a fürt egyetlen gépen; a beépített fürtöt törölje és hozza létre újra, ha szeretné hajtsa végre a fürt frissítése, vagy problémát tapasztal az alkalmazás frissítése. 

## <a name="enable-powershell-script-execution"></a>A PowerShell-parancsfájl végrehajtásának engedélyezése
A Service Fabric Windows PowerShell-parancsfájlokat használ a helyi fejlesztési fürtök létrehozásához és az alkalmazások Visual Studióból történő üzembe helyezéséhez. Alapértelmezés szerint a Windows blokkolja ezen szkriptek futását. Az engedélyezésükhöz módosítania kell a PowerShell végrehajtási házirendjét. Nyissa meg a PowerShellt rendszergazdaként, és írja be a következő parancsot:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```
## <a name="install-docker-optional"></a>Telepítheti a Dockert (nem kötelező)
[Service Fabric, amely egy tárlóvezénylővel](service-fabric-containers-overview.md) üzembe helyezésének mikroszolgáltatásokat Vezényel számítógépfürtökön. A helyi fejlesztési fürt Windows tárolóalkalmazások futtatásához először telepítenie kell a Docker for Windows. Első [Docker CE for Windows (stable)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description). Miután telepítette és elindította a Dockert, kattintson a jobb gombbal a tálca ikonjára, és válassza a **Switch to Windows containers** (Váltás Windows-tárolókra) lehetőséget. Ez a lépés szükséges ahhoz, hogy Windows-alapú Docker-rendszerképeket tudjon futtatni.

## <a name="next-steps"></a>További lépések
Most, hogy végzett a fejlesztőkörnyezet beállításával, belefoghat az alkalmazások létrehozásába és futtatásába.

* [Az első Service Fabric-alkalmazás létrehozása a Visual Studióban](service-fabric-create-your-first-application-in-visual-studio.md)
* [Ismerje meg az alkalmazások helyi fürtön történő üzembe helyezését és kezelését](service-fabric-get-started-with-a-local-cluster.md)
* [További tudnivalók a programozási modellekről: Reliable Services és Reliable Actors](service-fabric-choose-framework.md)
* [A Service Fabric mintakódjainak megtekintése a GitHubon](https://aka.ms/servicefabricsamples)
* [A fürt megjelenítése a Service Fabric Explorer segítségével](service-fabric-visualizing-your-cluster.md)
* [A platform átfogó bemutatásának megtekintéséhez kövesse a Service Fabric képzési tervét](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)
* A [Service Fabric támogatási lehetőségeinek](service-fabric-support.md) ismertetése

[1]: https://azure.microsoft.com/campaigns/service-fabric/ "A Service Fabric kampányoldala"
[2]: https://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "VS 2015 WebPI-hivatkozás"
[full-bundle-dev15]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Dev15 WebPI-hivatkozás"
[core-sdk]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Core SDK WebPI-hivatkozás"
[powershell5-download]:https://www.microsoft.com/en-us/download/details.aspx?id=50395
