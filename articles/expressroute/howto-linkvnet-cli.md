---
title: "Virtuális hálózat csatolása ExpressRoute-kapcsolatcsoportot: parancssori felület: Azure |} Microsoft Docs"
description: "Ez a dokumentum áttekintést csatolása a virtuális hálózatokon (Vnetek) az ExpressRoute-Kapcsolatcsoportok a Resource Manager üzembe helyezési modellben és a parancssori felület használatával."
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlit
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/25/2017
ms.author: anzaman,cherylmc
ms.openlocfilehash: fa55cbad9fca799faff4e4cef87f9eedb8d2023f
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-cli"></a>Egy virtuális hálózathoz csatlakozni egy ExpressRoute-kapcsolatcsoportot parancssori felület használatával

Ez a cikk segít virtuális hálózatokról (Vnetekről) hivatkozásra az Azure ExpressRoute-Kapcsolatcsoportok parancssori felület használatával. Azure parancssori felület használatával csatolni a virtuális hálózatok segítségével kell létrehozni a Resource Manager üzembe helyezési modellben. Lehetnek ugyanazon előfizetés, vagy egy másik előfizetés részeként. Ha szeretne egy másik módszer segítségével csatlakozzon a virtuális hálózat ExpressRoute-kapcsolatcsoportot, kiválaszthatja a cikk az alábbi listából:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video - Azure-portálon](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-linkvnet-classic.md)
> 

## <a name="configuration-prerequisites"></a>Konfigurációs előfeltételek

* A parancssori felület (CLI) legújabb verziójára van szüksége. További információkért lásd: [Azure CLI 2.0 telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli).
* Át kell tekintenie a [Előfeltételek](expressroute-prerequisites.md), [útválasztási követelmények](expressroute-routing.md), és [munkafolyamatok](expressroute-workflows.md) konfigurálás elkezdése előtt.
* Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége. 
  * Kövesse az utasításokat [ExpressRoute-kapcsolatcsoportot létrehozni](howto-circuit-cli.md) és a kapcsolatcsoport szerint a kapcsolat szolgáltatójánál engedélyezve van. 
  * Győződjön meg arról, hogy rendelkezik az Azure magánhálózati társviszony-létesítés a kapcsolatcsoport konfigurálva. Tekintse meg a [konfigurálja az útválasztást](howto-routing-cli.md) útválasztási miként. 
  * Győződjön meg arról, hogy konfigurálva van-e az Azure magánhálózati társviszony-létesítés. A BGP társviszony-létesítés között a hálózati és a Microsoft be kell, hogy engedélyezheti a végpontok közötti kapcsolat.
  * Győződjön meg arról, hogy a virtuális hálózat és a virtuális hálózati átjáró létrehozása, és teljesen kiépítve. Kövesse az utasításokat [a virtuális hálózati átjáró konfigurálása ExpressRoute](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli). Használjon `--gateway-type ExpressRoute`.

* Legfeljebb 10 virtuális hálózatok hozzákapcsolhatja egy szabványos ExpressRoute-kapcsolatcsoportot. Az összes virtuális hálózatot geopolitikai ugyanabban a régióban kell lennie, egy szabványos ExpressRoute-kapcsolatcsoportot használatakor. 

* Ha engedélyezi a prémium szintű ExpressRoute-bővítmény, egy virtuális hálózaton kívül az ExpressRoute-kapcsolatcsoport geopolitikai régiója hivatkozásra, vagy virtuális hálózatok nagyobb számú kapcsolódni az ExpressRoute-kapcsolatcsoportot. A prémium szintű bővítmény kapcsolatos további információkért tekintse meg a [gyakran ismételt kérdések](expressroute-faqs.md).

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>A virtuális hálózati ugyanahhoz az előfizetéshez csatlakozni expressroute-kapcsolatcsoporthoz

A példa használatával ExpressRoute-kapcsolatcsoportot virtuális hálózati átjáró képes kapcsolódni. Győződjön meg arról, hogy a virtuális hálózati átjáró jön létre, és készen áll a létrehozhatja, ha a parancs futtatása előtt.

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Egy másik előfizetéshez tartozó virtuális hálózat bevonása egy kapcsolatcsoportba

Több előfizetés ExpressRoute-kapcsolatcsoportot lehet megosztani. Az alábbi ábra egy egyszerű sematikus ExpressRoute-Kapcsolatcsoportok megosztási hogyan működik a több előfizetésekhez.

A nagy felhőben kisebb felhők egyes szervezet különböző részlegei tartozó előfizetések megjelenítésére szolgál. A saját előfizetés üzembe helyezéséhez, a szolgáltatások –, de használhatja a szervezeten belüli osztályok mindegyikének oszthatnak meg csatlakozni a helyi hálózat a egyetlen ExpressRoute-kapcsolatcsoportot. Egy részleghez (ebben a példában: informatikai) az ExpressRoute-kapcsolatcsoport rendelkezhet. A szervezeten belüli más előfizetések használhatja az ExpressRoute-kapcsolatcsoportot.

> [!NOTE]
> A dedikált kör kapcsolat és a sávszélesség költségek az ExpressRoute-kapcsolatcsoport tulajdonosát alkalmazandó. Az összes virtuális hálózatot megosztani a azonos sávszélességet.
> 
> 

![Az előfizetések közötti kapcsolathoz](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration---circuit-owners-and-circuit-users"></a>Felügyeleti - áramkör tulajdonosa és a kapcsolatcsoport felhasználók

A kapcsolatcsoport tulajdonosát a ExpressRoute-kapcsolatcsoport erőforrás Power jogosultsága. A kapcsolatcsoport tulajdonosát hozhat létre, amely a "Kör felhasználók" is váltható engedélyek. Kör felhasználók, amelyek nem tartoznak a ExpressRoute-kapcsolatcsoportot tárolóként ugyanazt az előfizetést virtuális hálózati átjárók tulajdonosai. Kör felhasználók is beválthatja engedélyek (virtuális hálózatonként egy engedélyezési).

A kapcsolatcsoport tulajdonosát a rendelkezik módosítja, és bármikor engedélyek visszavonása. Amikor visszavon egy engedélyezési, minden kapcsolatoknál törlődnek az előfizetésből, amelyek hozzáférését visszavonták.

### <a name="circuit-owner-operations"></a>Kapcsolatcsoport-tulajdonos műveletek

**Az engedély létrehozása**

A kapcsolatcsoport tulajdonosát létrehoz egy engedélyezésre, amelynek segítségével a kapcsolatcsoport felhasználó csatlakozzon a virtuális hálózati átjárók az ExpressRoute-kapcsolatcsoport engedélykulcs hoz létre. Az engedély csak egy kapcsolat érvénytelen.

A következő példa bemutatja, hogyan hozzon létre egy engedélyezési:

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization
```

A válasz tartalmazza az engedélyezési kulcsot és annak állapotát:

```azurecli
"authorizationKey": "0a7f3020-541f-4b4b-844a-5fb43472e3d7",
"authorizationUseStatus": "Available",
"etag": "W/\"010353d4-8955-4984-807a-585c21a22ae0\"",
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/authorizations/MyAuthorization1",
"name": "MyAuthorization1",
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup"
```

**Engedélyek áttekintése**

A kapcsolatcsoport tulajdonosát futtassa a következő példa egy adott körön kiállított összes engedélyek tekinthetők át:

```azurecli
az network express-route auth list --circuit-name MyCircuit -g ExpressRouteResourceGroup
```

**Engedélyek hozzáadása**

A kapcsolatcsoport tulajdonosát az alábbi példa használatával adhat hozzá engedélyek:

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

**Engedélyek törlése**

A kapcsolatcsoport tulajdonosát is visszavonni vagy törlése a felhasználó engedélyek futtassa az alábbi példa:

```azurecli
az network express-route auth delete --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

### <a name="circuit-user-operations"></a>Kör felhasználói műveletek

A kapcsolatcsoport-felhasználó a társ-Azonosítóval és a engedélykulcs a kapcsolatcsoport tulajdonosát a kell. A hitelesítési kulcs egy GUID Azonosítót.

```azurecli
Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**Egy kapcsolat hitelesítési beváltani**

A kapcsolatcsoport felhasználó futtathatja a következő példa egy hivatkozás hitelesítési beváltani:

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit --authorization-key "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Egy kapcsolat hitelesítési felszabadítása**

Törölni kell a kapcsolat az ExpressRoute-kapcsolatcsoport a virtuális hálózatra hivatkozó engedélyezési is megjelenhetnek.

## <a name="next-steps"></a>Következő lépések

További információ az ExpressRoute-tal kapcsolatban: [ExpressRoute – Gyakori kérdések](expressroute-faqs.md).