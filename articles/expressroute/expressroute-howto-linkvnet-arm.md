---
title: 'Virtuális hálózat összekapcsolása egy ExpressRoute-kapcsolatcsoport: PowerShell: Azure |} A Microsoft Docs'
description: Ez a dokumentum az ExpressRoute-Kapcsolatcsoportok összekapcsolása virtuális hálózatok (Vnetek) áttekintést nyújt a Resource Manager üzemi modell és a PowerShell használatával.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 10/30/2018
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: 5dde6dfdfaed23dbbfe61b06a433f731d90a0440
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53075998"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit"></a>Virtuális hálózat összekapcsolása egy ExpressRoute-kapcsolatcsoporthoz
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Videó – Azure portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-linkvnet-classic.md)
>

Ez a cikk segít összekapcsolása virtuális hálózatok (Vnetek) az Azure ExpressRoute-Kapcsolatcsoportok a Resource Manager üzemi modell és a PowerShell használatával. Virtuális hálózatok csak az ugyanabban az előfizetésben vagy egy másik előfizetés részeként a. Ez a cikk is bemutatja, hogyan frissíthető egy virtuális hálózati kapcsolat.

* Legfeljebb 10 virtuális hálózatok kapcsolat egy standard ExpressRoute-kapcsolatcsoporthoz. Az összes virtuális hálózatok ugyanazon geopolitikai régióban kell lennie, a standard ExpressRoute-kapcsolatcsoport használatánál. 

* Egyetlen virtuális hálózat legfeljebb négy ExpressRoute-Kapcsolatcsoportok lehet kapcsolódni. Ebben a cikkben a lépések segítségével hozzon létre egy új kapcsolat objektumot mindegyik ExpressRoute-kapcsolatcsoporthoz csatlakozik. Az ExpressRoute-Kapcsolatcsoportok ugyanahhoz az előfizetéshez tartozik, eltérő előfizetésekben vagy mindkét vegyesen is lehet.

* Virtuális hálózatokat az ExpressRoute-kapcsolatcsoport a geopolitikai régión kívül, vagy nagyobb számú virtuális hálózatok csatlakozni az ExpressRoute-kapcsolatcsoportot, ha az ExpressRoute prémium bővítmény engedélyezve. Ellenőrizze a [– gyakori kérdések](expressroute-faqs.md) kapcsolatos további részletekért a premium bővítményt.


## <a name="before-you-begin"></a>Előkészületek

* Tekintse át a [Előfeltételek](expressroute-prerequisites.md), [útválasztási követelmények](expressroute-routing.md), és [munkafolyamatok](expressroute-workflows.md) konfigurálás megkezdése előtt.

* Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége. 
  * Kövesse az utasításokat [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-arm.md) , és a kapcsolatcsoport a kapcsolatszolgáltató által engedélyezett. 
  * Gondoskodjon arról, hogy az Azure privát társviszony-létesítést a kapcsolatcsoporthoz konfigurálva. Tekintse meg a [konfigurálja az útválasztást](expressroute-howto-routing-arm.md) cikk vonatkozó útválasztási utasításokat. 
  * Győződjön meg arról, hogy az Azure privát társviszony-létesítés konfigurálva legyen, és a BGP társviszony-létesítés a hálózat és a Microsoft között működik, így engedélyezheti a végpontok közötti kapcsolat.
  * Gondoskodjon arról, hogy egy virtuális hálózat és a egy virtuális hálózati átjáró létrehozása, és teljesen kiépítve. Kövesse az utasításokat [az ExpressRoute virtuális hálózati átjáró létrehozása](expressroute-howto-add-gateway-resource-manager.md). Az ExpressRoute virtuális hálózati átjáró a "ExpressRoute", nem gatewaytype VPN típust használja.

### <a name="working-with-azure-powershell"></a>Az Azure PowerShell használata
[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Azonos előfizetésben található virtuális hálózat csatlakoztatása egy kapcsolatcsoporthoz
A következő parancsmag használatával csatlakozni tud a virtuális hálózati átjáró csatlakoztatása egy ExpressRoute-kapcsolatcsoporthoz. Győződjön meg arról, hogy a virtuális hálózati átjáró jön létre, és készen áll a csatolás, a parancsmag futtatása előtt:

```azurepowershell-interactive
$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$gw = Get-AzureRmVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Egy másik előfizetéshez tartozó virtuális hálózat bevonása egy kapcsolatcsoportba
ExpressRoute-kapcsolatcsoport több előfizetésre kiterjedő megoszthatja. A következő ábrán látható egy egyszerű az ExpressRoute-Kapcsolatcsoportok hogyan megosztási alkotások sematikus több előfizetéshez.

A nagyméretű felhőbeli belül a kisebb felhők mindegyike egy szervezet különböző részlegei tartozó előfizetések megjelenítésére szolgál. Minden, a szervezeti egységek, a szervezeten belül használhatja a saját előfizetés üzembe helyezéséhez a szolgáltatásaik –, de egyetlen ExpressRoute-kapcsolatcsoporthoz szeretne csatlakozni a helyszíni hálózaton is megoszthatja. Egyetlen részleg (ebben a példában: informatikai) is a saját ExpressRoute-kapcsolatcsoportot. Más előfizetésekre, a szervezeten belül használható az ExpressRoute-kapcsolatcsoporthoz.

> [!NOTE]
> Az ExpressRoute-kapcsolatcsoport kapcsolatot és a sávszélesség kell fizetni az előfizetés tulajdonosa lesz alkalmazható. Minden virtuális hálózat ossza meg ugyanazt a sávszélesség.
> 
> 

![Az előfizetések közötti kapcsolat](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)


### <a name="administration---circuit-owners-and-circuit-users"></a>Felügyelet – kapcsolatcsoport tulajdonosai és a kapcsolatcsoport felhasználóinak

A kapcsolatcsoport tulajdonosát a jogosult kiemelt felhasználó az ExpressRoute-kapcsolatcsoport erőforrás. A kapcsolatcsoport tulajdonosát engedélyeket, melyeket a "kapcsolatcsoport felhasználóinak" hozhat létre. Kapcsolatcsoport felhasználók, amelyek nem az ExpressRoute-kapcsolatcsoporthoz, az egy előfizetésen belüli virtuális hálózati átjárók tulajdonosai. Kapcsolatcsoport felhasználóinak az engedélyek (egy engedélyezési száma virtuális hálózatonként) is beváltásához.

A kapcsolatcsoport tulajdonosát a rendelkezik módosítja, és bármikor engedélyek visszavonása. Visszavonása egy engedélyezési eredményez az összes hivatkozás kapcsolat törlése folyamatban az előfizetésből, amelynek hozzáférését visszavonták.

### <a name="circuit-owner-operations"></a>Kapcsolatcsoport-tulajdonos műveletek

**Az engedély létrehozása**

A kapcsolatcsoport tulajdonosát egy engedélyezési hoz létre. Az eredmény egy kapcsolatcsoport felhasználó által a virtuális hálózati átjárók az ExpressRoute-kapcsolatcsoporthoz való csatlakozáshoz használható hitelesítési kulcs létrehozását. Egy engedélyezési csak egy kapcsolat érvényességét.

A következő parancsmag kódrészlet bemutatja, hogyan hozhat létre egy engedélyezési:

```azurepowershell-interactive
$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$auth1 = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
```


Ez a válasz tartalmazza a hitelesítési kulcsot és az állapotát:

    Name                   : MyAuthorization1
    Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
    Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
    AuthorizationKey       : ####################################
    AuthorizationUseStatus : Available
    ProvisioningState      : Succeeded



**Az engedélyek áttekintéséhez**

A kapcsolatcsoport tulajdonosát a következő parancsmag futtatásával egy adott kapcsolatcsoportban kiállított összes engedélyek tekintheti át:

```azurepowershell-interactive
$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Engedélyek hozzáadása**

A kapcsolatcsoport tulajdonosát a következő parancsmag használatával adhat hozzá engedélyeket:

```azurepowershell-interactive
$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization2"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Engedélyek törlése**

A kapcsolatcsoport tulajdonosát is visszavonása/törlése engedélyek a felhasználónak a következő parancsmagot:

```azurepowershell-interactive
Remove-AzureRmExpressRouteCircuitAuthorization -Name "MyAuthorization2" -ExpressRouteCircuit $circuit
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit
```    

### <a name="circuit-user-operations"></a>Kapcsolatcsoport felhasználói műveletek

A kapcsolatcsoport-felhasználó a társ-azonosító és a egy engedélyezési kulcsot, a kapcsolatcsoport tulajdonosát a van szüksége. A hitelesítési kulcs a GUID Azonosítót.

Társ azonosítója a következő paranccsal ellenőrizhetők:

```azurepowershell-interactive
Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**Hogy a kapcsolati engedély beváltása**

A kapcsolatcsoport felhasználói beváltása egy kapcsolat a következő parancsmag futtatásával:

```azurepowershell-interactive
$id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"    
$gw = Get-AzureRmVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**A kapcsolati engedély felszabadítása**

Egy engedélyezési fel lehet szabadítani, a kapcsolat törlésével a hivatkozásokat az ExpressRoute-kapcsolatcsoport a virtuális hálózathoz.

## <a name="modify-a-virtual-network-connection"></a>Módosítsa a virtuális hálózati kapcsolat
A virtuális hálózati kapcsolat bizonyos tulajdonságait is frissítheti. 

**Kapcsolat súlyát frissítése**

Több ExpressRoute-kapcsolatcsoporttal a virtuális hálózathoz csatlakoztatható legyen. Egynél több ExpressRoute-kapcsolatcsoport a ugyanazon előtaggal jelenhet meg. Melyik kapcsolat előtaghoz irányuló forgalom küldésére módosíthatja *routingweight értékének* kapcsolat. A kapcsolat a legmagasabb küld forgalmat *routingweight értékének*.

```azurepowershell-interactive
$connection = Get-AzureRmVirtualNetworkGatewayConnection -Name "MyVirtualNetworkConnection" -ResourceGroupName "MyRG"
$connection.RoutingWeight = 100
Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
```

A számos *routingweight értékének* 0 a 32000 van. Az alapértelmezett érték a 0.

## <a name="next-steps"></a>További lépések
További információ az ExpressRoute-tal kapcsolatban: [ExpressRoute – Gyakori kérdések](expressroute-faqs.md).
