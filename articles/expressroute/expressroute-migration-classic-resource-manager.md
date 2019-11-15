---
title: 'Azure ExpressRoute: klasszikus virtuális hálózatok migrálása a Resource Managerbe'
description: Ezen a lapon a kapcsolatcsoport áthelyezése után Resource Manager virtuális hálózatokat az ExpressRoute-kapcsolódó áttelepítését mutatja.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: cherylmc
ms.openlocfilehash: 2b74523f42a1f57805388aa8c60cf1ad5b1d1331
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74080088"
---
# <a name="migrate-expressroute-associated-virtual-networks-from-classic-to-resource-manager"></a>Az ExpressRoute-kapcsolódó virtuális hálózatok áttelepítése klasszikusról Resource Manager

Ez a cikk bemutatja, hogyan migrálhat az ExpressRoute-kapcsolódó virtuális hálózatok a klasszikus üzemi modellben az Azure Resource Manager-alapú üzemi modellbe helyezze át az ExpressRoute-kapcsolatcsoport. 

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Ellenőrizze, hogy rendelkezik-e a Azure PowerShell modulok legújabb verzióival. További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azure/overview) foglalkozó témakörben talál. A PowerShell Service Management modul (amely a klasszikus üzemi modellhez szükséges) telepítéséhez tekintse meg [a Azure PowerShell Service Management modul telepítését](/powershell/azure/servicemanagement/install-azure-ps)ismertető témakört.
* Győződjön meg arról, hogy áttekintette a [Előfeltételek](expressroute-prerequisites.md), [útválasztási követelmények](expressroute-routing.md), és [munkafolyamatok](expressroute-workflows.md) konfigurálás megkezdése előtt.
* Tekintse át az adatokat a megadott [az ExpressRoute-Kapcsolatcsoportok áthelyezése klasszikusból Resource Manager](expressroute-move.md). Győződjön meg arról, hogy megértette a korlátok és korlátozások.
* Ellenőrizze, hogy a kapcsolatcsoport teljesen működőképes, a klasszikus üzemi modellben.
* Győződjön meg arról, hogy a Resource Manager-alapú üzemi modellben létrehozott erőforráscsoport.
* Tekintse át a következő erőforrás-áttelepítési dokumentáció:

    * [A platform által támogatott áttelepítés IaaS-erőforrások klasszikusból Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [Részletes műszaki útmutató a klasszikusból az Azure Resource Manager-alapú üzemi modellbe történő, platform által támogatott migrálásról](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
    * [– Gyakori kérdések: Az IaaS-erőforrások klasszikusból Azure Resource Manager a Platform által támogatott áttelepítés](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [Tekintse át a leggyakoribb áttelepítési hibák és megoldások](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="supported-and-unsupported-scenarios"></a>Támogatott és nem támogatott forgatókönyvek

* Egy ExpressRoute-kapcsolatcsoportot is áthelyezhető a klasszikusból a Resource Manager-környezet állásidő nélkül. Bármely ExpressRoute-kapcsolatcsoportot a klasszikusból a Resource Manager-környezet üzemkimaradás nélkül áthelyezheti. Kövesse a [ExpressRoute-Kapcsolatcsoportok áthelyezése a klasszikusból a Resource Manager-alapú üzemi modellbe, PowerShell-lel](expressroute-howto-move-arm.md). Ez feltétele át kell helyeznie az erőforrásokat a virtuális hálózathoz csatlakozik.
* Virtuális hálózatok, az átjárók és a kapcsolódó központi telepítések a virtuális hálózat ExpressRoute-kapcsolatcsoport ugyanahhoz az előfizetéshez kapcsolódó áttelepíthetők a Resource Manager-környezet állásidő nélkül. Követheti az erőforrásokat, például a virtuális hálózatok, az átjárók és a virtuális hálózaton belül üzembe helyezett virtuális gépek áttelepítése a később ismertetett lépéseket. Biztosítania kell, hogy a virtuális hálózatok vannak konfigurálva, mielőtt azokat áttelepítené. 
* Virtuális hálózatok, az átjárók és a kapcsolódó központi telepítések a virtuális hálózaton belül, amelyek nem az ExpressRoute-kapcsolatcsoport az azonos előfizetésben kell némi állásidővel a migrálás befejezéséhez. A dokumentum utolsó szakasza ismerteti, hogyan migrálhat erőforrásokat kell követni.
* Az ExpressRoute-átjáró és a VPN Gateway virtuális hálózat nem migrálható.
* A ExpressRoute áramkör-előfizetések közötti áttelepítés nem támogatott. További információ: [Microsoft. Network Move support](../azure-resource-manager/move-support-resources.md#microsoftnetwork).

## <a name="move-an-expressroute-circuit-from-classic-to-resource-manager"></a>ExpressRoute-Kapcsolatcsoportok áthelyezése Kapcsolatcsoportok módosítása klasszikusról Resource Manager
Át kell helyeznie egy ExpressRoute-kapcsolatcsoportot a klasszikusból a Resource Manager-környezetből az ExpressRoute-kapcsolatcsoporthoz csatlakoztatott erőforrások áttelepítése előtt. Ennek a feladatnak, tekintse meg a következő cikkeket:

* Tekintse át az adatokat a megadott [az ExpressRoute-Kapcsolatcsoportok áthelyezése klasszikusból Resource Manager](expressroute-move.md).
* [Kapcsolatcsoport áthelyezése Kapcsolatcsoportok módosítása klasszikusról Resource Manager Azure PowerShell-lel](expressroute-howto-move-arm.md).
* Az Azure Service Management portált használja. Kövesse a munkafolyamat [hozzon létre egy új ExpressRoute-kapcsolatcsoport](expressroute-howto-circuit-portal-resource-manager.md) , és válassza ki az importálási funkcióval. 

Ez a művelet üzemszünet nem foglalja magában. Továbbra is az adatok átviteléhez a helyszíni és a Microsoft között, amíg a migrálás folyamatban van.

## <a name="migrate-virtual-networks-gateways-and-associated-deployments"></a>Áttelepíteni a virtuális hálózatok, az átjárók és a kapcsolódó központi telepítések

Az áttelepítéséhez szükséges lépések attól függenek, az erőforrások ugyanahhoz az előfizetéshez, vagy eltérő előfizetésekben is vannak-e.

### <a name="migrate-virtual-networks-gateways-and-associated-deployments-in-the-same-subscription-as-the-expressroute-circuit"></a>Áttelepíteni a virtuális hálózatok, az átjárók és a kapcsolódó központi telepítések ugyanahhoz az előfizetéshez tartozik, az ExpressRoute-kapcsolatcsoport
Ez a szakasz ismerteti, hogyan kell áttelepíteni egy virtuális hálózati átjáró és ugyanahhoz az előfizetéshez tartozik, az ExpressRoute-kapcsolatcsoport kapcsolódó központi telepítések követni. Állásidő nélkül nem tartozik az áttelepítés. Továbbra is használja az összes erőforrást a migrálás folyamatát. A felügyeleti sík zárolva van, amíg a migrálás folyamatban van. 

1. Győződjön meg arról, hogy az ExpressRoute-kapcsolatcsoport áthelyezte a klasszikusból a Resource Manager-környezetből.
2. Győződjön meg arról, hogy a virtuális hálózat elő van készítve megfelelően az áttelepítés.
3. Regisztrálja az előfizetését, az erőforrás-migrálás. Erőforrás-migrálás az előfizetés regisztrálásához használja a következő PowerShell-kódrészlettel:

   ```powershell 
   Select-AzSubscription -SubscriptionName <Your Subscription Name>
   Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
   Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
   ```
4. Ellenőrizze, előkészítése és migrálása. A virtuális hálózat áthelyezéséhez használja a következő PowerShell-kódrészlettel:

   ```powershell
   Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
   Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
   Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
   ```

   A következő PowerShell-parancsmag futtatásával is megszakíthatja a migrálást:

   ```powershell
   Move-AzureVirtualNetwork -Abort $vnetName
   ```

## <a name="next-steps"></a>Következő lépések
* [A platform által támogatott áttelepítés IaaS-erőforrások klasszikusból Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [Részletes műszaki útmutató a klasszikusból az Azure Resource Manager-alapú üzemi modellbe történő, platform által támogatott migrálásról](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
* [– Gyakori kérdések: Az IaaS-erőforrások klasszikusból Azure Resource Manager a Platform által támogatott áttelepítés](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [Tekintse át a leggyakoribb áttelepítési hibák és megoldások](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
