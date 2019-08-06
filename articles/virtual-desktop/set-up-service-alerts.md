---
title: Szolgáltatási riasztások beállítása a Windows rendszerű virtuális asztali környezethez – Azure
description: Azure Service Health beállítása a szolgáltatás értesítéseinek fogadására a Windows rendszerű virtuális asztali számítógépeken.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: helohr
ms.openlocfilehash: cbd55d3243426f2e6ec84986a2147ff94574bdda
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816379"
---
# <a name="tutorial-set-up-service-alerts"></a>Oktatóanyag: Szolgáltatásriasztások beállítása

A Azure Service Health segítségével figyelheti a Windows rendszerű virtuális asztali szolgáltatással kapcsolatos problémákat és az állapot-tanácsadókat. A Azure Service Health különböző típusú riasztásokat (például e-maileket vagy SMS-ket) tud értesíteni, segít megérteni a probléma hatását, és a probléma megoldásakor naprakészen tarthatja a frissítést. A Azure Service Health az állásidő enyhítését, valamint a tervezett karbantartás és az erőforrások rendelkezésre állását befolyásoló változások előkészítését is segíti.

Az oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Szolgáltatási riasztások létrehozása és konfigurálása.

Ha többet szeretne megtudni a Azure Service Healthről, tekintse meg az [Azure Health dokumentációját](https://docs.microsoft.com/azure/service-health/).

## <a name="prerequisites"></a>Előfeltételek

- [Oktatóanyag: Bérlő létrehozása a Windows rendszerű virtuális asztali verzióban – előzetes verzió](https://docs.microsoft.com/azure/virtual-desktop/tenant-setup-azure-active-directory)
- [Oktatóanyag: Egyszerű szolgáltatások és szerepkör-hozzárendelések létrehozása a PowerShell-lel](https://docs.microsoft.com/azure/virtual-desktop/create-service-principal-role-powershell)
- [Oktatóanyag: Állomáslista létrehozása az Azure Marketplace-szel](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-azure-marketplace)

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
> [Kapcsolódás a Távoli asztal-ügyfélhez Windows 7 és Windows 10 rendszeren](./connect-windows-7-and-10.md)
