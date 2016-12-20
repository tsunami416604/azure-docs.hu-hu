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
ms.date: 10/26/2016
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 7e33e00a676f4aa7143cede3380adb58ba1d11e4
ms.openlocfilehash: 2c4a92fadaa845fc0d14e5101a87aabe8f8d2891


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
A Webplatform-telepítő két konfigurációt kínál a Service Fabric fejlesztéséhez:

* [A Service Fabric-futtatókörnyezet, az SDK és az eszközök telepítése a Visual Studio 2015 számára (a Visual Studio 2015 2. vagy újabb frissítése szükséges)][full-bundle-vs2015]
* [Csak a Service Fabric-futtatókörnyezet és az SDK telepítése (nincsenek Visual Studio-eszközök)][core-sdk]

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

[1]: http://azure.microsoft.com/en-us/campaigns/service-fabric/ "A Service Fabric kampányoldala"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "VS 2015 WebPI-hivatkozás"
[full-bundle-dev15]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Dev15 WebPI-hivatkozás"
[core-sdk]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Core SDK WebPI-hivatkozás"
[powershell5-download]:https://www.microsoft.com/en-us/download/details.aspx?id=50395



<!--HONumber=Nov16_HO4-->


