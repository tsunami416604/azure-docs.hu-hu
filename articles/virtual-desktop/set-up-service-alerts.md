---
title: Szolgáltatásriasztások beállítása a Windows virtuális asztalhoz – Azure
description: Az Azure Service Health beállítása a Windows virtuális asztal szolgáltatásértesítéseinek fogadására.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 2834ba924fa9c29d955c38fbaeb45ab23e5c4e9b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79127711"
---
# <a name="tutorial-set-up-service-alerts"></a>Oktatóanyag: Szolgáltatásriasztások beállítása

Az Azure Service Health segítségével figyelheti a Windows virtuális asztal szolgáltatásproblémáit és egészségügyi tanácsait. Az Azure Service Health különböző típusú (például e-mail vagy SMS- értesítés) esetén értesítheti Önt, segít megérteni a probléma hatását, és a probléma megoldásával naprakészen tartani. Az Azure Service Health is segít csökkenteni az állásidőt, és felkészülni a tervezett karbantartásra és az erőforrások rendelkezésre állására vonatkozó módosításokra.

Az oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Szolgáltatásriasztások létrehozása és konfigurálása.

Ha többet szeretne megtudni az Azure Service Health szolgáltatásról, olvassa el az [Azure Health dokumentációját.](https://docs.microsoft.com/azure/service-health/)

## <a name="prerequisites"></a>Előfeltételek

- [Oktatóanyag: Bérlő létrehozása a Windows virtuális asztalon](tenant-setup-azure-active-directory.md)
- [Oktatóanyag: Szolgáltatástagok és szerepkör-hozzárendelések létrehozása a PowerShell-el](create-service-principal-role-powershell.md)
- [Oktatóanyag: Gazdagépkészlet létrehozása az Azure Piactérrel](create-host-pools-azure-marketplace.md)

## <a name="create-service-alerts"></a>Szolgáltatásriasztások létrehozása

Ez a szakasz bemutatja, hogyan konfigurálhatja az Azure Service Health-t, és hogyan állíthat be értesítéseket, amelyeket az Azure Portalon érhet el. Különböző típusú értesítéseket állíthat be, és ütemezheti őket, hogy időben értesítsék Önt.

### <a name="recommended-service-alerts"></a>Ajánlott szolgáltatási riasztások

Javasoljuk, hogy hozzon létre szolgáltatásriasztásokat a következő állapotesemény-típusokhoz:

- **Szolgáltatási probléma:** Értesítéseket kaphat olyan fontos problémákról, amelyek hatással vannak a felhasználók és a szolgáltatás közötti kapcsolatra, vagy a Windows virtuális asztal bérlőjének kezelésére.
- **Egészségügyi tanácsadás:** Értesítést kap, hogy szükség van a figyelmet. Az alábbi példák az ilyen típusú értesítésekre mutatnak be példákat:
    - A virtuális gépek (VM-ek) nincsenek biztonságosan konfigurálva 3389-es nyitott portként
    - A funkcionalitás eprecációja

### <a name="configure-service-alerts"></a>Szolgáltatásriasztások konfigurálása

Szolgáltatásriasztások konfigurálása:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
2. Válassza a **Szolgáltatás állapota lehetőséget.**
3. A [szolgáltatásértesítésektevékenység-értesítések létrehozása](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log-service-notifications?toc=%2Fazure%2Fservice-health%2Ftoc.json#alert-and-new-action-group-using-azure-portal) című útmutató utasításaival állíthatja be a riasztásokat és az értesítéseket.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan állíthatja be és használhatja az Azure Service Health szolgáltatást a Windows virtuális asztal szolgáltatási problémáinak és egészségügyi tanácsainak figyelésére. Ha többet szeretne tudni aWindows virtuális asztalra való bejelentkezésről, folytassa a Csatlakozás a Windows virtuális asztalhoz útmutatóhoz című útmutatóval.

> [!div class="nextstepaction"]
> [Csatlakozás a Távoli asztali ügyfélhez Windows 7 és Windows 10 rendszeren](./connect-windows-7-and-10.md)
