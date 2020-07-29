---
title: 'Azure ExpressRoute: VNet összekapcsolása az áramkörrel: CLI'
description: Ez a cikk bemutatja, hogyan kapcsolhat virtuális hálózatokat (virtuális hálózatok) a ExpressRoute-áramkörökhöz a Resource Manager-alapú üzemi modell és a parancssori felület használatával.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: how-to
ms.date: 07/27/2020
ms.author: cherylmc
ms.openlocfilehash: 3d51409baf9d5ca6ce90f1367ac128d7d47a9757
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87325253"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-cli"></a>Virtuális hálózat összekapcsolása egy ExpressRoute-áramkörrel a CLI használatával

Ez a cikk segítséget nyújt a Virtual Networks (virtuális hálózatok) és az Azure ExpressRoute-áramkörök összekapcsolásához a CLI használatával. Az Azure CLI használatával való hivatkozáshoz a virtuális hálózatokat a Resource Manager-alapú üzemi modell használatával kell létrehozni. Lehetnek ugyanabban az előfizetésben vagy egy másik előfizetés részeként. Ha másik módszert szeretne használni a VNet ExpressRoute-áramkörhöz való összekapcsolásához, a következő listából választhat egy cikket:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Videó – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-linkvnet-classic.md)
> 

## <a name="configuration-prerequisites"></a>Konfigurációs előfeltételek

* Szüksége lesz a parancssori felület (CLI) legújabb verziójára. További információ: [Az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli).

* A konfigurálás megkezdése előtt át kell tekintenie az [előfeltételeket](expressroute-prerequisites.md), az [útválasztási követelményeket](expressroute-routing.md)és a [munkafolyamatokat](expressroute-workflows.md) .

* Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége. 
  * Az utasításokat követve [hozzon létre egy ExpressRoute áramkört](howto-circuit-cli.md) , és engedélyezze az áramkört a kapcsolat szolgáltatója számára. 
  * Győződjön meg arról, hogy az Ön áramköréhez konfigurálva van az Azure-beli magánhálózati kapcsolat. Az útválasztási utasításokért tekintse meg az [Útválasztás konfigurálása](howto-routing-cli.md) című cikket. 
  * Győződjön meg arról, hogy az Azure-beli magánhálózati társítás konfigurálva van. A hálózat és a Microsoft közötti BGP-társat úgy kell beállítani, hogy engedélyezze a végpontok közötti kapcsolatot.
  * Győződjön meg arról, hogy van egy virtuális hálózat és egy virtuális hálózati átjáró létrehozva és teljesen kiépítve. A [ExpressRoute virtuális hálózati átjárójának konfigurálásához](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli)kövesse az utasításokat. Ügyeljen arra, hogy a következőt használja: `--gateway-type ExpressRoute` .

* Akár 10 virtuális hálózatot is összekapcsolhat egy standard ExpressRoute áramkörrel. Szabványos ExpressRoute áramkör használata esetén minden virtuális hálózatnak ugyanabban a geopolitikai régióban kell lennie. 

* Egyetlen VNet legfeljebb négy ExpressRoute-áramkörhöz lehet kapcsolni. Az alábbi folyamat használatával hozzon létre egy új kapcsolati objektumot minden olyan ExpressRoute-áramkörhöz, amelyhez csatlakozik. A ExpressRoute-áramkörök lehetnek ugyanabban az előfizetésben, különböző előfizetésekben vagy a kettő kombinációjában is.

* Ha engedélyezi a ExpressRoute prémium bővítményt, összekapcsolhat egy virtuális hálózatot az ExpressRoute-áramkör geopolitikai régióján kívül, vagy nagyobb számú virtuális hálózatot csatlakoztathat a ExpressRoute-áramkörhöz. A prémium szintű bővítménnyel kapcsolatos további információkért tekintse meg a [gyakori kérdéseket](expressroute-faqs.md).

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Ugyanahhoz az előfizetéshez tartozó virtuális hálózat összekapcsolása egy áramkörrel

A példa használatával összekapcsolhatja a virtuális hálózati átjárót egy ExpressRoute-áramkörhöz. Győződjön meg arról, hogy a virtuális hálózati átjáró létrejött, és készen áll a csatolásra a parancs futtatása előtt.

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Egy másik előfizetéshez tartozó virtuális hálózat bevonása egy kapcsolatcsoportba

Megoszthat egy ExpressRoute-áramkört több előfizetésen keresztül is. Az alábbi ábra egy egyszerű sematikus ábrát mutat be, hogy a megosztás hogyan működik a ExpressRoute-áramkörök több előfizetésen keresztül.

A nagyméretű felhőben lévő kisebb felhőket a szervezeten belüli különböző részlegekhez tartozó előfizetések ábrázolására használják. A szervezeten belüli részlegek saját előfizetést használhatnak a szolgáltatásaik üzembe helyezéséhez – de egyetlen ExpressRoute áramkört is megoszthatnak a helyszíni hálózathoz való kapcsolódáshoz. Egyetlen részleg (ebben a példában: IT) a ExpressRoute áramkör tulajdonosa lehet. A szervezeten belüli egyéb előfizetések a ExpressRoute áramkört is használhatják.

> [!NOTE]
> A dedikált áramkör kapcsolati és sávszélesség-díjait a rendszer a ExpressRoute Circuit tulajdonosára alkalmazza. Minden virtuális hálózat ugyanazt a sávszélességet használja.
> 
> 

![Előfizetések közötti kapcsolat](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration---circuit-owners-and-circuit-users"></a>Felügyelet – az áramkör tulajdonosai és felhasználói

Az "áramkör tulajdonosa" a ExpressRoute Circuit erőforrásának jogosult Kiemelt felhasználója. Az áramkör tulajdonosa létrehozhat olyan engedélyeket, amelyeket az "áramköri felhasználók" is beválthatnak. Az áramköri felhasználók olyan virtuális hálózati átjárók tulajdonosai, amelyek nem a ExpressRoute áramkörhöz tartozó előfizetésben találhatók. Az áramköri felhasználók beválthatják az engedélyeket (egy-egy hitelesítés virtuális hálózatonként).

Az áramkör tulajdonosa bármikor módosíthatja és visszavonhatja az engedélyeket. Az engedélyek visszavonása után az összes kapcsolati kapcsolat törlődik az előfizetésből, amelynek a hozzáférését visszavonták.

### <a name="circuit-owner-operations"></a>Áramköri tulajdonosi műveletek

**Engedélyezés létrehozása**

Az áramkör tulajdonosa létrehoz egy engedélyt, amely létrehoz egy engedélyezési kulcsot, amelyet egy áramköri felhasználó használhat a virtuális hálózati átjárók ExpressRoute áramkörhöz való kapcsolódásához. Egy engedély csak egy kapcsolatban érvényes.

Az alábbi példa bemutatja, hogyan hozhat létre engedélyt:

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization
```

A válasz az engedélyezési kulcsot és az állapotot tartalmazza:

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

Az áramkör tulajdonosa a következő példa futtatásával áttekintheti az adott áramkörön kiadott összes engedélyt:

```azurecli
az network express-route auth list --circuit-name MyCircuit -g ExpressRouteResourceGroup
```

**Engedélyek hozzáadása**

Az áramkör tulajdonosa a következő példa használatával adhat hozzá engedélyeket:

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

**Engedélyek törlése**

Az áramkör tulajdonosa visszavonhatja vagy törölheti az engedélyeket a felhasználónak a következő példa futtatásával:

```azurecli
az network express-route auth delete --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

### <a name="circuit-user-operations"></a>Áramkör felhasználói műveletei

Az áramkör-felhasználónak szüksége van a társ-AZONOSÍTÓra és egy engedélyezési kulcsra az áramköri tulajdonostól. Az engedélyezési kulcs egy GUID.

```powershell
Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**A kapcsolódási engedély beváltása**

Az áramkör-felhasználó a következő példát futtathatja egy hivatkozás engedélyezésének beváltásához:

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit --authorization-key "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**A kapcsolódási engedély felszabadítása**

Az engedélyezést a ExpressRoute áramkört a virtuális hálózathoz összekötő kapcsolat törlésével lehet felszabadítani.

## <a name="modify-a-virtual-network-connection"></a>Virtuális hálózati kapcsolatok módosítása
A virtuális hálózati kapcsolatok bizonyos tulajdonságainak frissítését végezheti el. 

**A kapcsolatok súlyozásának frissítése**

A virtuális hálózat több ExpressRoute-áramkörhöz is csatlakoztatható. Előfordulhat, hogy ugyanazt az előtagot több ExpressRoute áramkörből is megkapja. Annak kiválasztásához, hogy melyik kapcsolódási forgalmat küldje el az előtagja számára, módosíthatja a kapcsolatok *RoutingWeight* . A rendszer a forgalmat a legmagasabb *RoutingWeight*kapcsolatban küldi el.

```azurecli
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --routing-weight 100
```

A *RoutingWeight* tartománya 0 és 32000 között van. Az alapértelmezett érték a 0.

## <a name="configure-expressroute-fastpath"></a>ExpressRoute-FastPath konfigurálása 
A [ExpressRoute FastPath](expressroute-about-virtual-network-gateways.md) is engedélyezheti, ha a virtuális hálózati átjáró Ultra Performance vagy ErGw3AZ. A FastPath a helyszíni hálózat és a virtuális hálózat között a másodpercenkénti adatelérési utak, például a csomagok másodpercenkénti számát és a kapcsolatok másodpercenkénti számát javítja. 

**FastPath konfigurálása új kapcsolatban**

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

**Meglévő kapcsolatok frissítése a FastPath engedélyezéséhez**

```azurecli
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true
```

## <a name="next-steps"></a>További lépések

A ExpressRoute kapcsolatos további információkért tekintse meg a [ExpressRoute gyakori kérdések](expressroute-faqs.md)című témakört.
