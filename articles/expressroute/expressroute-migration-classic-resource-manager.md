---
title: 'Virtuális hálózatok áttelepítése klasszikusról Resource Manager - ExpressRoute: Azure: PowerShell |} A Microsoft Docs'
description: Ezen a lapon a kapcsolatcsoport áthelyezése után Resource Manager virtuális hálózatokat az ExpressRoute-kapcsolódó áttelepítését mutatja.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: ganesr;cherylmc
ms.custom: seodec18
ms.openlocfilehash: c9f013a6af0b6d232eff32a9827006ce3247db3c
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53136407"
---
# <a name="migrate-expressroute-associated-virtual-networks-from-classic-to-resource-manager"></a>Az ExpressRoute-kapcsolódó virtuális hálózatok áttelepítése klasszikusról Resource Manager

Ez a cikk bemutatja, hogyan migrálhat az ExpressRoute-kapcsolódó virtuális hálózatok a klasszikus üzemi modellben az Azure Resource Manager-alapú üzemi modellbe helyezze át az ExpressRoute-kapcsolatcsoport. 

## <a name="before-you-begin"></a>Előkészületek
* Győződjön meg arról, hogy az Azure PowerShell-modulok legújabb verzióját. További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azure/overview) foglalkozó témakörben talál.
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
  Select-AzureRmSubscription -SubscriptionName <Your Subscription Name>
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
  Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
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

## <a name="next-steps"></a>További lépések
* [A platform által támogatott áttelepítés IaaS-erőforrások klasszikusból Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [Részletes műszaki útmutató a klasszikusból az Azure Resource Manager-alapú üzemi modellbe történő, platform által támogatott migrálásról](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
* [– Gyakori kérdések: Az IaaS-erőforrások klasszikusból Azure Resource Manager a Platform által támogatott áttelepítés](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [Tekintse át a leggyakoribb áttelepítési hibák és megoldások](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
