---
title: 'Az erőforrás-kezelő áttelepítése ExpressRoute társított virtuális hálózatokat a klasszikus: Azure: PowerShell |} Microsoft Docs'
description: Ezen a lapon helyezze át a kapcsolatcsoport az erőforrás-kezelő társított virtuális hálózatokat áttelepítési módját ismerteti.
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/06/2017
ms.author: ganesr;cherylmc
ms.openlocfilehash: 336f68308f7d4b4dd3c7476a4fabd793939e9e85
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23850825"
---
# <a name="migrate-expressroute-associated-virtual-networks-from-classic-to-resource-manager"></a>Klasszikus ExpressRoute társított virtuális hálózatok át az erőforrás-kezelő

Ez a cikk ismerteti, hogyan telepíthetők át Azure ExpressRoute társított virtuális hálózatokat a klasszikus telepítési modellből az Azure Resource Manager telepítési modell helyezze át az ExpressRoute-kapcsolatcsoportot. 


## <a name="before-you-begin"></a>Előkészületek
* Győződjön meg arról, hogy rendelkezik-e az Azure PowerShell-modulok legújabb verzióját. További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azure/overview) foglalkozó témakörben talál.
* Győződjön meg arról, hogy tekintse át a [Előfeltételek](expressroute-prerequisites.md), [útválasztási követelmények](expressroute-routing.md), és [munkafolyamatok](expressroute-workflows.md) konfigurálás elkezdése előtt.
* Tekintse át az adatokat a megadott [klasszikus ExpressRoute-kapcsolatcsoportot áthelyezést az erőforrás-kezelő](expressroute-move.md). Győződjön meg arról, hogy megértette a korlátai és korlátozásai.
* Győződjön meg arról, hogy a kapcsolatcsoport teljesen működőképes, a klasszikus üzembe helyezési modellben.
* Győződjön meg arról, hogy rendelkezik-e a Resource Manager üzembe helyezési modellel létrehozott erőforráscsoport.
* Tekintse át a következő erőforrás-áttelepítési dokumentáció:

    * [IaaS-erőforrásokra a klasszikus Azure Resource Manager platform által támogatott áttelepítése](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [Műszaki részletes bemutatója a platform által támogatott áttelepítési a klasszikus Azure Resource Managerbe](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
    * [Gyakori kérdések: Platform által támogatott áttelepítési IaaS erőforrások a klasszikus Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [Tekintse át a leggyakrabban előforduló áttelepítést hibákat, és azok mérséklési](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="supported-and-unsupported-scenarios"></a>Támogatott és nem támogatott forgatókönyvek

* A Resource Manager-környezet állásidő nélkül klasszikus ExpressRoute-kapcsolatcsoportot helyezheti át. Klasszikus ExpressRoute-kapcsolatcsoportot áthelyezheti a Resource Manager-környezet állásidő nélkül. Kövesse az utasításokat a [klasszikus ExpressRoute-Kapcsolatcsoportok áthelyezését a Resource Manager üzembe helyezési modellel PowerShell-lel](expressroute-howto-move-arm.md). Ez feltétele kell helyeznie az erőforrásokat a virtuális hálózathoz.
* Virtuális hálózatok, az átjárók és a társított központi telepítések a virtuális hálózaton belül ExpressRoute-kapcsolatcsoportot ugyanahhoz az előfizetéshez kapcsolódó az erőforrás-kezelő környezetbe állásidő nélkül telepíthetők át. Az erőforrások, például a virtuális hálózatok, az átjárók és a virtuális hálózaton belül telepített virtuális gépek áttelepítéséhez később ismertetett lépések követésével. Meg kell győződnie arról, hogy a virtuális hálózatok helyesen vannak konfigurálva az áttelepítés előtt. 
* Virtuális hálózatok, az átjárók és a társított központi telepítések a virtuális hálózaton belül, amelyek nem szerepelnek az ExpressRoute-kapcsolatcsoport tárolóként ugyanazt az előfizetést kell némi állásidővel, az áttelepítés befejezéséhez. A dokumentum utolsó szakasza-erőforrások áttelepítése követendő lépéseket ismerteti.
* Egy virtuális hálózaton, ExpressRoute-átjáró és a VPN-átjáró nem telepíthető át.

## <a name="move-an-expressroute-circuit-from-classic-to-resource-manager"></a>Klasszikus ExpressRoute-kapcsolatcsoportot áthelyezéséhez az erőforrás-kezelő
Át kell helyeznie egy ExpressRoute-kapcsolatcsoportot klasszikus a Resource Manager-környezetben az ExpressRoute-kapcsolatcsoport kapcsolódó erőforrások áttelepítése előtt. Ennek a feladatnak tekintse meg a következő cikkeket:

* Tekintse át az adatokat a megadott [klasszikus ExpressRoute-kapcsolatcsoportot áthelyezést az erőforrás-kezelő](expressroute-move.md).
* [Klasszikus expressroute-kapcsolatcsoporthoz áthelyezéséhez a Resource Manager az Azure PowerShell](expressroute-howto-move-arm.md).
* Használhatja az Azure szolgáltatásfelügyeleti portált. A munkafolyamat követésével [hozzon létre egy új ExpressRoute-kapcsolatcsoportot](expressroute-howto-circuit-portal-resource-manager.md) , és válassza az Importálás lehetőséget. 

Ez a művelet nem jár állásidővel. Továbbra is az adatok átviteléhez a helyszíni és a Microsoft között, amíg az áttelepítés van folyamatban.

## <a name="migrate-virtual-networks-gateways-and-associated-deployments"></a>Telepítse át a virtuális hálózatok, az átjárók és a társított központi telepítések

A áttelepítéséhez szükséges lépések attól függnek, hogy az erőforrások vannak-e ugyanazt az előfizetést, különböző előfizetésekhez vagy mindkettőt.

### <a name="migrate-virtual-networks-gateways-and-associated-deployments-in-the-same-subscription-as-the-expressroute-circuit"></a>Telepítse át a virtuális hálózatok, az átjárók és az ExpressRoute-kapcsolatcsoport tárolóként ugyanazt az előfizetést a társított központi telepítések
Ez a szakasz áttelepítése a virtuális hálózat, az átjárót és a társított központi telepítések ugyanazt az előfizetést, mint az ExpressRoute-kapcsolatcsoport követendő lépéseket ismerteti. Nincs leállás az áttelepítés tartozik. Ön továbbra is használhatja az áttelepítési folyamatot, az összes erőforrást. A felügyeleti vezérlősík zárolva van, amíg az áttelepítés van folyamatban. 

1. Győződjön meg arról, hogy az ExpressRoute-kapcsolatcsoport átkerült klasszikus a Resource Manager-környezetben.
2. Győződjön meg arról, hogy a virtuális hálózati készült megfelelően az áttelepítés.
3. Az előfizetés regisztrálása a erőforrás áttelepítése. Az erőforrás-áttelepítése előfizetés regisztrálásához a következő PowerShell kódrészletet használja:

  ```powershell 
  Select-AzureRmSubscription -SubscriptionName <Your Subscription Name>
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
  Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
  ```
4. Ellenőrizze, előkészítéséhez és áttelepítése. Helyezze át a virtuális hálózaton, használja a következő PowerShell-részlet:

  ```powershell
  Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
  Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
  Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
  ```

  Áttelepítés a következő PowerShell-parancsmagot is megszakítja:

  ```powershell
  Move-AzureVirtualNetwork -Abort $vnetName
  ```

## <a name="next-steps"></a>Következő lépések
* [IaaS-erőforrásokra a klasszikus Azure Resource Manager platform által támogatott áttelepítése](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [Műszaki részletes bemutatója a platform által támogatott áttelepítési a klasszikus Azure Resource Managerbe](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
* [Gyakori kérdések: Platform által támogatott áttelepítési IaaS erőforrások a klasszikus Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [Tekintse át a leggyakrabban előforduló áttelepítést hibákat, és azok mérséklési](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
