---
title: 'Azure-ExpressRoute: Global Reach konfigurálása'
description: Ez a cikk az ExpressRoute-Kapcsolatcsoportok privát hálózati a helyszíni hálózat között, és engedélyezze a globális elérhetőségű teszik hivatkozásra.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: jaredro
ms.openlocfilehash: 76de7a8854a58deb924cbbe3177ad5a7b5fd57a2
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083462"
---
# <a name="configure-expressroute-global-reach"></a>Az ExpressRoute Global Reach konfigurálása

Ez a cikk segít az ExpressRoute globális elérhetőségű PowerShell-lel konfigurálja. További információkért lásd: [ExpressRouteRoute globális elérhetőségű](expressroute-global-reach.md).

 ## <a name="before-you-begin"></a>Előkészületek

A konfigurálás megkezdése előtt erősítse meg a következőket:

* Megismerheti az ExpressRoute áramkör kiépítési [munkafolyamatait](expressroute-workflows.md).
* A ExpressRoute-áramkörök kiépített állapotban vannak.
* Az Azure-beli privát kapcsolatok konfigurálása a ExpressRoute-áramkörökben történik.
* Ha helyileg szeretné futtatni a PowerShellt, ellenőrizze, hogy a Azure PowerShell legújabb verziója van-e telepítve a számítógépen.

### <a name="working-with-azure-powershell"></a>Az Azure PowerShell használata

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="identify-circuits"></a>Áramkörök azonosítása

1. A konfiguráció elindításához jelentkezzen be az Azure-fiókjába, és válassza ki a használni kívánt előfizetést.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]
2. Azonosítsa a használni kívánt ExpressRoute-áramköröket. A ExpressRoute Global Reach bármely két ExpressRoute között engedélyezheti, ha azok a támogatott országokban/régiókban találhatók, és különböző, egymástól eltérő helyeken hozták létre. 

   * Ha az előfizetés mindkét áramkört birtokolja, akkor a következő részekben választhatja ki a konfiguráció futtatásához.
   * Ha a két áramkör különböző Azure-előfizetésekben található, akkor egy Azure-előfizetésre van szükség. Ezután adja meg az engedélyezési kulcsot, amikor a másik Azure-előfizetésben futtatja a konfigurációs parancsot.

## <a name="enable-connectivity"></a>Kapcsolat engedélyezése

Engedélyezze a kapcsolatot a helyszíni hálózatok között. Az azonos Azure-előfizetésben és a különböző előfizetésekben lévő áramkörökhöz külön utasítások tartoznak.

### <a name="expressroute-circuits-in-the-same-azure-subscription"></a>ExpressRoute-áramkörök ugyanabban az Azure-előfizetésben

1. Kapcsolatcsoport 1 és 2 kapcsolatcsoport beolvasásához használja a következő parancsokat. A két Kapcsolatcsoportok találhatók ugyanabban az előfizetésben.

   ```azurepowershell-interactive
   $ckt_1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   ```
2. Futtassa a következő parancsot az 1. áramkörön, és adja át a 2. áramkör privát társ-AZONOSÍTÓját. A parancs futtatásakor vegye figyelembe a következőket:

   * A privát társ-létrehozási azonosító a következő példához hasonlóan néz ki: 

     ```
     /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering
     ```
   * *– A AddressPrefix-* nek egy/29 IPv4-alhálózatnak kell lennie, például "10.0.0.0/29". Ebben az alhálózatban az IP-címeket használjuk a két ExpressRoute-áramkör közötti kapcsolat létesítéséhez. Az ezen az alhálózaton található címeket nem használhatja az Azure-beli virtuális hálózatokban vagy a helyszíni hálózaton.

     ```azurepowershell-interactive
     Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
     ```
3. Mentse a konfigurációt az 1. áramkörön a következőképpen:

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

Ha az előző művelet befejeződik, mindkét oldalon a két ExpressRoute-áramkörön keresztül kapcsolatot fog létesíteni a helyszíni hálózatok között.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>Az ExpressRoute-Kapcsolatcsoportok az Azure-előfizetések

Ha a két áramkör nem ugyanabban az Azure-előfizetésben található, akkor engedélyre van szüksége. A következő konfigurációban az engedélyezés a 2. áramkör-előfizetésben jön létre, az engedélyezési kulcsot pedig az 1. áramkörhöz adja át.

1. Hitelesítési kulcs létrehozásához.

   ```azurepowershell-interactive
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_2
   ```

   Jegyezze fel a 2. áramkör privát társ-AZONOSÍTÓját, valamint az engedélyezési kulcsot.
2. Futtassa a következő parancsot a kapcsolatcsoport 1 ellen. Adja át a 2. áramkör privát társ-AZONOSÍTÓját és az engedélyezési kulcsot.

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
   ```
3. Mentse a konfigurációt az 1. áramkörön.

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

Ha az előző művelet befejeződik, mindkét oldalon a két ExpressRoute-áramkörön keresztül kapcsolatot fog létesíteni a helyszíni hálózatok között.

## <a name="verify-the-configuration"></a>A konfiguráció ellenőrzése

A következő paranccsal ellenőrizheti a konfigurációt azon az áramkörön, amelyen a konfigurációt végezték (például az előző példában az 1. áramkört).
```azurepowershell-interactive
$ckt1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

Ha egyszerűen *$CKT 1* -et futtat a PowerShellben, a kimenetben megjelenik a *CircuitConnectionStatus* . Megtudhatja, hogy létrejött-e a kapcsolat, a "csatlakoztatott" vagy a "leválasztott". 

## <a name="disable-connectivity"></a>Kapcsolat letiltása

A helyszíni hálózatok közötti kapcsolat letiltásához futtassa a parancsokat azon az áramkörön, ahol a konfiguráció létrejött (például az előző példában szereplő 1. áramkör).

```azurepowershell-interactive
$ckt1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

A Get-művelet a állapotának ellenőrzése futtathatja.

Az előző művelet befejezése után már nincs kapcsolat a helyszíni hálózat és a ExpressRoute-áramkörök között.

## <a name="next-steps"></a>Következő lépések
1. [További információ az ExpressRoute globális elérhetőségű](expressroute-global-reach.md)
2. [Az ExpressRoute-kapcsolat ellenőrzése](expressroute-troubleshooting-expressroute-overview.md)
3. [ExpressRoute-áramkör összekapcsolása egy Azure-beli virtuális hálózattal](expressroute-howto-linkvnet-arm.md)
