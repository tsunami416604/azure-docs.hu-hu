---
title: 'Globális elérhetőség – az ExpressRoute konfigurálása: Azure | Microsoft Docs'
description: Ez a cikk az ExpressRoute-Kapcsolatcsoportok privát hálózati a helyszíni hálózat között, és engedélyezze a globális elérhetőségű teszik hivatkozásra.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: jaredro
ms.custom: seodec18
ms.openlocfilehash: 171bf94bbccd45b9be995977c9ec2a26a75d9602
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57403481"
---
# <a name="configure-expressroute-global-reach"></a>Az ExpressRoute Global Reach konfigurálása

Ez a cikk segít az ExpressRoute globális elérhetőségű PowerShell-lel konfigurálja. További információkért lásd: [ExpressRouteRoute globális elérhetőségű](expressroute-global-reach.md).

 ## <a name="before-you-begin"></a>Előkészületek

Mielőtt elkezdené a konfigurációt, ellenőrizze az alábbiakat:

* Az ExpressRoute kapcsolatcsoport-kiépítési tisztában [munkafolyamatok](expressroute-workflows.md).
* Az ExpressRoute-Kapcsolatcsoportok kiépített állapotban van.
* Az Azure privát társviszony-létesítést az ExpressRoute-Kapcsolatcsoportok van konfigurálva.
* Ha a PowerShell helyi futtatása esetén ellenőrizze, hogy az Azure PowerShell legújabb verzióját a számítógépen van telepítve.

### <a name="working-with-azure-powershell"></a>Az Azure PowerShell használata

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="identify-circuits"></a>Kapcsolatcsoportok azonosítása

1. A konfiguráció indításához jelentkezzen be az Azure-fiókjával, és válassza ki a használni kívánt előfizetést.

  [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]
2. A kívánt ExpressRoute-Kapcsolatcsoportok azonosításához. Engedélyezheti a globális elérhetőségű ExpressRoute bármely két ExpressRoute-Kapcsolatcsoportok között mindaddig, amíg Ön található a támogatott országok és a létrehozásuk másik társviszony-létesítési helyszínek. 

  * Ha az előfizetés tulajdonosa mindkét Kapcsolatcsoportok, vagy futtassa a konfigurációt az alábbi szakaszok a kapcsolatcsoport választhat.
  * Ha a két kapcsolatcsoporttal az Azure-előfizetések, szüksége van egy Azure-előfizetésből engedélyezési. Ezután adja át az engedélyezési kulcsot a többi Azure-előfizetésben a konfigurációs parancs futtatásakor.

## <a name="enable-connectivity"></a>Kapcsolat engedélyezése

Engedélyezze a kapcsolatot a helyszíni hálózat között. Nincsenek külön csoportok, amelyek szerepelnek az Azure-előfizetéshez, és a Kapcsolatcsoportok, amelyek eltérő előfizetésekben vonatkozó utasításokat tartalmazza.

### <a name="expressroute-circuits-in-the-same-azure-subscription"></a>Az ExpressRoute-Kapcsolatcsoportok az Azure-előfizetéshez

1. Kapcsolatcsoport 1 és 2 kapcsolatcsoport beolvasásához használja a következő parancsokat. A két Kapcsolatcsoportok találhatók ugyanabban az előfizetésben.

  ```azurepowershell-interactive
  $ckt_1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
  $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
  ```
2. Futtassa a következő parancsot a kapcsolatcsoport 1 szemben, és a privát társviszony-létesítési kör 2 Azonosítóját adja át. Ha a következő parancs futtatásával, vegye figyelembe a következőket:

  * A privát társviszony-létesítési azonosítója a következő példához hasonlóan néz ki: 

    ```
    /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering
    ```
  * *-AddressPrefix* kell lennie egy/29 IPv4 alhálózati, például a "10.0.0.0/29". IP-cím az alhálózat, a két ExpressRoute-Kapcsolatcsoportok közötti kapcsolatot létesíteni használjuk. Ez az alhálózat, ne használja a címeket, az Azure virtuális hálózataihoz, illetve a helyszíni hálózat.

    ```azurepowershell-interactive
    Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
    ```
3. A konfiguráció mentéséhez kapcsolatcsoport 1 a következőképpen:

  ```azurepowershell-interactive
  Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
  ```

Az előző művelet befejezéséről, elérhetőséget keresztül a két ExpressRoute-Kapcsolatcsoportok mindkét oldalon az a helyszíni hálózat között.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>Az ExpressRoute-Kapcsolatcsoportok az Azure-előfizetések

Ha a két Kapcsolatcsoportok nem az Azure-előfizetéshez, engedélyt kell. A következő konfigurációt, az engedélyezési jön létre a kapcsolatcsoport 2 előfizetés, és a hitelesítési kulcs átadott kapcsolatcsoport 1.

1. Hitelesítési kulcs létrehozásához.

  ```azurepowershell-interactive
  $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
  Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
  Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_2
  ```

  Jegyezze fel a kapcsolatcsoport 2, valamint a hitelesítési kulcs privát társviszony-létesítési azonosítója.
2. Futtassa a következő parancsot a kapcsolatcsoport 1 ellen. 2. és a hitelesítési kulcs adja át a privát társviszony-létesítési kör Azonosítóját.

  ```azurepowershell-interactive
  Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
  ```
3. A kapcsolatcsoport 1 a konfiguráció mentéséhez.

  ```azurepowershell-interactive
  Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
  ```

Az előző művelet befejezéséről, elérhetőséget keresztül a két ExpressRoute-Kapcsolatcsoportok mindkét oldalon az a helyszíni hálózat között.

## <a name="verify-the-configuration"></a>A konfiguráció ellenőrzése

Használja a következő parancsot a kapcsolatcsoport a konfiguráció ellenőrzése, amelyen a konfigurációs (például az előző példában 1 kapcsolatcsoport) történt.
```azurepowershell-interactive
$ckt1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

Ha egyszerűen futtassa *$ckt1* láthatja a PowerShellben *CircuitConnectionStatus* a kimenetben. Megadja, hogy e a kapcsolat létrejött, "Csatlakoztatva" vagy "Csatlakoztatva". 

## <a name="disable-connectivity"></a>Tiltsa le a kapcsolat

Letilthatja a kapcsolat a helyszíni hálózat között, a parancsok futtatásához a kapcsolatcsoport hol jött létre a configuration (például a kapcsolatcsoport 1 az előző példában).

```azurepowershell-interactive
$ckt1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

A Get-művelet a állapotának ellenőrzése futtathatja.

Az előző művelet befejezése után, hogy már nem rendelkezik az ExpressRoute-Kapcsolatcsoportok keresztül a helyszíni hálózat között.

## <a name="next-steps"></a>További lépések
1. [További információ az ExpressRoute globális elérhetőségű](expressroute-global-reach.md)
2. [Az ExpressRoute-kapcsolat ellenőrzése](expressroute-troubleshooting-expressroute-overview.md)
3. [Az Azure virtuális hálózat ExpressRoute-kapcsolatcsoport összekapcsolása](expressroute-howto-linkvnet-arm.md)
