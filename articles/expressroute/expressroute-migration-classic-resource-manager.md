---
title: 'Azure ExpressRoute: Klasszikus virtuális hálózatok áttelepítése az Erőforrás-kezelőbe'
description: Ez a lap azt ismerteti, hogyan telepítheti át az ExpressRoute-hoz társított virtuális hálózatokat az Erőforrás-kezelőbe a kapcsolatcsoport áthelyezése után.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: cherylmc
ms.openlocfilehash: 8033c80b72c19a9473ce7ecfaa8fe5a1da9f12ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061314"
---
# <a name="migrate-expressroute-associated-virtual-networks-from-classic-to-resource-manager"></a>ExpressRoute-hoz társított virtuális hálózatok áttelepítése klasszikusról erőforrás-kezelőre

Ez a cikk bemutatja, hogyan telepítheti át az ExpressRoute-hoz társított virtuális hálózatokat a klasszikus üzembe helyezési modellről az Azure Resource Manager telepítési modelljére az ExpressRoute-kapcsolat áthelyezése után. 

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Ellenőrizze, hogy rendelkezik-e az Azure PowerShell-modulok legújabb verzióival. További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azure/overview) foglalkozó témakörben talál. A PowerShell szolgáltatáskezelési modul telepítéséhez (amely a klasszikus üzembe helyezési modellhez szükséges) az [Azure PowerShell szolgáltatáskezelő modul telepítése című](/powershell/azure/servicemanagement/install-azure-ps)témakörben található.
* A konfiguráció megkezdése előtt ellenőrizze, hogy áttekintette-e az [előfeltételeket,](expressroute-prerequisites.md) [az útválasztási követelményeket](expressroute-routing.md)és [a munkafolyamatokat.](expressroute-workflows.md)
* Tekintse át az [ExpressRoute-kapcsolat áthelyezése klasszikusról erőforrás-kezelőre](expressroute-move.md)című csoportban megadott információkat. Győződjön meg arról, hogy teljes mértékben megértette a korlátokat és korlátozásokat.
* Ellenőrizze, hogy az áramkör teljes mértékben működőképes-e a klasszikus üzembe helyezési modellben.
* Győződjön meg arról, hogy rendelkezik egy erőforráscsoporttal, amely et az Erőforrás-kezelő telepítési modelljében hoztak létre.
* Tekintse át az erőforrás-áttelepítés alábbi dokumentációját:

    * [Az IaaS-erőforrások platform által támogatott áttelepítése klasszikusról Azure Resource Managerre](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [Részletes műszaki útmutató a klasszikusból az Azure Resource Manager-alapú üzemi modellbe történő, platform által támogatott migrálásról](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
    * [Gyakran feltett kérdések: Az IaaS-erőforrások platform által támogatott áttelepítése klasszikusról Azure Resource Managerre](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [A leggyakoribb áttelepítési hibák és -megoldások áttekintése](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="supported-and-unsupported-scenarios"></a>Támogatott és nem támogatott forgatókönyvek

* Az ExpressRoute-áramkör a klasszikus környezetből az Erőforrás-kezelő környezetbe helyezhető át állásidő nélkül. Bármely ExpressRoute-kapcsolatcsoport áthelyezhető a klasszikus környezetből az Erőforrás-kezelő környezetbe állásidő nélkül. Kövesse az [ExpressRoute-áramkörök klasszikusról az Erőforrás-kezelő telepítési modelljére a PowerShell használatával történő áthelyezésére](expressroute-howto-move-arm.md)vonatkozó utasításokat. Ez a virtuális hálózathoz kapcsolódó erőforrások áthelyezésének előfeltétele.
* Virtuális hálózatok, átjárók és a kapcsolódó központi telepítések a virtuális hálózaton belül, amelyek kapcsolódnak egy ExpressRoute-kapcsolat ugyanazon az előfizetésben lehet áttelepíteni az Erőforrás-kezelő környezetben állásidő nélkül. A későbbiek ben ismertetett lépéseket követve áttelepítheti az erőforrásokat, például a virtuális hálózatokat, az átjárókat és a virtuális hálózaton belül üzembe helyezett virtuális gépeket. Az áttelepítés előtt meg kell győződnie arról, hogy a virtuális hálózatok megfelelően vannak konfigurálva. 
* Virtuális hálózatok, átjárók és a kapcsolódó központi telepítések a virtuális hálózaton belül, amelyek nem ugyanabban az előfizetésben, mint az ExpressRoute-kapcsolat igényel némi állásidőt az áttelepítés befejezéséhez. A dokumentum utolsó szakasza az erőforrások áttelepítéséhez követendő lépéseket ismerteti.
* Az ExpressRoute-átjáróval és vpn-átjáróval rendelkező virtuális hálózat nem telepíthető át.
* Az ExpressRoute-kapcsolat közötti előfizetések közötti áttelepítés nem támogatott. További információt a [Microsoft.Network move support című témakörben talál.](../azure-resource-manager/management/move-support-resources.md#microsoftnetwork)

## <a name="move-an-expressroute-circuit-from-classic-to-resource-manager"></a>ExpressRoute-kapcsolat áthelyezése klasszikusról erőforrás-kezelőre
Mielőtt megpróbálna áttelepíteni egy ExpressRoute-áramkört a klasszikus környezetből az Erőforrás-kezelő környezetbe, mielőtt megpróbálna áttelepíteni az ExpressRoute-kapcsolathoz kapcsolódó erőforrásokat. A feladat elvégzéséhez olvassa el az alábbi cikkeket:

* Tekintse át az [ExpressRoute-kapcsolat áthelyezése klasszikusról erőforrás-kezelőre](expressroute-move.md)című csoportban megadott információkat.
* [Az Azure PowerShell használatával áthelyezegy áramkört a klasszikusról az Erőforrás-kezelőre.](expressroute-howto-move-arm.md)
* Használja az Azure Service Management portálon. A munkafolyamat követésével [új ExpressRoute-áramkört hozhat létre,](expressroute-howto-circuit-portal-resource-manager.md) és kiválaszthatja az importálási lehetőséget. 

Ez a művelet nem jár állásidővel. Folytathatja az adatok átvitelét a telephelyés a Microsoft között, amíg az áttelepítés folyamatban van.

## <a name="migrate-virtual-networks-gateways-and-associated-deployments"></a>Virtuális hálózatok, átjárók és kapcsolódó telepítések áttelepítése

Az áttelepítéshez követendő lépések attól függnek, hogy az erőforrások ugyanabban az előfizetésben, különböző előfizetésekben vagy mindkettőben vannak-e.

### <a name="migrate-virtual-networks-gateways-and-associated-deployments-in-the-same-subscription-as-the-expressroute-circuit"></a>Virtuális hálózatok, átjárók és társított központi telepítések áttelepítése az ExpressRoute-kapcsolattal azonos előfizetésben
Ez a szakasz azokat a lépéseket ismerteti, amelyeket a virtuális hálózat, az átjáró és a kapcsolódó központi telepítések áttelepítéséhez kell követni az ExpressRoute-kapcsolattal azonos előfizetésben. Ehhez az áttelepítéshez nincs állásidő társítva. Az áttelepítési folyamat során továbbra is használhatja az összes erőforrást. A felügyeleti sík zárolva van, amíg az áttelepítés folyamatban van. 

1. Győződjön meg arról, hogy az ExpressRoute-kapcsolat a klasszikusról az Erőforrás-kezelő környezetbe került.
2. Győződjön meg arról, hogy a virtuális hálózat megfelelően van előkészítve az áttelepítéshez.
3. Regisztrálja az előfizetést az erőforrások áttelepítéséhez. Az előfizetés erőforrás-áttelepítéshez való regisztrálásához használja a következő PowerShell-kódrészletet:

   ```powershell 
   Select-AzSubscription -SubscriptionName <Your Subscription Name>
   Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
   Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
   ```
4. Érvényesítés, előkészítés és áttelepítés. A virtuális hálózat áthelyezéséhez használja a következő PowerShell-kódrészletet:

   ```powershell
   Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
   Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
   Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
   ```

   Az áttelepítésmegszakításhoz a következő PowerShell-parancsmag futtatásával is megszakíthatja:

   ```powershell
   Move-AzureVirtualNetwork -Abort $vnetName
   ```

## <a name="next-steps"></a>További lépések
* [Az IaaS-erőforrások platform által támogatott áttelepítése klasszikusról Azure Resource Managerre](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [Részletes műszaki útmutató a klasszikusból az Azure Resource Manager-alapú üzemi modellbe történő, platform által támogatott migrálásról](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
* [Gyakran feltett kérdések: Az IaaS-erőforrások platform által támogatott áttelepítése klasszikusról Azure Resource Managerre](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [A leggyakoribb áttelepítési hibák és -megoldások áttekintése](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
