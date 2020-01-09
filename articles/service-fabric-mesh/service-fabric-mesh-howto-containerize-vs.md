---
title: Meglévő .NET-alkalmazás tárolóba helyezése Service Fabric Mesh számára
description: Adja hozzá Service Fabric Mesh-tárolók előkészítésének támogatását a teljes .NET-keretrendszert használó ASP.NET és konzolos projektekhez.
author: dkkapur
ms.author: dekapur
ms.date: 11/08/2018
ms.topic: conceptual
ms.openlocfilehash: d67ea5bb7df5910ec87e69adf3c414c303bf0182
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75462051"
---
# <a name="containerize-an-existing-net-app-for-service-fabric-mesh"></a>Meglévő .NET-alkalmazás tárolóba helyezése Service Fabric Mesh számára

Ez a cikk bemutatja, hogyan adhat hozzá Service Fabric Mesh Container-előkészítési támogatást egy meglévő .NET-alkalmazáshoz.

A Visual Studio 2017-es verziójában tárolókra bontás-támogatást adhat hozzá a teljes .NET-keretrendszert használó ASP.NET és konzolos projektekhez.

> [!NOTE]
> A .NET **Core** -projektek jelenleg nem támogatottak.

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, kezdés előtt [létrehozhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Győződjön meg arról, hogy [beállította a fejlesztési környezetet](service-fabric-mesh-howto-setup-developer-environment-sdk.md). Ide tartozik a Service Fabric futtatókörnyezet, az SDK, a Docker, a Visual Studio 2017 telepítése és egy helyi fürt létrehozása.

## <a name="open-an-existing-net-app"></a>Meglévő .NET-alkalmazás megnyitása

Nyissa meg azt az alkalmazást, amelyhez hozzá szeretné adni a Container-előkészítési támogatást.

Ha példát szeretne kipróbálni, használhatja az [üzlet](https://github.com/MikkelHegn/ContainersSFLab) kód mintát. A cikk további része azt feltételezi, hogy ezt a projektet használjuk, bár ezeket a lépéseket saját projektre is alkalmazhatja.

Az **üzlet** -projekt másolatának beolvasása:

```git
git clone https://github.com/MikkelHegn/ContainersSFLab.git
```

A letöltés után a Visual Studio 2017-es verziójában nyissa meg a **ContainersSFLab\eShopLegacyWebFormsSolution\eShopLegacyWebForms.SLN**.

## <a name="add-container-support"></a>Tároló-támogatás hozzáadása
 
Adja hozzá a Container-előkészítési támogatást egy meglévő ASP.NET-vagy konzol-projekthez az Service Fabric Mesh Tools használatával a következőképpen:

A Visual Studio Solution Explorerben kattintson a jobb gombbal a projekt nevére (a példában a **eShopLegacyWebForms**), majd válassza a **Hozzáadás** > **tároló Orchestrator-támogatás**lehetőséget.
Megjelenik a **Container Orchestrator-támogatás hozzáadása** párbeszédpanel.

![A Visual Studio tároló-Orchestrator hozzáadása párbeszédpanel](./media/service-fabric-mesh-howto-containerize-vs/add-container-orchestration-support.png)

Válassza ki **Service Fabric rácsvonalat** a legördülő listából, majd kattintson **az OK**gombra.

Az eszköz ezután ellenőrzi, hogy telepítve van-e a Docker, hozzáadja a Docker a projekthez, és lekéri a projekthez tartozó Docker-rendszerképet.  
A megoldáshoz egy Service Fabric Mesh-alkalmazási projekt van hozzáadva. A háló közzétételi profiljait és konfigurációs fájljait tartalmazza. A projekt neve megegyezik a projekt nevével, az "alkalmazás" a végéhez fűzve, például **eShopLegacyWebFormsApplication**. 

Az új háló projektben két mappát fog látni:
- Olyan YAML-fájlokat tartalmazó **alkalmazás-erőforrások** , amelyek további rácsvonal-erőforrásokat, például a hálózatot írják le.
- A Service. YAML fájlt tartalmazó **szolgáltatás-erőforrások** , amelyek azt ismertetik, hogyan kell futtatni az alkalmazást az üzembe helyezéskor.

Miután hozzáadta a Container-előkészítési támogatást az alkalmazáshoz, az **F5** billentyű lenyomásával hibakeresést végezhet a .net-alkalmazáson a helyi Service Fabric Mesh-fürtön. Itt látható az Service Fabric Mesh-fürtön futó ASP.NET alkalmazás: 

![Elektronikus alkalmazás](./media/service-fabric-mesh-howto-containerize-vs/eshop-running.png)

Most már közzéteheti az alkalmazást az Azure Service Fabric Mesh szolgáltatásban.

## <a name="next-steps"></a>Következő lépések

Tekintse meg, hogyan tehet közzé egy alkalmazást a Service Fabric Meshban: [oktatóanyag – Service Fabric Mesh-alkalmazás üzembe helyezése](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)