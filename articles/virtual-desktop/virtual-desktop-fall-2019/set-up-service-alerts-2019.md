---
title: Szolgáltatási riasztások beállítása a Windows rendszerű virtuális asztalhoz – Fall 2019 – Azure
description: Azure Service Health beállítása a szolgáltatás értesítéseinek fogadására a Windows rendszerű virtuális asztali számítógépeken.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 05/27/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5b3a29e97fbdf2579e2ef6a86b8dadf91f679366
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87085826"
---
# <a name="tutorial-set-up-service-alerts---fall-2019"></a>Oktatóanyag: szolgáltatási riasztások beállítása – Fall 2019

>[!IMPORTANT]
>Ez a tartalom a Fall 2019 kiadásra vonatkozik, amely nem támogatja a Windows rendszerű virtuális asztali objektumokat Azure Resource Manager. Ha a Spring 2020 Update szolgáltatásban bevezetett Azure Resource Manager Windows rendszerű virtuális asztali objektumokat szeretne felügyelni, tekintse meg [ezt a cikket](../set-up-service-alerts.md).

A Azure Service Health segítségével figyelheti a Windows rendszerű virtuális asztali szolgáltatással kapcsolatos problémákat és az állapot-tanácsadókat. A Azure Service Health különböző típusú riasztásokat (például e-maileket vagy SMS-ket) tud értesíteni, segít megérteni a probléma hatását, és a probléma megoldásakor naprakészen tarthatja a frissítést. A Azure Service Health az állásidő enyhítését, valamint a tervezett karbantartás és az erőforrások rendelkezésre állását befolyásoló változások előkészítését is segíti.

Az oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Szolgáltatási riasztások létrehozása és konfigurálása.

Ha többet szeretne megtudni a Azure Service Healthről, tekintse meg az [Azure Health dokumentációját](https://docs.microsoft.com/azure/service-health/).

## <a name="prerequisites"></a>Előfeltételek

- [Oktatóanyag: bérlő létrehozása a Windows Virtual Desktopban](tenant-setup-azure-active-directory.md)
- [Oktatóanyag: egyszerű szolgáltatások és szerepkör-hozzárendelések létrehozása a PowerShell-lel](create-service-principal-role-powershell.md)
- [Oktatóanyag: állomáslista létrehozása az Azure Marketplace-szel](create-host-pools-azure-marketplace-2019.md)

## <a name="create-service-alerts"></a>Szolgáltatás-riasztások létrehozása

Ebből a szakaszból megtudhatja, hogyan konfigurálhatja a Azure Service Health és hogyan állíthatja be az értesítéseket, amelyeket a Azure Portal érhet el. Különböző típusú riasztásokat állíthat be, és ütemezhet úgy, hogy időben értesítse Önt.

### <a name="recommended-service-alerts"></a>Ajánlott szolgáltatási riasztások

Javasoljuk, hogy hozzon létre szolgáltatási riasztásokat a következő állapot-események típusaihoz:

- **Szolgáltatási probléma:** Értesítéseket kaphat olyan főbb problémákról, amelyek hatással vannak a felhasználók és a szolgáltatás közötti kapcsolatra, illetve a Windows rendszerű virtuális asztali bérlők felügyeletére.
- **Egészségügyi tanácsadó:** A beavatkozást igénylő értesítések fogadása. Az alábbiakban néhány példát láthat az ilyen típusú értesítésekre:
    - Virtual Machines (VM) nem biztonságos módon konfigurálva a 3389-as Open-portként
    - A funkciók elavulása

### <a name="configure-service-alerts"></a>Szolgáltatási riasztások konfigurálása

A szolgáltatási riasztások konfigurálása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Válassza a **Service Health lehetőséget.**
3. A riasztások és értesítések beállításához használja a [műveletnapló riasztások létrehozása a szolgáltatás értesítéseire](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log-service-notifications?toc=%2Fazure%2Fservice-health%2Ftoc.json#alert-and-new-action-group-using-azure-portal) című témakör utasításait.

## <a name="next-steps"></a>További lépések

Ebből az oktatóanyagból megtudhatta, hogyan állíthatja be és használhatja a Azure Service Health a szolgáltatással kapcsolatos problémák és a Windows rendszerű virtuális asztalok állapot-tanácsadói figyelésére. Ha szeretné megtudni, hogyan jelentkezhet be a Windows rendszerű virtuális asztalra, folytassa a Kapcsolódás a Windows rendszerű virtuális asztali gépekhez című témakört.

> [!div class="nextstepaction"]
> [Kapcsolódás a Távoli asztal-ügyfélhez Windows 7 és Windows 10 rendszeren](connect-windows-7-10-2019.md)
