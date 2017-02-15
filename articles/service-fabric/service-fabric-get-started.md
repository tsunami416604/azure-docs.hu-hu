---
title: "A fejlesztési környezet beállítása | Microsoft Docs"
description: "Telepítse a futtatókörnyezetet, az SDK-t és az eszközöket, majd hozzon létre egy helyi fejlesztési fürtöt. A beállítás befejezése után készen áll az alkalmazások létrehozására."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: b94e2d2e-435c-474a-ae34-4adecd0e6f8f
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/13/2016
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 04092b735fa77c72ffe6c492a3fc975eac2e99fd
ms.openlocfilehash: a71b77a320e9321eaa857acfcfae8822de0ac9e5


---
# <a name="prepare-your-development-environment"></a>A fejlesztőkörnyezet előkészítése
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md) 
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
> 
> 

 Az [Azure Service Fabric-alkalmazásoknak][1] a fejlesztői gépen való létrehozásához és futtatásához telepítse a futtatókörnyezetet, az SDK-t és az eszközöket. Továbbá engedélyeznie kell az SDK-ban található Windows PowerShell-parancsfájlok végrehajtását.

## <a name="prerequisites"></a>Előfeltételek
### <a name="supported-operating-system-versions"></a>Támogatott operációsrendszer-verziók
A fejlesztéshez a következő operációsrendszer-verziók támogatottak:

* Windows 7
* Windows 8/Windows 8.1
* Windows Server 2012 R2
* Windows 10

> [!NOTE]
> Alapértelmezés szerint a Windows 7 csak a Windows PowerShell 2.0-t tartalmazza. A Service Fabric PowerShell-parancsmagokhoz a PowerShell 3.0 vagy újabb verziója szükséges. A [Windows PowerShell 5.0-t letöltheti][powershell5-download] a Microsoft letöltőközpontból.
> 
> 

## <a name="install-the-runtime-sdk-and-tools"></a>A futtatókörnyezet, az SDK és az eszközök telepítése
A webplatform-telepítő kétféle konfigurációt kínál a Service Fabric fejlesztéséhez.

Visual Studio 2017 (az Azure Development and Management Workload telepítése szükséges):

* [A Service Fabric-futtatókörnyezet és az SDK telepítése (Visual Studio-eszközök nélkül)][core-sdk]

Visual Studio 2015 (Visual Studio 2015 2. frissítés vagy újabb szükséges):

* [A Service Fabric-futtatókörnyezet, az SDK és az eszközök telepítése][full-bundle-vs2015]
* [Csak a Service Fabric-futtatókörnyezet és az SDK telepítése (Visual Studio-eszközök nélkül)][core-sdk]

> [!WARNING]
> Néhány ügyfél hibákat jelzett, amikor a telepítés során ezeket az indítási hivatkozásokat használták, vagy amikor a hivatkozásokat Chrome böngészőben használták. Ezek a webplatform-telepítő ismert hibái, amelyek javítása folyamatban van.  Áthidaló megoldásként próbálja meg a következőket:
>- Indítsa a hivatkozásokat Internet Explorer vagy Edge böngészővel, vagy
>- Indítsa el a webplatform-telepítőt a Start menüből, keressen rá a „Service Fabric” kifejezésre, majd telepítse az SDK-t
> 
> Elnézést kérünk az okozott kellemetlenségekért. 

Az aktuális verziók a következők:
* Service Fabric SDK 2.4.145
* Service Fabric-futtatókörnyezet 5.4.145
* Visual Studio 2015-eszközök 1.4.41209

A támogatott verziók listáját lásd: [Service Fabric-támogatás](service-fabric-support.md).

## <a name="enable-powershell-script-execution"></a>A PowerShell-parancsfájl végrehajtásának engedélyezése
A Service Fabric Windows PowerShell-parancsfájlokat használ a helyi fejlesztési fürtök létrehozásához és az alkalmazások Visual Studióból történő üzembe helyezéséhez. Alapértelmezés szerint a Windows blokkolja ezen szkriptek futását. Az engedélyezésükhöz módosítania kell a PowerShell végrehajtási házirendjét. Nyissa meg a PowerShellt rendszergazdaként, és írja be a következő parancsot:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## <a name="next-steps"></a>Következő lépések
Most, hogy végzett a fejlesztőkörnyezet beállításával, belefoghat az alkalmazások létrehozásába és futtatásába.

* [Az első Service Fabric-alkalmazás létrehozása a Visual Studióban](service-fabric-create-your-first-application-in-visual-studio.md)
* [Ismerje meg az alkalmazások helyi fürtön történő üzembe helyezését és kezelését](service-fabric-get-started-with-a-local-cluster.md)
* [További tudnivalók a programozási modellekről: Reliable Services és Reliable Actors](service-fabric-choose-framework.md)
* [A Service Fabric mintakódjainak megtekintése a GitHubon](https://aka.ms/servicefabricsamples)
* [A fürt megjelenítése a Service Fabric Explorer segítségével](service-fabric-visualizing-your-cluster.md)
* [A platform átfogó bemutatásának megtekintéséhez kövesse a Service Fabric képzési tervét](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)
* A [Service Fabric támogatási lehetőségeinek](service-fabric-support.md) ismertetése

[1]: http://azure.microsoft.com/en-us/campaigns/service-fabric/ "A Service Fabric kampányoldala"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "VS 2015 WebPI-hivatkozás"
[full-bundle-dev15]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Dev15 WebPI-hivatkozás"
[core-sdk]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Core SDK WebPI-hivatkozás"
[powershell5-download]:https://www.microsoft.com/en-us/download/details.aspx?id=50395



<!--HONumber=Dec16_HO2-->


