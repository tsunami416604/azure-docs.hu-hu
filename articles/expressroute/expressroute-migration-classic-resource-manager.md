---
title: 'Azure ExpressRoute: klasszikus virtuális hálózatok migrálása a Resource Managerbe'
description: Ezen a lapon a kapcsolatcsoport áthelyezése után Resource Manager virtuális hálózatokat az ExpressRoute-kapcsolódó áttelepítését mutatja.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: cherylmc
ms.openlocfilehash: 8033c80b72c19a9473ce7ecfaa8fe5a1da9f12ee
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2020
ms.locfileid: "77061314"
---
# <a name="migrate-expressroute-associated-virtual-networks-from-classic-to-resource-manager"></a>Az ExpressRoute-kapcsolódó virtuális hálózatok áttelepítése klasszikusról Resource Manager

Ez a cikk bemutatja, hogyan migrálhat az ExpressRoute-kapcsolódó virtuális hálózatok a klasszikus üzemi modellben az Azure Resource Manager-alapú üzemi modellbe helyezze át az ExpressRoute-kapcsolatcsoport. 

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Ellenőrizze, hogy rendelkezik-e a Azure PowerShell modulok legújabb verzióival. További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azure/overview) foglalkozó témakörben talál. A PowerShell Service Management modul (amely a klasszikus üzemi modellhez szükséges) telepítéséhez tekintse meg [a Azure PowerShell Service Management modul telepítését](/powershell/azure/servicemanagement/install-azure-ps)ismertető témakört.
* A konfigurálás megkezdése előtt ellenőrizze, hogy áttekintette-e az [előfeltételeket](expressroute-prerequisites.md), az [útválasztási követelményeket](expressroute-routing.md)és a [munkafolyamatokat](expressroute-workflows.md) .
* Tekintse át a [ExpressRoute-áramkör áthelyezése a Klasszikusból a Resource Managerbe](expressroute-move.md)című témakörben megadott információkat. Győződjön meg arról, hogy megértette a korlátok és korlátozások.
* Ellenőrizze, hogy a kapcsolatcsoport teljesen működőképes, a klasszikus üzemi modellben.
* Győződjön meg arról, hogy a Resource Manager-alapú üzemi modellben létrehozott erőforráscsoport.
* Tekintse át a következő erőforrás-áttelepítési dokumentáció:

    * [Platform által támogatott IaaS-erőforrások áttelepítése a Klasszikusból a Azure Resource Managerba](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [Részletes műszaki útmutató a klasszikusból az Azure Resource Manager-alapú üzemi modellbe történő, platform által támogatott migrálásról](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
    * [Gyakori kérdések: a platform által támogatott IaaS-erőforrások áttelepítése klasszikusról Azure Resource Managerra](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [A leggyakoribb áttelepítési hibák és enyhítések áttekintése](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="supported-and-unsupported-scenarios"></a>Támogatott és nem támogatott forgatókönyvek

* Egy ExpressRoute-kapcsolatcsoportot is áthelyezhető a klasszikusból a Resource Manager-környezet állásidő nélkül. Bármely ExpressRoute-kapcsolatcsoportot a klasszikusból a Resource Manager-környezet üzemkimaradás nélkül áthelyezheti. Kövesse a ExpressRoute- [áramkörök Klasszikusból a Resource Manager-alapú üzemi modellbe való áthelyezésének](expressroute-howto-move-arm.md)utasításait a PowerShell használatával. Ez feltétele át kell helyeznie az erőforrásokat a virtuális hálózathoz csatlakozik.
* Virtuális hálózatok, az átjárók és a kapcsolódó központi telepítések a virtuális hálózat ExpressRoute-kapcsolatcsoport ugyanahhoz az előfizetéshez kapcsolódó áttelepíthetők a Resource Manager-környezet állásidő nélkül. Követheti az erőforrásokat, például a virtuális hálózatok, az átjárók és a virtuális hálózaton belül üzembe helyezett virtuális gépek áttelepítése a később ismertetett lépéseket. Biztosítania kell, hogy a virtuális hálózatok vannak konfigurálva, mielőtt azokat áttelepítené. 
* Virtuális hálózatok, az átjárók és a kapcsolódó központi telepítések a virtuális hálózaton belül, amelyek nem az ExpressRoute-kapcsolatcsoport az azonos előfizetésben kell némi állásidővel a migrálás befejezéséhez. A dokumentum utolsó szakasza ismerteti, hogyan migrálhat erőforrásokat kell követni.
* Az ExpressRoute-átjáró és a VPN Gateway virtuális hálózat nem migrálható.
* A ExpressRoute áramkör-előfizetések közötti áttelepítés nem támogatott. További információ: [Microsoft. Network Move support](../azure-resource-manager/management/move-support-resources.md#microsoftnetwork).

## <a name="move-an-expressroute-circuit-from-classic-to-resource-manager"></a>ExpressRoute-Kapcsolatcsoportok áthelyezése Kapcsolatcsoportok módosítása klasszikusról Resource Manager
Át kell helyeznie egy ExpressRoute-kapcsolatcsoportot a klasszikusból a Resource Manager-környezetből az ExpressRoute-kapcsolatcsoporthoz csatlakoztatott erőforrások áttelepítése előtt. Ennek a feladatnak, tekintse meg a következő cikkeket:

* Tekintse át a [ExpressRoute-áramkör áthelyezése a Klasszikusból a Resource Managerbe](expressroute-move.md)című témakörben megadott információkat.
* [Áramkör áthelyezése a Klasszikusból a Resource managerbe Azure PowerShell használatával](expressroute-howto-move-arm.md).
* Az Azure Service Management portált használja. A munkafolyamatot követve [létrehozhat egy új ExpressRoute áramkört](expressroute-howto-circuit-portal-resource-manager.md) , és kiválaszthatja az importálás lehetőséget. 

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
* [Platform által támogatott IaaS-erőforrások áttelepítése a Klasszikusból a Azure Resource Managerba](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [Részletes műszaki útmutató a klasszikusból az Azure Resource Manager-alapú üzemi modellbe történő, platform által támogatott migrálásról](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
* [Gyakori kérdések: a platform által támogatott IaaS-erőforrások áttelepítése klasszikusról Azure Resource Managerra](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [A leggyakoribb áttelepítési hibák és enyhítések áttekintése](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
