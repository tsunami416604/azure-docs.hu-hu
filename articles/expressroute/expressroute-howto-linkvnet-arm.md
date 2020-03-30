---
title: 'ExpressRoute: Virtuális hálózat összekapcsolása egy kapcsolat: Azure PowerShell'
description: Ez a dokumentum áttekintést nyújt avirtuális hálózatok (VNets) és az ExpressRoute-áramkörök összekapcsolásáról az Erőforrás-kezelő telepítési modelljének és a PowerShellnek a használatával.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 05/20/2018
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: 242f52d643e817730772a7d678a219c2b6149d2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235468"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit"></a>Virtuális hálózat összekapcsolása egy ExpressRoute-kapcsolatcsoporttal
> [!div class="op_single_selector"]
> * [Azure-portál](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [Powershell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Videó – Azure-portál](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-linkvnet-classic.md)
>

Ez a cikk segít a virtuális hálózatok (VNets) összekapcsolása az Azure ExpressRoute-áramkörök a Resource Manager telepítési modell és a PowerShell használatával. A virtuális hálózatok lehetnek ugyanabban az előfizetésben vagy egy másik előfizetés egy része. Ez a cikk azt is bemutatja, hogyan frissítheti a virtuális hálózati kapcsolatot.

* Egy szabványos ExpressRoute-kapcsolathoz legfeljebb 10 virtuális hálózat kapcsolódhat. Minden virtuális hálózatnak ugyanabban a geopolitikai régióban kell lennie, ha szabványos ExpressRoute-áramkört használ. 

* Egyetlen virtuális hálózat legfeljebb négy ExpressRoute-kapcsolatcsoporthoz kapcsolható. A cikkben ismertetett lépésekkel új kapcsolatobjektumot hozhat létre minden olyan ExpressRoute-kapcsolati kapcsolathoz, amelyhez csatlakozik. Az ExpressRoute-áramkörök lehetnek ugyanabban az előfizetésben, különböző előfizetések, vagy a kettő kombinációja.

* Az ExpressRoute-kapcsolat geopolitikai régióján kívüli virtuális hálózatokat összekapcsolhatja, vagy nagyobb számú virtuális hálózatot csatlakoztathat az ExpressRoute-kapcsolathoz, ha engedélyezte az ExpressRoute prémium szintű bővítményt. A prémium kiegészítővel kapcsolatos további részletekért tekintse meg a [GYIK-et.](expressroute-faqs.md)


## <a name="before-you-begin"></a>Előkészületek

* A konfiguráció megkezdése előtt tekintse át az [előfeltételeket,](expressroute-prerequisites.md) [útválasztási követelményeket](expressroute-routing.md)és [munkafolyamatokat.](expressroute-workflows.md)

* Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége. 
  * Kövesse az utasításokat [egy ExpressRoute-kapcsolat létrehozásához,](expressroute-howto-circuit-arm.md) és a kapcsolatszolgáltató által engedélyezett áramkört. 
  * Győződjön meg arról, hogy az Azure privát társviszony-létesítés konfigurálva van a kapcsolatcsoporthoz. Az útválasztási utasításokat az útválasztási útmutató [konfigurálása](expressroute-howto-routing-arm.md) című cikkben találja. 
  * Győződjön meg arról, hogy az Azure privát társviszony-létesítés konfigurálva van, és a BGP-társviszony-létesítés a hálózat és a Microsoft között, így lehetővé teszi a végpontok közötti kapcsolatot.
  * Győződjön meg arról, hogy van egy virtuális hálózat és egy virtuális hálózati átjáró létrehozott és teljesen kiépített. Az utasításokat követve [hozzon létre egy virtuális hálózati átjárót az ExpressRoute számára.](expressroute-howto-add-gateway-resource-manager.md) Az ExpressRoute virtuális hálózati átjárója a GatewayType "ExpressRoute" típust használja, nem a VPN-t.

### <a name="working-with-azure-powershell"></a>Az Azure PowerShell együttműködése

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Virtuális hálózat csatlakoztatása ugyanabban az előfizetésben egy áramkörhöz
Virtuális hálózati átjárót egy ExpressRoute-kapcsolatcsoporthoz a következő parancsmag használatával csatlakoztathat. A parancsmag futtatása előtt győződjön meg arról, hogy a virtuális hálózati átjáró létrejött, és készen áll a csatolásra:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$gw = Get-AzVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Egy másik előfizetéshez tartozó virtuális hálózat bevonása egy kapcsolatcsoportba
Az ExpressRoute-áramköröket több előfizetésen keresztül is megoszthatja. Az alábbi ábra egy egyszerű vázlatot mutat be, amely bemutatja, hogyan működik az ExpressRoute-áramkörök megosztása több előfizetésen keresztül.

A nagy felhőn belüli kisebb felhők mindegyike olyan előfizetéseket jelöl, amelyek a szervezet különböző részlegeihez tartoznak. A szervezet minden részlege használhatja a saját előfizetését a szolgáltatásaik üzembe helyezéséhez – de egyetlen ExpressRoute-kapcsolaton osztozhatnak a helyszíni hálózathoz való csatlakozáshoz. Egyetlen részleg (ebben a példában: IT) birtokolhatja az ExpressRoute-áramkört. A szervezeten belüli egyéb előfizetések használhatják az ExpressRoute-áramkört.

> [!NOTE]
> Az ExpressRoute-kapcsolati és sávszélesség-díjak az előfizetés tulajdonosa lesz alkalmazva. Minden virtuális hálózat ugyanazt a sávszélességet használja.
> 
> 

![Előfizetések közötti kapcsolat](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)


### <a name="administration---circuit-owners-and-circuit-users"></a>Adminisztráció - áramkör-tulajdonosok és áramkör-felhasználók

Az "áramkör tulajdonosa" az ExpressRoute-áramköri erőforrás jogosult Energiafelhasználója. A kapcsolatcsoport tulajdonosa olyan engedélyeket hozhat létre, amelyeket a "körfelhasználók" beválthatnak. Az áramkör-felhasználók olyan virtuális hálózati átjárók tulajdonosai, amelyek nem ugyanabban az előfizetésben vannak, mint az ExpressRoute-kapcsolat. A kapcsolatcsoport-felhasználók virtuális hálózatonként egy engedélyezést válthatnak be.Circuit users can redeem authorizations (one authorization per virtual network).

Az áramkör tulajdonosa bármikor módosíthatja és visszavonhatja az engedélyeket. Az engedélyezés visszavonása azt eredményezi, hogy az összes kapcsolatkapcsolat törlődik attól az előfizetésből, amelynek hozzáférését visszavonták.

### <a name="circuit-owner-operations"></a>Áramkörtulajdonosi műveletek

**Engedélyezés létrehozása**

A kapcsolatcsoport tulajdonosa létrehoz egy engedélyt. Ennek eredményeképpen létre kell hozni egy engedélyezési kulcsot, amelyet egy áramkör-felhasználó használhat a virtuális hálózati átjárók ExpressRoute-kapcsolathoz való csatlakoztatására. Az engedélyezés csak egy kapcsolatra érvényes.

A következő parancsmag-kódrészlet bemutatja, hogyan hozhat létre engedélyezési adatokat:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$auth1 = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
```


Az erre adott válasz az engedélyezési kulcsot és az állapotot tartalmazza:

    Name                   : MyAuthorization1
    Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
    Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
    AuthorizationKey       : ####################################
    AuthorizationUseStatus : Available
    ProvisioningState      : Succeeded



**Engedélyek áttekintése**

A kör tulajdonosa a következő parancsmag futtatásával ellenőrizheti az adott áramkörre kiadott összes engedélyt:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Engedélyek hozzáadása**

Az áramkör tulajdonosa a következő parancsmag használatával adhat hozzá engedélyeket:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization2"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Engedélyek törlése**

A kapcsolatcsoport tulajdonosa visszavonhatja/törölheti a felhasználónak szóló engedélyeket a következő parancsmag futtatásával:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitAuthorization -Name "MyAuthorization2" -ExpressRouteCircuit $circuit
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit
```    

### <a name="circuit-user-operations"></a>Áramkörfelhasználói műveletek

Az áramkör-felhasználónak szüksége van a társazonosítóra és egy engedélyezési kulcsra a kapcsolattulajdonostól. Az engedélyezési kulcs guid.

A társazonosító a következő parancsból ellenőrizhető:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**Kapcsolati engedély beváltása**

Az áramkör-felhasználó a következő parancsmast futtathatja a kapcsolatengedélyezés beváltásához:

```azurepowershell-interactive
$id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"    
$gw = Get-AzVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Kapcsolatengedélyezési kiadás**

Az engedélyezést az ExpressRoute-kapcsolat virtuális hálózattal összekötő kapcsolat törlésével szabadíthatja fel.

## <a name="modify-a-virtual-network-connection"></a>Virtuális hálózati kapcsolat módosítása
A virtuális hálózati kapcsolat bizonyos tulajdonságai frissíthetők. 

**A kapcsolat súlyának frissítése**

A virtuális hálózat több ExpressRoute-kapcsolathoz is csatlakoztatható. Ugyanazt az előtagot több ExpressRoute-kapcsolatból is kaphatja. Ha meg szeretné választani, hogy melyik kapcsolatot küldje el az előtaghoz szánt forgalmat, módosíthatja a kapcsolat *RoutingWeight-ját.* A forgalom a legmagasabb RoutingWeight kapcsolaton keresztül lesz *elküldve.*

```azurepowershell-interactive
$connection = Get-AzVirtualNetworkGatewayConnection -Name "MyVirtualNetworkConnection" -ResourceGroupName "MyRG"
$connection.RoutingWeight = 100
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
```

*Az RoutingWeight* tartománya 0 és 32000 között van. Az alapértelmezett érték a 0.

## <a name="configure-expressroute-fastpath"></a>ExpressRoute fastpath konfigurálása 
Engedélyezheti [expressroute FastPath,](expressroute-about-virtual-network-gateways.md) ha a virtuális hálózati átjáró Ultra Performance vagy ErGw3AZ. A FastPath javítja az adatelérési út teljesítményét, például a másodpercenkénti csomagokat és a helyszíni hálózat és a virtuális hálózat közötti másodpercenkénti kapcsolatokat. 

**A FastPath konfigurálása új kapcsolaton**

```azurepowershell-interactive 
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG" 
$gw = Get-AzVirtualNetworkGateway -Name "MyGateway" -ResourceGroupName "MyRG" 
$connection = New-AzVirtualNetworkGatewayConnection -Name "MyConnection" -ResourceGroupName "MyRG" -ExpressRouteGatewayBypass -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute -Location "MyLocation" 
``` 

**Meglévő kapcsolat frissítése a FastPath engedélyezéséhez**

```azurepowershell-interactive 
$connection = Get-AzVirtualNetworkGatewayConnection -Name "MyConnection" -ResourceGroupName "MyRG" 
$connection.ExpressRouteGatewayBypass = $True
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
``` 

## <a name="next-steps"></a>További lépések
Az ExpressRoute-ról további információt az [ExpressRoute gyakori kérdések című](expressroute-faqs.md)témakörben talál.
