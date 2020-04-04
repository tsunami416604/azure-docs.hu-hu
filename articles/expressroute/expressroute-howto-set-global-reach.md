---
title: 'Azure ExpressRoute: Globális elérés konfigurálása'
description: Ez a cikk segítséget nyújt az ExpressRoute-áramkörök összekapcsolásában, hogy magánhálózatot hozhassanak létre a helyszíni hálózatok között, és engedélyezze a Globális elérést.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: jaredro
ms.openlocfilehash: 587a17659a412d6f894faf5a744a7d9c444935c8
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656748"
---
# <a name="configure-expressroute-global-reach"></a>Az ExpressRoute Global Reach konfigurálása

Ez a cikk segít konfigurálni ExpressRoute globális elérés a PowerShell használatával. További információ: [ExpressRouteRoute Global Reach](expressroute-global-reach.md).

 ## <a name="before-you-begin"></a>Előkészületek

A konfiguráció megkezdése előtt erősítse meg a következőket:

* Az ExpressRoute-kapcsolat létesítési [munkafolyamatait](expressroute-workflows.md)ismeri.
* Az ExpressRoute-áramkörök kiépített állapotban vannak.
* Az Azure privát társviszony-létesítés konfigurálva van az ExpressRoute-áramkörök.
* Ha helyileg szeretné futtatni a PowerShellt, ellenőrizze, hogy az Azure PowerShell legújabb verziója telepítve van-e a számítógépen.

### <a name="working-with-azure-powershell"></a>Az Azure PowerShell együttműködése

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="identify-circuits"></a>Áramkörök azonosítása

1. A konfiguráció elindításához jelentkezzen be az Azure-fiókjába, és válassza ki a használni kívánt előfizetést.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]
2. Azonosítsa a használni kívánt ExpressRoute-áramköröket. Engedélyezheti az ExpressRoute globális elérését bármely két ExpressRoute-kapcsolatlétesítés között, feltéve, hogy azok a támogatott országokban/régiókban találhatók, és különböző társviszony-létesítési helyeken jöttek létre. 

   * Ha az előfizetés mindkét kapcsolatcsoport tulajdonosa, kiválaszthatja, hogy a következő szakaszokban futtassa a konfigurációt.
   * Ha a két kapcsolatcsoport különböző Azure-előfizetésekben található, egy Azure-előfizetésengedélyezésére van szükség. Ezután adja át az engedélyezési kulcsot, amikor futtatja a konfigurációs parancsot a másik Azure-előfizetésben.

## <a name="enable-connectivity"></a>Kapcsolat engedélyezése

Engedélyezze a kapcsolatot a helyszíni hálózatok között. Az azonos Azure-előfizetésben lévő áramkörökhöz külön utasítások vannak, és különböző előfizetések áramkörök.

### <a name="expressroute-circuits-in-the-same-azure-subscription"></a>ExpressRoute-áramkörök ugyanabban az Azure-előfizetésben

1. Az 1-es és a 2-es áramkör levételéhez használja a következő parancsokat. A két áramkör ugyanabban az előfizetésben van.

   ```azurepowershell-interactive
   $ckt_1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   ```
2. Futtassa a következő parancsot az 1-es áramkörhöz, és adja át a 2-es áramkör privát társviszony-létesítési azonosítóját. A parancs futtatásakor vegye figyelembe a következőket:

   * A privát társviszony-létesítési azonosító a következő példához hasonlóan néz ki: 

     ```
     /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering
     ```
   * *-Az AddressPrefix* névnek /29 IPv4 alhálózatnak kell lennie, például "10.0.0.0/29". Az alhálózat IP-címeit használjuk a két ExpressRoute-kapcsolat létrehozásához a két ExpressRoute-kapcsolat között. Ne használja az alhálózat címeit az Azure virtuális hálózataiban vagy a helyszíni hálózatban.

     ```azurepowershell-interactive
     Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
     ```
3. Mentse a konfigurációt az 1-es áramkörre az alábbiak szerint:

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

Amikor az előző művelet befejeződik, a két ExpressRoute-kapcsolaton keresztül mindkét oldalon lesz kapcsolat a helyszíni hálózatok között.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>ExpressRoute-áramkörök különböző Azure-előfizetésekben

Ha a két kapcsolatcsoport nem ugyanabban az Azure-előfizetésben van, engedélyre van szüksége. A következő konfigurációban az engedélyezés a 2-es áramkör-előfizetésben jön létre, és az engedélyezési kulcs átkerül az 1-es áramkörhöz.

1. Engedélyezési kulcs létrehozása.

   ```azurepowershell-interactive
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_2
   ```

   Jegyezze fel a 2-es kapcsolat létesítési azonosítóját, valamint az engedélyezési kulcsot.
2. Futtassa a következő parancsot az 1-es áramkörhöz. Adja át a 2-es kapcsolatlétesítési azonosítót és az engedélyezési kulcsot.

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
   ```
3. Mentse a konfigurációt az 1-es áramkörre.

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

Amikor az előző művelet befejeződik, a két ExpressRoute-kapcsolaton keresztül mindkét oldalon lesz kapcsolat a helyszíni hálózatok között.

## <a name="verify-the-configuration"></a>A konfiguráció ellenőrzése

A következő paranccsal ellenőrizze a konfigurációt azon az áramkörön, ahol a konfiguráció készült (például az 1- es áramkör az előző példában).
```azurepowershell-interactive
$ckt1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

Ha egyszerűen *fut$ckt1* a PowerShellben, a kimenetben megjelenik a *CircuitConnectionStatus.* Azt jelzi, hogy a kapcsolat létrejött-e, "Csatlakoztatva" vagy "Leválasztva". 

## <a name="disable-connectivity"></a>Az összekapcsolhatóság letiltása

A helyszíni hálózatok közötti kapcsolat letiltásához futtassa a parancsokat azon az áramkörön, ahol a konfiguráció készült (például az 1-es áramkör az előző példában).

```azurepowershell-interactive
$ckt1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Az állapot ellenőrzéséhez futtathatja a Get műveletet.

Az előző művelet befejezése után már nem rendelkezik kapcsolat a helyszíni hálózat között az ExpressRoute-áramkörökön keresztül.

## <a name="next-steps"></a>További lépések
1. [További információ az ExpressRoute globális eléréséről](expressroute-global-reach.md)
2. [ExpressRoute-kapcsolat ellenőrzése](expressroute-troubleshooting-expressroute-overview.md)
3. [ExpressRoute-kapcsolat egy Azure virtuális hálózattal](expressroute-howto-linkvnet-arm.md)
