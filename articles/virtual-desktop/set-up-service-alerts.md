---
title: Windows virtuális asztal – Azure szolgáltatási riasztások beállítása
description: Hogyan állítható be az Azure Service Health a Windows virtuális asztal szolgáltatási értesítések fogadásához.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: v-chjenk
ms.openlocfilehash: cae75f16da2cad453c74b7e6e9fb62dd789fe5c7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67081370"
---
# <a name="tutorial-set-up-service-alerts"></a>Oktatóanyag: A szolgáltatás értesítéseinek beállítása

Használhatja az Azure Service Health szolgáltatással kapcsolatos problémák és állapot-tanácsadási információk figyelése a Windows virtuális asztal. Az Azure Service Health értesíti a különböző típusú riasztások (például e-mail vagy SMS), hogy segítséget egy problémát, és nyomon követni a probléma megoldását hatásának megismerése. Az Azure Service Health segítségével csökkentheti az állásidőt, és előkészítheti a a tervezett karbantartásokra és az erőforrásai elérhetőségét esetleg érintő változásokra.

Az oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Hozzon létre, és a szolgáltatásriasztások konfigurálása.

Azure Service Health szolgáltatással kapcsolatos további tudnivalókért tekintse meg a [Azure Health dokumentációjából](https://docs.microsoft.com/azure/service-health/).

## <a name="prerequisites"></a>Előfeltételek

- [Oktatóanyag: Bérlő létrehozása a Windows virtuális asztal előzetes verzióban érhető el](https://docs.microsoft.com/azure/virtual-desktop/tenant-setup-azure-active-directory)
- [Oktatóanyag: Szolgáltatásnevek és szerepkör-hozzárendelések létrehozása a PowerShell használatával](https://docs.microsoft.com/azure/virtual-desktop/create-service-principal-role-powershell)
- [Oktatóanyag: Gazdagép-készlet létrehozása az Azure Marketplace-en](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-azure-marketplace)

## <a name="create-service-alerts"></a>Szolgáltatási riasztások létrehozása

Ez a szakasz bemutatja, hogyan konfigurálhatja az Azure Service Health és hogyan állítható be értesítéseket, amely az Azure Portalon érhető el. Állítsa be a riasztások különböző típusait, és időben értesíteni ütemezni.

### <a name="recommended-service-alerts"></a>Ajánlott szolgáltatás riasztások

Javasoljuk, hogy hozzon létre a következő egészségügyi eseménytípusok szolgáltatással kapcsolatos riasztások:

- **Szolgáltatási probléma:** Fontosabb problémákat, amelyek hatással a felhasználók a szolgáltatással vagy a Windows virtuális asztal bérlő kezelése lehetővé teszi az értesítések.
- **Állapottanácsadó:** Értesítések kérése, figyelmet igénylő problémák. Ez az értesítés típusát néhány példát a következők:
    - Virtuális gépek (VM), nem biztonságos módon konfigurálva, a 3389-es port megnyitása
    - Elavult funkciók

### <a name="configure-service-alerts"></a>Szolgáltatásriasztások konfigurálása

Szolgáltatásriasztások konfigurálása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Válassza ki **szolgáltatás állapotát.**
3. Kövesse az utasításokat a [tevékenységnapló-riasztások létrehozása a szolgáltatási értesítések](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log-service-notifications?toc=%2Fazure%2Fservice-health%2Ftoc.json#alert-and-new-action-group-using-azure-portal) a riasztások és értesítések beállítása.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtudhatta, hogyan állíthatja be, és használja az Azure Service Health szolgáltatással kapcsolatos problémák és állapot-tanácsadási információk megfigyelésére Windows virtuális asztali. Bejelentkezés Windows virtuális asztal kapcsolatos további információkért folytassa a Windows virtuális asztal útmutatók a kapcsolódás.

> [!div class="nextstepaction"]
> [Csatlakozás a távoli asztali ügyfél Windows 7 és Windows 10 rendszeren](./connect-windows-7-and-10.md)
