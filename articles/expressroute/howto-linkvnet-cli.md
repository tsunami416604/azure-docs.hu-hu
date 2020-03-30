---
title: 'Azure ExpressRoute: Virtuális hálózat összekapcsolása az áramkörhöz: CLI'
description: Ez a cikk bemutatja, hogyan kapcsolhatja össze a virtuális hálózatokat (virtuális hálózatokat) az ExpressRoute-áramkörökkel az Erőforrás-kezelő telepítési modelljének és a CLI-nek a használatával.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: cherylmc
ms.openlocfilehash: fdd809bcba703dbd8f9ee1e7c18185fd20e4586f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476134"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-cli"></a>Virtuális hálózat csatlakoztatása ExpressRoute-áramkörhöz CLI használatával

Ez a cikk segít a virtuális hálózatok (Virtuális hálózatok) összekapcsolása az Azure ExpressRoute-áramkörök cli használatával. Az Azure CLI használatával történő kapcsolathoz a virtuális hálózatokat az Erőforrás-kezelő telepítési modelljével kell létrehozni. Lehetnek ugyanabban az előfizetésben, vagy egy másik előfizetés egy része. Ha egy másik módszert szeretne használni a virtuális hálózat ExpressRoute-kapcsolathoz való csatlakoztatásához, válasszon ki egy cikket az alábbi listából:

> [!div class="op_single_selector"]
> * [Azure-portál](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [Powershell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Videó – Azure-portál](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-linkvnet-classic.md)
> 

## <a name="configuration-prerequisites"></a>Konfigurációs előfeltételek

* A parancssori felület (CLI) legújabb verziójára van szükség. További információ: [Install the Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

* A konfiguráció megkezdése előtt át kell tekintenie az [előfeltételeket,](expressroute-prerequisites.md) [az útválasztási követelményeket](expressroute-routing.md)és a [munkafolyamatokat.](expressroute-workflows.md)

* Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége. 
  * Kövesse az utasításokat [egy ExpressRoute-kapcsolat létrehozásához,](howto-circuit-cli.md) és a kapcsolatszolgáltató által engedélyezett áramkört. 
  * Győződjön meg arról, hogy az Azure privát társviszony-létesítés konfigurálva van a kapcsolatcsoporthoz. Az útválasztási utasításokat az útválasztási útmutató [konfigurálása](howto-routing-cli.md) című cikkben találja. 
  * Győződjön meg arról, hogy az Azure privát társviszony-létesítés konfigurálva van. A hálózat és a Microsoft közötti BGP-társviszony-létesítésnek fel kell lépnie, hogy engedélyezhesse a végpontok közötti kapcsolatot.
  * Győződjön meg arról, hogy van egy virtuális hálózat és egy virtuális hálózati átjáró létrehozott és teljesen kiépített. Kövesse az utasításokat [a Virtuális hálózati átjáró konfigurálásához az ExpressRoute számára.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli) Ügyeljen arra, `--gateway-type ExpressRoute`hogy használja a .

* Egy szabványos ExpressRoute-kapcsolathoz legfeljebb 10 virtuális hálózat kapcsolódhat. Minden virtuális hálózatnak ugyanabban a geopolitikai régióban kell lennie, ha szabványos ExpressRoute-áramkört használ. 

* Egyetlen virtuális hálózat legfeljebb négy ExpressRoute-kapcsolatcsoporthoz kapcsolható. Az alábbi folyamat segítségével új kapcsolatobjektumot hozhat létre minden olyan ExpressRoute-kapcsolati kapcsolathoz, amelyhez csatlakozik. Az ExpressRoute-áramkörök lehetnek ugyanabban az előfizetésben, különböző előfizetések, vagy a kettő kombinációja.

* Ha engedélyezi az ExpressRoute prémium szintű bővítményt, összekapcsolhat egy virtuális hálózatot az ExpressRoute-kapcsolat geopolitikai régióján kívül, vagy nagyobb számú virtuális hálózatot csatlakoztathat az ExpressRoute-kapcsolathoz. A prémium bővítményről a GYIK című [témakörben](expressroute-faqs.md)talál további információt.

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Virtuális hálózat csatlakoztatása ugyanabban az előfizetésben egy áramkörhöz

A példa segítségével virtuális hálózati átjárót csatlakoztathat egy ExpressRoute-kapcsolatcsoporthoz. A parancs futtatása előtt győződjön meg arról, hogy a virtuális hálózati átjáró létrejött, és készen áll a csatolásra.

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Egy másik előfizetéshez tartozó virtuális hálózat bevonása egy kapcsolatcsoportba

Az ExpressRoute-áramköröket több előfizetésen keresztül is megoszthatja. Az alábbi ábra egy egyszerű vázlatot mutat be arról, hogyan működik az ExpressRoute-áramkörök megosztása több előfizetésen keresztül.

A nagy felhőn belüli kisebb felhők mindegyike olyan előfizetéseket jelöl, amelyek a szervezet különböző részlegeihez tartoznak. A szervezet minden részlege használhatja a saját előfizetését a szolgáltatásaik üzembe helyezéséhez – de egyetlen ExpressRoute-kapcsolaton osztozhatnak a helyszíni hálózathoz való csatlakozáshoz. Egyetlen részleg (ebben a példában: IT) birtokolhatja az ExpressRoute-áramkört. A szervezeten belüli egyéb előfizetések használhatják az ExpressRoute-áramkört.

> [!NOTE]
> A dedikált kapcsolati és sávszélesség-díjak az ExpressRoute-kapcsolat i. Minden virtuális hálózat ugyanazt a sávszélességet használja.
> 
> 

![Előfizetések közötti kapcsolat](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration---circuit-owners-and-circuit-users"></a>Felügyelet - Áramkör-tulajdonosok és -felhasználók

A "Kapcsolatcsoport tulajdonosa" az ExpressRoute-áramköri erőforrás jogosult Energiafelhasználója. A körtulajdonos olyan engedélyeket hozhat létre, amelyeket a "Körfelhasználók" beválthatnak. A felhasználói csoportba tartozó felhasználók olyan virtuális hálózati átjárók tulajdonosai, amelyek nem ugyanabban az előfizetésben vannak, mint az ExpressRoute-kapcsolat. A felhasználói csoportba való belépést használó felhasználók virtuális hálózatonként egy engedélyezést válthatnak be.

A körtulajdonos bármikor módosíthatja és visszavonhatja az engedélyeket. Az engedélyezés visszavonásakor az összes kapcsolatkapcsolat törlődik attól az előfizetéstől, amelynek hozzáférését visszavonták.

### <a name="circuit-owner-operations"></a>Áramkörtulajdonosi műveletek

**Engedélyezés létrehozása**

A kapcsolatcsoport-tulajdonos létrehoz egy hitelesítési kulcsot, amely egy áramköri felhasználó által a virtuális hálózati átjárók ExpressRoute-kapcsolathoz való csatlakoztatására használható engedélyezési kulcsot hoz létre. Az engedélyezés csak egy kapcsolatra érvényes.

A következő példa bemutatja, hogyan hozhat létre engedélyezést:

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization
```

A válasz tartalmazza az engedélyezési kulcsot és az állapotot:

```output
"authorizationKey": "0a7f3020-541f-4b4b-844a-5fb43472e3d7",
"authorizationUseStatus": "Available",
"etag": "W/\"010353d4-8955-4984-807a-585c21a22ae0\"",
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/authorizations/MyAuthorization1",
"name": "MyAuthorization1",
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup"
```

**Engedélyek áttekintése**

A körtulajdonos a következő példával ellenőrizheti az adott áramkörhöz kiadott összes engedélyt:

```azurecli
az network express-route auth list --circuit-name MyCircuit -g ExpressRouteResourceGroup
```

**Engedélyek hozzáadása**

A körtulajdonos a következő példával adhat hozzá engedélyeket:

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

**Engedélyek törlése**

A körtulajdonos a következő példával vonhatja vissza/törölheti a felhasználó nak szóló engedélyeket:

```azurecli
az network express-route auth delete --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

### <a name="circuit-user-operations"></a>Felhasználói áramköri műveletek

A kapcsolathasználati felhasználói nak szüksége van a társazonosítóra és egy engedélyezési kulcsra a kapcsolattulajdonostól. Az engedélyezési kulcs guid.

```powershell
Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**Kapcsolati engedély beváltása**

A kapcsolatfelhasználó a következő példát futtathatja a hivatkozás-engedélyezés beváltásához:

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit --authorization-key "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Kapcsolatengedélyezési kiadás**

Az engedélyezést az ExpressRoute-kapcsolat virtuális hálózattal összekötő kapcsolat törlésével szabadíthatja fel.

## <a name="modify-a-virtual-network-connection"></a>Virtuális hálózati kapcsolat módosítása
A virtuális hálózati kapcsolat bizonyos tulajdonságai frissíthetők. 

**A kapcsolat súlyának frissítése**

A virtuális hálózat több ExpressRoute-kapcsolathoz is csatlakoztatható. Ugyanazt az előtagot több ExpressRoute-kapcsolatból is kaphatja. Ha meg szeretné választani, hogy melyik kapcsolatot küldje el az előtaghoz szánt forgalmat, módosíthatja a kapcsolat *RoutingWeight-ját.* A forgalom a legmagasabb RoutingWeight kapcsolaton keresztül lesz *elküldve.*

```azurecli
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --routing-weight 100
```

*Az RoutingWeight* tartománya 0 és 32000 között van. Az alapértelmezett érték a 0.

## <a name="configure-expressroute-fastpath"></a>ExpressRoute fastpath konfigurálása 
Engedélyezheti [expressroute FastPath,](expressroute-about-virtual-network-gateways.md) ha az ExpressRoute-áramkör [ExpressRoute Direct](expressroute-erdirect-about.md) és a virtuális newtork átjáró Ultra Performance vagy ErGw3AZ. A FastPath javítja az adatelérési út preformance-t, például a másodpercenkénti csomagokat és a helyszíni hálózat és a virtuális hálózat közötti másodpercenkénti kapcsolatokat. 

**A FastPath konfigurálása új kapcsolaton**

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

**Meglévő kapcsolat frissítése a FastPath engedélyezéséhez**

```azurecli
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true
```

## <a name="next-steps"></a>További lépések

Az ExpressRoute-ról további információt az [ExpressRoute gyakori kérdések című](expressroute-faqs.md)témakörben talál.
