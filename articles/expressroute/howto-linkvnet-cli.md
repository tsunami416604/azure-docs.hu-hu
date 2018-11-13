---
title: 'Virtuális hálózat összekapcsolása egy ExpressRoute-kapcsolatcsoport: parancssori felület: Azure |} A Microsoft Docs'
description: Ez a dokumentum az ExpressRoute-Kapcsolatcsoportok összekapcsolása virtuális hálózatok (Vnetek) áttekintést nyújt a Resource Manager üzemi modell és a CLI használatával.
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlit
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2018
ms.author: anzaman,cherylmc
ms.openlocfilehash: 1db8a85f40182d954fdd711b203a6ee42aecc694
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51248911"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-cli"></a>Virtuális hálózat összekapcsolása egy ExpressRoute-kapcsolatcsoporthoz parancssori felület használatával

Ez a cikk segít, virtuális hálózatok (Vnetek) Azure ExpressRoute-Kapcsolatcsoportok parancssori felület használatával. Azure CLI-vel csatolásához a virtuális hálózatok kell létrehozni a Resource Manager üzemi modell használatával. Lehetnek azonos előfizetésben, vagy egy másik előfizetés részeként. Ha szeretne egy másik módszer segítségével csatlakozhat virtuális hálózatához egy ExpressRoute-kapcsolatcsoporthoz, kiválaszthatja a cikk az alábbi listából:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Videó – Azure portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-linkvnet-classic.md)
> 

## <a name="configuration-prerequisites"></a>Konfigurációs előfeltételek

* A parancssori felület (CLI) legújabb verziójára van szüksége. További információkért lásd: [az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli).

* Át kell tekintenie a [Előfeltételek](expressroute-prerequisites.md), [útválasztási követelmények](expressroute-routing.md), és [munkafolyamatok](expressroute-workflows.md) konfigurálás megkezdése előtt.

* Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége. 
  * Kövesse az utasításokat [ExpressRoute-kapcsolatcsoport létrehozása](howto-circuit-cli.md) , és a kapcsolatcsoport a kapcsolatszolgáltató által engedélyezett. 
  * Gondoskodjon arról, hogy az Azure privát társviszony-létesítést a kapcsolatcsoporthoz konfigurálva. Tekintse meg a [konfigurálja az útválasztást](howto-routing-cli.md) cikk vonatkozó útválasztási utasításokat. 
  * Győződjön meg arról, hogy konfigurálva van-e az Azure privát társviszony-létesítés. A BGP-társviszonyt a hálózat és a Microsoft között fel kell lennie, így engedélyezheti a végpontok közötti kapcsolat.
  * Gondoskodjon arról, hogy egy virtuális hálózat és a egy virtuális hálózati átjáró létrehozása, és teljesen kiépítve. Kövesse az utasításokat [az ExpressRoute virtuális hálózati átjáró konfigurálása](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli). Ügyeljen arra, hogy `--gateway-type ExpressRoute`.

* Legfeljebb 10 virtuális hálózatok kapcsolat egy standard ExpressRoute-kapcsolatcsoporthoz. Az összes virtuális hálózatok ugyanazon geopolitikai régióban kell lennie, a standard ExpressRoute-kapcsolatcsoport használatánál. 

* Egyetlen virtuális hálózat legfeljebb négy ExpressRoute-Kapcsolatcsoportok lehet kapcsolódni. Egyes ExpressRoute-kapcsolatcsoporthoz csatlakozik egy új kapcsolat objektumot létrehozásához használja az alábbi folyamatot. Az ExpressRoute-Kapcsolatcsoportok ugyanahhoz az előfizetéshez tartozik, eltérő előfizetésekben vagy mindkét vegyesen is lehet.

* Ha engedélyezi az ExpressRoute prémium bővítmény, csatolása a virtuális hálózat az ExpressRoute-kapcsolatcsoport a geopolitikai régión kívül, vagy több virtuális hálózat csatlakozik az ExpressRoute-kapcsolatcsoport. A premium bővítményt kapcsolatos további információkért lásd: a [– gyakori kérdések](expressroute-faqs.md).

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Azonos előfizetésben található virtuális hálózat csatlakoztatása egy kapcsolatcsoporthoz

A példa használatával csatlakozni tud a virtuális hálózati átjáró csatlakoztatása egy ExpressRoute-kapcsolatcsoporthoz. Győződjön meg arról, hogy a virtuális hálózati átjáró jön létre, és készen áll a csatolás, a parancs futtatása előtt.

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Egy másik előfizetéshez tartozó virtuális hálózat bevonása egy kapcsolatcsoportba

ExpressRoute-kapcsolatcsoport több előfizetésre kiterjedő megoszthatja. Az alábbi ábra egy egyszerű az ExpressRoute-Kapcsolatcsoportok hogyan megosztási alkotások sematikus több előfizetéshez.

A nagyméretű felhőbeli belül a kisebb felhők mindegyike egy szervezet különböző részlegei tartozó előfizetések megjelenítésére szolgál. Minden, a szervezeti egységek, a szervezeten belül használhatja a saját előfizetés üzembe helyezéséhez a szolgáltatásaik –, de egyetlen ExpressRoute-kapcsolatcsoporthoz szeretne csatlakozni a helyszíni hálózaton is megoszthatja. Egyetlen részleg (ebben a példában: informatikai) is a saját ExpressRoute-kapcsolatcsoportot. Más előfizetésekre, a szervezeten belül használható az ExpressRoute-kapcsolatcsoporthoz.

> [!NOTE]
> Az ExpressRoute-kapcsolatcsoport tulajdonosát a kapcsolatot és a sávszélesség díjak a dedikált kapcsolatcsoport lépnek érvénybe. Minden virtuális hálózat ossza meg ugyanazt a sávszélesség.
> 
> 

![Az előfizetések közötti kapcsolat](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration---circuit-owners-and-circuit-users"></a>Felügyelet – kapcsolatcsoport tulajdonosai és a kapcsolatcsoport felhasználóinak

A kapcsolatcsoport tulajdonosát a jogosult kiemelt felhasználó az ExpressRoute-kapcsolatcsoport erőforrás. A kapcsolatcsoport tulajdonosát engedélyeket, melyeket a "Kapcsolatcsoport felhasználóinak" hozhat létre. Kapcsolatcsoport felhasználók, amelyek nem az ExpressRoute-kapcsolatcsoporthoz, az egy előfizetésen belüli virtuális hálózati átjárók tulajdonosai. Kapcsolatcsoport felhasználóinak az engedélyek (egy engedélyezési száma virtuális hálózatonként) is beváltásához.

A kapcsolatcsoport tulajdonosát a rendelkezik módosítja, és bármikor engedélyek visszavonása. Egy engedélyezési visszavonja, amikor az összes hivatkozás kapcsolat lesznek törölve az előfizetést, amelynek hozzáférését visszavonták.

### <a name="circuit-owner-operations"></a>Kapcsolatcsoport-tulajdonos műveletek

**Az engedély létrehozása**

A kapcsolatcsoport tulajdonosát létrehoz egy engedélyezési, amely létrehoz egy engedélyezési kulcsot, amely egy kapcsolatcsoport-felhasználó által a virtuális hálózati átjárók az ExpressRoute-kapcsolatcsoporthoz való csatlakozáshoz használható. Egy engedélyezési csak egy kapcsolat érvényességét.

Az alábbi példa bemutatja, hogyan hozhat létre egy engedélyezési:

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization
```

A válasz tartalmazza a hitelesítési kulcsot és az állapotát:

```azurecli
"authorizationKey": "0a7f3020-541f-4b4b-844a-5fb43472e3d7",
"authorizationUseStatus": "Available",
"etag": "W/\"010353d4-8955-4984-807a-585c21a22ae0\"",
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/authorizations/MyAuthorization1",
"name": "MyAuthorization1",
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup"
```

**Az engedélyek áttekintéséhez**

A kapcsolatcsoport tulajdonosát tekintse át az adott expressroute-kapcsolatcsoporthoz az alábbi példa futtatása által kiállított összes engedélyek:

```azurecli
az network express-route auth list --circuit-name MyCircuit -g ExpressRouteResourceGroup
```

**Engedélyek hozzáadása**

A kapcsolatcsoport tulajdonosát a következő példa használatával adhat hozzá engedélyeket:

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

**Engedélyek törlése**

A kapcsolatcsoport tulajdonosát is visszavonása/törlése engedélyek a felhasználónak a következő példa futtatásával:

```azurecli
az network express-route auth delete --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

### <a name="circuit-user-operations"></a>Kapcsolatcsoport felhasználói műveletek

A kapcsolatcsoport-felhasználó a társ-azonosító és a egy engedélyezési kulcsot, a kapcsolatcsoport tulajdonosát a van szüksége. A hitelesítési kulcs a GUID Azonosítót.

```azurecli
Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**Hogy a kapcsolati engedély beváltása**

A kapcsolatcsoport-felhasználó futtathatja a következő példa való beváltása egy kapcsolat:

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit --authorization-key "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**A kapcsolati engedély felszabadítása**

Egy engedélyezési fel lehet szabadítani, a kapcsolat törlésével a hivatkozásokat az ExpressRoute-kapcsolatcsoport a virtuális hálózathoz.

## <a name="next-steps"></a>További lépések

További információ az ExpressRoute-tal kapcsolatban: [ExpressRoute – Gyakori kérdések](expressroute-faqs.md).