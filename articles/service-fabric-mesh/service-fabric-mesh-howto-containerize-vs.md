---
title: Meglévő .NET alkalmazás tárolóba hozása a Service Fabric Mesh számára
description: Add service fabric hálós vezénylési támogatás ASP.NET és konzol projektek, amelyek a teljes .
author: dkkapur
ms.author: dekapur
ms.date: 11/08/2018
ms.topic: conceptual
ms.openlocfilehash: d67ea5bb7df5910ec87e69adf3c414c303bf0182
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75462051"
---
# <a name="containerize-an-existing-net-app-for-service-fabric-mesh"></a>Meglévő .NET alkalmazás tárolóba hozása a Service Fabric Mesh számára

Ez a cikk bemutatja, hogyan adhat hozzá Service Fabric Mesh tároló vezénylési támogatást egy meglévő .NET alkalmazáshoz.

A Visual Studio 2017-ben tárolótámogatást adhat a ASP.NET teljes .

> [!NOTE]
> A .NET **Core** projektek jelenleg nem támogatottak.

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, kezdés előtt [létrehozhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Győződjön meg arról, hogy [beállította a fejlesztői környezetet.](service-fabric-mesh-howto-setup-developer-environment-sdk.md) Ez magában foglalja a Service Fabric futásidejű, SDK, Docker, Visual Studio 2017 telepítése és egy helyi fürt létrehozása.

## <a name="open-an-existing-net-app"></a>Meglévő .NET alkalmazás megnyitása

Nyissa meg azt az alkalmazást, amelyhez hozzá szeretné adni a tároló vezénylési támogatását.

Ha szeretne kipróbálni egy példát, használhatja az [eShop](https://github.com/MikkelHegn/ContainersSFLab) kód mintát. A cikk további részében azt feltételezi, hogy a projektet használjuk, bár ezeket a lépéseket alkalmazhatja a saját projektjére.

Szerezzen egy másolatot az **eShop** projektről:

```git
git clone https://github.com/MikkelHegn/ContainersSFLab.git
```

Miután ez a letöltés, a Visual Studio 2017 nyissa **meg a ContainersSFLab\eShopLegacyWebFormsSolution\eShopLegacyWebForms.sln**.

## <a name="add-container-support"></a>Tárolótámogatás hozzáadása
 
Tárolóvezénylési támogatás hozzáadása egy meglévő ASP.NET vagy konzolprojekthez a Service Fabric Mesh eszközök használatával az alábbiak szerint:

A Visual Studio megoldáskezelőjében kattintson a jobb gombbal a projekt nevére (a példában **az eShopLegacyWebForms),** majd válassza a **Container** > **Orchestrator támogatás hozzáadása parancsot.**
Megjelenik **a Tárolóvongvanátor támogatás hozzáadása** párbeszédpanel.

![Visual Studio tárolóvongonsen párbeszédpanel hozzáadása](./media/service-fabric-mesh-howto-containerize-vs/add-container-orchestration-support.png)

Válassza a legördülő menü **Szolgáltatásháló hálója** lehetőséget, majd kattintson az **OK**gombra.

Az eszköz ezután ellenőrzi, hogy a Docker telepítve van-e, hozzáad egy Docker-fájlt a projekthez, és lehúz egy docker-lemezképet a projekthez.  
A service fabric háló alkalmazás projekt hozzáadódik a megoldáshoz. Ez tartalmazza a Mesh közzétételi profilok és konfigurációs fájlokat. A projekt neve megegyezik a projekt nevével, és az "Alkalmazás" a végéig összevan fűzve, például **az eShopLegacyWebFormsApplication**. 

Az új Mesh projektben két mappa jelenik meg, amelyeket tudnia kell:
- **Alkalmazás-erőforrások,** amelyek yaml-fájlokat tartalmaznak, amelyek további mesh erőforrásokat, például a hálózatot írják le.
- **Service Resources,** amely egy service.yaml fájlt tartalmaz, amely leírja, hogyan kell futtatni az alkalmazást, amikor telepítve van.

Miután a tároló vezénylési támogatást hozzáadta az alkalmazáshoz, az **F5** billentyű lenyomásával hibakereséshez a .NET alkalmazás a helyi Service Fabric Mesh fürtön. Az eShop ASP.NET service fabric hálófürtön futó alkalmazás: 

![eShop alkalmazás](./media/service-fabric-mesh-howto-containerize-vs/eshop-running.png)

Most már közzéteheti az alkalmazást az Azure Service Fabric Mesh.

## <a name="next-steps"></a>További lépések

Tekintse meg, hogyan tehet közzé egy alkalmazást a Service Fabric Mesh szolgáltatásban: [Oktatóanyag – Service Fabric Mesh-alkalmazás telepítése](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)