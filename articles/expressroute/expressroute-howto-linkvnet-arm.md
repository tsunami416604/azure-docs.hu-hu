---
title: "Virtuális hálózat csatolása ExpressRoute-kapcsolatcsoportot: PowerShell: Azure |} Microsoft Docs"
description: "Ez a dokumentum áttekintést csatolása a virtuális hálózatokon (Vnetek) az ExpressRoute-Kapcsolatcsoportok a Resource Manager üzembe helyezési modellben és a PowerShell használatával."
services: expressroute
documentationcenter: na
author: ganesr
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: daacb6e5-705a-456f-9a03-c4fc3f8c1f7e
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2018
ms.author: ganesr
ms.openlocfilehash: 354f7c455e1a2846bbdd63fa12b1cc01e2a1b9c5
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2018
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit"></a>Csatlakozás a virtuális hálózati ExpressRoute-kapcsolatcsoportot
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video - Azure-portálon](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-linkvnet-classic.md)
>

Ez a cikk segít virtuális hálózatokról (Vnetekről) hivatkozásra az Azure ExpressRoute-Kapcsolatcsoportok a Resource Manager üzembe helyezési modellben és a PowerShell használatával. Virtuális hálózatok a ugyanahhoz az előfizetéshez vagy egy másik előfizetés része lehet. Ez a cikk is bemutatja, hogyan frissítse a virtuális hálózati kapcsolaton. 

## <a name="before-you-begin"></a>Előkészületek
* Telepítse a legújabb verzióját az Azure PowerShell-modulok. További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azure/overview) foglalkozó témakörben talál.

* Tekintse át a [Előfeltételek](expressroute-prerequisites.md), [útválasztási követelmények](expressroute-routing.md), és [munkafolyamatok](expressroute-workflows.md) konfigurálás elkezdése előtt.

* Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége. 
  * Kövesse az utasításokat [ExpressRoute-kapcsolatcsoportot létrehozni](expressroute-howto-circuit-arm.md) és a kapcsolatcsoport szerint a kapcsolat szolgáltatójánál engedélyezve van. 
  * Győződjön meg arról, hogy rendelkezik az Azure magánhálózati társviszony-létesítés a kapcsolatcsoport konfigurálva. Tekintse meg a [konfigurálja az útválasztást](expressroute-howto-routing-arm.md) útválasztási miként. 
  * Győződjön meg arról, hogy az Azure magánhálózati társviszony-létesítés konfigurálva legyen, és a BGP társviszony-létesítés a hálózat és a Microsoft között működik-e, hogy engedélyezheti a végpontok közötti kapcsolat.
  * Győződjön meg arról, hogy a virtuális hálózat és a virtuális hálózati átjáró létrehozása, és teljesen kiépítve. Kövesse az utasításokat [hozzon létre egy virtuális hálózati átjáró ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). A virtuális hálózati átjáró ExpressRoute az "ExpressRoute", nem VPN GatewayType használja.

* Legfeljebb 10 virtuális hálózatok hozzákapcsolhatja egy szabványos ExpressRoute-kapcsolatcsoportot. Az összes virtuális hálózatot geopolitikai ugyanabban a régióban kell lennie, egy szabványos ExpressRoute-kapcsolatcsoportot használatakor. 

* Egy egyetlen virtuális hálózat legfeljebb négy ExpressRoute-Kapcsolatcsoportok lehet társítani. Az alábbi eljárás segítségével új objektumot az egyes csatlakozik ExpressRoute-kapcsolatcsoportot létrehozni. Az ExpressRoute-Kapcsolatcsoportok ugyanahhoz az előfizetéshez, különböző előfizetésekhez vagy vegyesen is lehet.

* A virtuális hálózat kívül az ExpressRoute-kapcsolatcsoport geopolitikai régiója összekapcsolása, vagy virtuális hálózatok nagyobb számú csatlakozni az ExpressRoute-kapcsolatcsoportot, ha engedélyezte a prémium szintű ExpressRoute-bővítmény. Ellenőrizze a [gyakran ismételt kérdések](expressroute-faqs.md) a prémium szintű bővítmény olvashat.


## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>A virtuális hálózati ugyanahhoz az előfizetéshez csatlakozni expressroute-kapcsolatcsoporthoz
A következő parancsmag használatával ExpressRoute-kapcsolatcsoportot virtuális hálózati átjáró képes kapcsolódni. Győződjön meg arról, hogy a virtuális hálózati átjáró jön létre, és készen áll a csatolás a parancsmag futtatása előtt:

```powershell
$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$gw = Get-AzureRmVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Egy másik előfizetéshez tartozó virtuális hálózat bevonása egy kapcsolatcsoportba
Több előfizetés ExpressRoute-kapcsolatcsoportot lehet megosztani. Az alábbi ábrán egy egyszerű sematikus ExpressRoute-Kapcsolatcsoportok megosztási hogyan működik a több előfizetésekhez.

A nagy felhőben kisebb felhők egyes szervezet különböző részlegei tartozó előfizetések megjelenítésére szolgál. A saját előfizetés üzembe helyezéséhez, a szolgáltatások –, de használhatja a szervezeten belüli osztályok mindegyikének oszthatnak meg csatlakozni a helyi hálózat a egyetlen ExpressRoute-kapcsolatcsoportot. Egy részleghez (ebben a példában: informatikai) az ExpressRoute-kapcsolatcsoport rendelkezhet. A szervezeten belüli más előfizetések használhatja az ExpressRoute-kapcsolatcsoportot.

> [!NOTE]
> Kapcsolat és a sávszélesség költségekkel az ExpressRoute-kapcsolatcsoport alkalmazandó az előfizetés tulajdonosa. Az összes virtuális hálózatot megosztani a azonos sávszélességet.
> 
> 

![Az előfizetések közötti kapcsolathoz](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)


### <a name="administration---circuit-owners-and-circuit-users"></a>Felügyeleti - áramkör tulajdonosa és a kapcsolatcsoport felhasználók

A kapcsolatcsoport tulajdonosát a ExpressRoute-kapcsolatcsoport erőforrás Power jogosultsága. A kapcsolatcsoport tulajdonosát hozhat létre, amely a "kör felhasználók" is váltható engedélyek. Kör felhasználók, amelyek nem tartoznak a ExpressRoute-kapcsolatcsoportot tárolóként ugyanazt az előfizetést virtuális hálózati átjárók tulajdonosai. Kör felhasználók is beválthatja engedélyek (virtuális hálózatonként egy engedélyezési).

A kapcsolatcsoport tulajdonosát a rendelkezik módosítja, és bármikor engedélyek visszavonása. Visszavonni egy engedélyezési eredményezi, az összes hivatkozás kapcsolatok törlődnek az előfizetésből, amelyek hozzáférését visszavonták.

### <a name="circuit-owner-operations"></a>Kapcsolatcsoport-tulajdonos műveletek

**Az engedély létrehozása**

A kapcsolatcsoport tulajdonosát engedélyezési hoz létre. Az eredmény, használhatja a kapcsolatcsoport felhasználó való csatlakozáshoz a virtuális hálózati átjárók az ExpressRoute-kapcsolatcsoport engedélykulcs létrehozásához. Az engedély csak egy kapcsolat érvénytelen.

A következő parancsmag kódrészletben láthatja, az engedély létrehozása:

```powershell
$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$auth1 = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
```


Ez a válasz a hitelesítési kulcs és az állapot fogja tartalmazni:

    Name                   : MyAuthorization1
    Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
    Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
    AuthorizationKey       : ####################################
    AuthorizationUseStatus : Available
    ProvisioningState      : Succeeded



**Engedélyek áttekintése**

A kapcsolatcsoport tulajdonosát tekintse át a következő parancsmag futtatásával egy adott körön kiállított összes engedélyek:

```powershell
$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Engedélyek hozzáadása**

A kapcsolatcsoport tulajdonosát a következő parancsmag használatával adhat hozzá engedélyek:

```powershell
$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization2"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Engedélyek törlése**

A kapcsolatcsoport tulajdonosát is revoke vagy törlése a felhasználó engedélyek a következő parancsmagot:

```powershell
Remove-AzureRmExpressRouteCircuitAuthorization -Name "MyAuthorization2" -ExpressRouteCircuit $circuit
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit
```    

### <a name="circuit-user-operations"></a>Kör felhasználói műveletek

A kapcsolatcsoport-felhasználó a társ-Azonosítóval és a engedélykulcs a kapcsolatcsoport tulajdonosát a kell. A hitelesítési kulcs egy GUID Azonosítót.

Társ azonosítója a következő paranccsal ellenőrizhetők:

```powershell
Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**Egy kapcsolat hitelesítési beváltani**

A kapcsolatcsoport felhasználó futtathatja egy hivatkozás hitelesítési beváltani a következő parancsmagot:

```powershell
$id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"    
$gw = Get-AzureRmVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Egy kapcsolat hitelesítési felszabadítása**

Törölni kell a kapcsolat az ExpressRoute-kapcsolatcsoport a virtuális hálózatra hivatkozó engedélyezési is megjelenhetnek.

## <a name="modify-a-virtual-network-connection"></a>Módosítsa a virtuális hálózati kapcsolat
Frissítheti az egyes tulajdonságait a virtuális hálózati kapcsolat. 

**A kapcsolat súly frissítése**

A virtuális hálózat több ExpressRoute-Kapcsolatcsoportok lehet csatlakoztatni. Egynél több ExpressRoute-kapcsolatcsoportot ugyanazon előtaggal kaphat. Az előtag szánt forgalom küldése mely kapcsolat módosíthatja *routingweight értékének* kapcsolat. Forgalom küldi a kapcsolatot a legmagasabb *routingweight értékének*.

```powershell
$connection = Get-AzureRmVirtualNetworkGatewayConnection -Name "MyVirtualNetworkConnection" -ResourceGroupName "MyRG"
$connection.RoutingWeight = 100
Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
```

A számos *routingweight értékének* mely 0 és 32 000. Az alapértelmezett értéke 0.

## <a name="next-steps"></a>További lépések
További információ az ExpressRoute-tal kapcsolatban: [ExpressRoute – Gyakori kérdések](expressroute-faqs.md).
