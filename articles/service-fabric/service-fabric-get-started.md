---
title: Windows-fejlesztői környezet beállítása
description: Telepítse a futtatókörnyezetet, az SDK-t és az eszközöket, majd hozzon létre egy helyi fejlesztési fürtöt. A beállítás befejezése után készen áll az alkalmazások létrehozására Windows rendszeren.
author: peterpogorski
ms.topic: conceptual
ms.date: 03/02/2020
ms.custom: sfrev
ms.openlocfilehash: f08c6b0675475b4e15ce6db3a9dbe0e2863b9ddb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252770"
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
> - Alapértelmezés szerint a Windows 7 csak a Windows PowerShell 2.0-t tartalmazza. A Service Fabric PowerShell-parancsmagokhoz a PowerShell 3.0 vagy újabb verziója szükséges. A [Windows PowerShell 5.1 letölthető][powershell5-download] a Microsoft letöltőközpontból.
> - A Service Fabric fordított proxyja nem érhető el Windows 7 rendszeren.

## <a name="install-the-sdk-and-tools"></a>Az SDK és az eszközök telepítése

Az SDK és az eszközök telepítésének ajánlott módja a Web Platform Installer (WebPI). Ha futásidejű hibákat kap a WebPI használatával, a telepítőkre mutató közvetlen hivatkozásokat is megkereshet idáig egy adott Service Fabric-kiadás kiadási megjegyzésekben. A kibocsátási megjegyzések megtalálhatók a különböző kiadási közlemények a [Service Fabric csapat blog](https://blogs.msdn.microsoft.com/azureservicefabric/).

> [!NOTE]
> A helyi service fabric fejlesztési fürt frissítései nem támogatottak.

### <a name="to-use-visual-studio-2017-or-2019"></a>A Visual Studio 2017 vagy 2019 használata

A Service Fabric-eszközök az Azure Development számítási feladat részét képezik a Visual Studio 2017-ben és 2019-ben. A Visual Studio telepítésének részeként engedélyezze ezt a munkafolyamatot.
Emellett telepítenie kell a Microsoft Azure Service Fabric SDK-t és futtatókörnyezetet is a webplatform-telepítővel.

* [A Microsoft Azure Service Fabric SDK telepítése][core-sdk]

### <a name="to-use-visual-studio-2015-requires-visual-studio-2015-update-2-or-later"></a>A Visual Studio 2015 használata (Visual Studio 2015 2. frissítés vagy újabb szükséges)

A Visual Studio 2015 esetében a Service Fabric-eszközök az SDK-val és a futtatókörnyezettel együtt települnek a webplatform-telepítő használatával:

* [A Microsoft Azure Service Fabric SDK és eszközök telepítése][full-bundle-vs2015]

### <a name="sdk-installation-only"></a>Csak az SDK telepítése

Ha csak az SDK-ra van szükség, telepítse a következő csomagot:

* [A Microsoft Azure Service Fabric SDK telepítése][core-sdk]

Az aktuális verziók a következők:

* Service Fabric SDK és tools 4.0.470
* Service Fabric futásidejű 7.0.470

A támogatott verziók listáját a [Service Fabric-verziók című témakörben található.](service-fabric-versions.md)

> [!NOTE]
> Az egygépes fürtök (OneBox) nem támogatottak az alkalmazás- vagy fürtfrissítésekhez; törölje a OneBox-fürtöt, és hozza létre újra, ha fürtfrissítést kell végrehajtania, vagy ha alkalmazásfrissítést kell végrehajtania. 

## <a name="enable-powershell-script-execution"></a>A PowerShell-parancsfájl végrehajtásának engedélyezése

A Service Fabric Windows PowerShell-parancsfájlokat használ a helyi fejlesztési fürtök létrehozásához és az alkalmazások Visual Studióból történő üzembe helyezéséhez. Alapértelmezés szerint a Windows blokkolja ezen szkriptek futását. Az engedélyezésükhöz módosítania kell a PowerShell végrehajtási házirendjét. Nyissa meg a PowerShellt rendszergazdaként, és írja be a következő parancsot:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## <a name="install-docker-optional"></a>A Docker telepítése (nem kötelező)

[A Service Fabric egy tároló vezénylő](service-fabric-containers-overview.md) gépfürtön üzembe helyező. A Windows-tárolóalkalmazások helyi fejlesztési fürtön való futtatásához először telepítenie kell a Docker for Windows alkalmazást. Get [Docker CE For Windows (stabil)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description). Miután telepítette és elindította a Dockert, kattintson a jobb gombbal a tálca ikonjára, és válassza a **Switch to Windows containers** (Váltás Windows-tárolókra) lehetőséget. Ez a lépés szükséges ahhoz, hogy Windows-alapú Docker-rendszerképeket tudjon futtatni.

## <a name="next-steps"></a>További lépések

Most, hogy végzett a fejlesztőkörnyezet beállításával, belefoghat az alkalmazások létrehozásába és futtatásába.

* [Útmutató alkalmazások létrehozásához, telepítéséhez és kezeléséhez](service-fabric-tutorial-create-dotnet-app.md)
* [További tudnivalók a programozási modellekről: Reliable Services és Reliable Actors](service-fabric-choose-framework.md)
* [A Service Fabric mintakódjainak megtekintése a GitHubon](https://aka.ms/servicefabricsamples)
* [A fürt megjelenítése a Service Fabric Explorerrel](service-fabric-visualizing-your-cluster.md)
* A [Service Fabric támogatási lehetőségeinek](service-fabric-support.md) ismertetése

[1]: https://azure.microsoft.com/campaigns/service-fabric/ "A Service Fabric kampányoldala"
[2]: https://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "VS 2015 WebPI-hivatkozás"
[full-bundle-dev15]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Dev15 WebPI-hivatkozás"
[core-sdk]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Core SDK WebPI-hivatkozás"
[powershell5-download]:https://www.microsoft.com/download/details.aspx?id=54616
