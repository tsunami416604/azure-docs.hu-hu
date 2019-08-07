---
title: Az Azure mikroszolgáltatások Windows fejlesztési környezetének kialakítása | Microsoft Docs
description: Telepítse a futtatókörnyezetet, az SDK-t és az eszközöket, majd hozzon létre egy helyi fejlesztési fürtöt. A beállítás befejezése után készen áll az alkalmazások létrehozására Windows rendszeren.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: b94e2d2e-435c-474a-ae34-4adecd0e6f8f
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/08/2019
ms.author: atsenthi
ms.openlocfilehash: 2e19a7c267131de845143b681457966ed884a565
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2019
ms.locfileid: "68775304"
---
# <a name="prepare-your-development-environment-on-windows"></a>A fejlesztőkörnyezet előkészítése Windowson
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md) 
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
> 
> 

Az [Azure Service Fabric-alkalmazások][1] a Windows fejlesztői gépen való létrehozásához és futtatásához telepítse a Service Fabric futtatókörnyezetet, az SDK-t és az eszközöket. Továbbá [engedélyeznie kell az SDK-ban található Windows PowerShell-szkriptek](#enable-powershell-script-execution) végrehajtását.

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
> - Alapértelmezés szerint a Windows 7 csak a Windows PowerShell 2.0-t tartalmazza. A Service Fabric PowerShell-parancsmagokhoz a PowerShell 3.0 vagy újabb verziója szükséges. A [Windows PowerShell 5,0][powershell5-download] a Microsoft letöltőközpontból tölthető le.
> - A Service Fabric fordított proxyja nem érhető el Windows 7 rendszeren.
>

## <a name="install-the-sdk-and-tools"></a>Az SDK és az eszközök telepítése
A webplatform-telepítő (WebPI) az SDK és az eszközök telepítésének ajánlott módja. Ha futásidejű hibákat kap a WebPI használatával, az adott Service Fabric kiadás kibocsátási megjegyzései között közvetlen hivatkozásokat is találhat a telepítőknek. A kibocsátási megjegyzések a [Service Fabric csapat blogjának](https://blogs.msdn.microsoft.com/azureservicefabric/)különböző kiadási hirdetményekben találhatók.

> [!NOTE]
> A helyi Service Fabric fejlesztési fürtök frissítése nem támogatott.

### <a name="to-use-visual-studio-2017-or-2019"></a>A Visual Studio 2017 vagy a 2019 használata
A Service Fabric-eszközök a Visual Studio 2017-es és 2019-es verziójának Azure-fejlesztési számítási feladatának részét képezik. A Visual Studio telepítésének részeként engedélyezze ezt a munkafolyamatot.
Emellett telepítenie kell a Microsoft Azure Service Fabric SDK-t és futtatókörnyezetet is a webplatform-telepítővel.

* [Telepítse a Microsoft Azure Service Fabric SDK-t][core-sdk]

### <a name="to-use-visual-studio-2015-requires-visual-studio-2015-update-2-or-later"></a>A Visual Studio 2015 használata (Visual Studio 2015 2. frissítés vagy újabb szükséges)
A Visual Studio 2015 esetében a Service Fabric-eszközök az SDK-val és a futtatókörnyezettel együtt települnek a webplatform-telepítő használatával:

* [A Microsoft Azure Service Fabric SDK és eszközök telepítése][full-bundle-vs2015]

### <a name="sdk-installation-only"></a>Csak az SDK telepítése
Ha csak az SDK-ra van szükség, telepítse a következő csomagot:
* [Telepítse a Microsoft Azure Service Fabric SDK-t][core-sdk]

Az aktuális verziók a következők:
* Service Fabric SDK és eszközök 3.4.658
* Service Fabric futtatókörnyezet 6.5.658
* Service Fabric Tools for Visual Studio 2015 2.5.20615.1
* A Visual Studio 2017 15,9 Service Fabric Tools for Visual Studio 2.4.11024.1 tartalmaz 
* A Visual Studio 2019 16,1 Service Fabric Tools for Visual Studio 2.5.20423.3 tartalmaz

A támogatott verziók listáját lásd: [Service Fabric verziók](service-fabric-versions.md)

> [!NOTE]
> Az alkalmazás-vagy fürt frissítései nem támogatják az egyetlen gépi fürtöket (beépített); törölje a beépített-fürtöt, és hozza létre újra, ha a fürt frissítését kell végrehajtania, vagy ha problémába ütközik az alkalmazások frissítésével. 

## <a name="enable-powershell-script-execution"></a>A PowerShell-parancsfájl végrehajtásának engedélyezése
A Service Fabric Windows PowerShell-parancsfájlokat használ a helyi fejlesztési fürtök létrehozásához és az alkalmazások Visual Studióból történő üzembe helyezéséhez. Alapértelmezés szerint a Windows blokkolja ezen szkriptek futását. Az engedélyezésükhöz módosítania kell a PowerShell végrehajtási házirendjét. Nyissa meg a PowerShellt rendszergazdaként, és írja be a következő parancsot:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```
## <a name="install-docker-optional"></a>A Docker telepítése (nem kötelező)
A [Service Fabric egy tároló Orchestrator](service-fabric-containers-overview.md) , amely a gépek egy fürtön való üzembe helyezését végzi. A Windows-tároló alkalmazások helyi fejlesztési fürtön való futtatásához először telepítenie kell a Docker for Windowst. Szerezze [be a Windows rendszerhez készült Docker CE-t (stable)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description). Miután telepítette és elindította a Dockert, kattintson a jobb gombbal a tálca ikonjára, és válassza a **Switch to Windows containers** (Váltás Windows-tárolókra) lehetőséget. Ez a lépés szükséges ahhoz, hogy Windows-alapú Docker-rendszerképeket tudjon futtatni.

## <a name="next-steps"></a>További lépések
Most, hogy végzett a fejlesztőkörnyezet beállításával, belefoghat az alkalmazások létrehozásába és futtatásába.

* [Ismerje meg, hogyan hozhat létre, helyezhet üzembe és kezelhet alkalmazásokat](service-fabric-tutorial-create-dotnet-app.md)
* [További információ a programozási modellekről: Reliable Services és Reliable Actors](service-fabric-choose-framework.md)
* [A Service Fabric mintakódjainak megtekintése a GitHubon](https://aka.ms/servicefabricsamples)
* [A fürt megjelenítése a Service Fabric Explorer segítségével](service-fabric-visualizing-your-cluster.md)
* A [Service Fabric támogatási lehetőségeinek](service-fabric-support.md) ismertetése

[1]: https://azure.microsoft.com/campaigns/service-fabric/ "A Service Fabric kampányoldala"
[2]: https://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "VS 2015 WebPI-hivatkozás"
[full-bundle-dev15]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Dev15 WebPI-hivatkozás"
[core-sdk]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Core SDK WebPI-hivatkozás"
[powershell5-download]:https://www.microsoft.com/en-us/download/details.aspx?id=50395
