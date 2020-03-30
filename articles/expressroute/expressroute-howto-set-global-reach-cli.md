---
title: 'Azure ExpressRoute: Az ExpressRoute globális eléréskonfigurálása: CLI'
description: Ez a cikk segítséget nyújt az ExpressRoute-áramkörök összekapcsolásában, hogy magánhálózatot hozhassanak létre a helyszíni hálózatok között, és engedélyezze a Globális elérést.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/12/2018
ms.author: jaredro
ms.openlocfilehash: a39cf4e09a70ca2b1225d699c84abf0e7f1d2eab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476406"
---
# <a name="configure-expressroute-global-reach-by-using-the-azure-cli"></a>Az ExpressRoute globális elérésének konfigurálása az Azure CLI használatával

Ez a cikk segít az Azure ExpressRoute globális elérés konfigurálása az Azure CLI használatával. További információ: [ExpressRoute Global Reach](expressroute-global-reach.md).
 
A konfiguráció megkezdése előtt hajtsa végre az alábbi követelményeket:

* Telepítse az Azure CLI legújabb verzióját. További információk: [Az Azure CLI telepítése](/cli/azure/install-azure-cli) és [Bevezetés az Azure CLI használatába](/cli/azure/get-started-with-azure-cli).
* Ismerje meg az ExpressRoute-kapcsolatlétesítési [munkafolyamatokat.](expressroute-workflows.md)
* Győződjön meg arról, hogy az ExpressRoute-áramkörök kiépített állapotban vannak.
* Győződjön meg arról, hogy az Azure privát társviszony-létesítés konfigurálva van az ExpressRoute-áramkörök.  

### <a name="sign-in-to-your-azure-account"></a>Jelentkezzen be az Azure-fiókjába

A konfiguráció elindításához jelentkezzen be az Azure-fiókjába. A következő parancs megnyitja az alapértelmezett böngészőt, és kéri az Azure-fiókjához való bejelentkezési hitelesítő adatokat:  

```azurecli
az login
```

Ha több Azure-előfizetéssel rendelkezik, ellenőrizze a fiók előfizetéseit:

```azurecli
az account list
```

Adja meg a használni kívánt előfizetést:

```azurecli
az account set --subscription <your subscription ID>
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>Az ExpressRoute-áramkörök azonosítása a konfigurációhoz

Az ExpressRoute globális elérése bármely két ExpressRoute-kapcsolat között engedélyezhető, feltéve, hogy azok támogatott országokban/régiókban találhatók, és különböző társviszony-létesítési helyeken jöttek létre. Ha az előfizetés mindkét kapcsolatcsoport tulajdonosa, kiválaszthatja, hogy a konfiguráció futtatásához a jelen cikk későbbi részében ismertetett módon futtassa a konfigurációt. Ha a két kapcsolatcsoport különböző Azure-előfizetésekben található, egy Azure-előfizetésből kell engedélyt adnia, és át kell adnia az engedélyezési kulcsban, amikor a másik Azure-előfizetésben futtatja a konfigurációs parancsot.

## <a name="enable-connectivity-between-your-on-premises-networks"></a>A helyszíni hálózatok közötti kapcsolat engedélyezése

Ha a parancsot a kapcsolat engedélyezéséhez futtatja, vegye figyelembe a paraméterértékekre vonatkozó alábbi követelményeket:

* *társ-áramkör* legyen a teljes erőforrás-azonosító. Példa:

  > /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}

* *a címelőtagnak* "/29" IPv4 alhálózatnak kell lennie (például "10.0.0.0/29"). Az alhálózat IP-címeit használjuk a két ExpressRoute-kapcsolat létrehozásához a két ExpressRoute-kapcsolat között. Nem használhat címeket ebben az alhálózatban az Azure virtuális hálózataiban vagy a helyszíni hálózatokban.

Két ExpressRoute-áramkör csatlakoztatásához futtassa a következő CLI parancsot:

```azurecli
az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29>
```

A CLI kimenet így néz ki:

```output
{
  "addressPrefix": "<__.__.__.__/29>",
  "authorizationKey": null,
  "circuitConnectionStatus": "Connected",
  "etag": "W/\"48d682f9-c232-4151-a09f-fab7cb56369a\"",
  "expressRouteCircuitPeering": {
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit1Name>/peerings/AzurePrivatePeering",
    "resourceGroup": "<ResourceGroupName>"
  },
  "id": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit1Name>/peerings/AzurePrivatePeering/connections/<ConnectionName>",
  "name": "<ConnectionName>",
  "peerExpressRouteCircuitPeering": {
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/<Circuit2ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit2Name>/peerings/AzurePrivatePeering",
    "resourceGroup": "<Circuit2ResourceGroupName>"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "<ResourceGroupName>",
  "type": "Microsoft.Network/expressRouteCircuits/peerings/connections"
}
```

Ha ez a művelet befejeződött, a két ExpressRoute-kapcsolaton keresztül mindkét oldalon a helyszíni hálózatok között lesz kapcsolat.

## <a name="enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions"></a>Az ExpressRoute-áramkörök közötti kapcsolat engedélyezése különböző Azure-előfizetésekben

Ha a két kapcsolatcsoport nem ugyanabban az Azure-előfizetésben van, engedélyre van szüksége. A következő konfigurációban a 2-es áramkör előfizetésében generál ja az engedélyezést, és átadja az engedélyezési kulcsot az 1-es áramkörnek.

1. Engedélyezési kulcs létrehozása:

   ```azurecli
   az network express-route auth create --circuit-name <Circuit2Name> -g <Circuit2ResourceGroupName> -n <AuthorizationName>
   ```

   A CLI kimenet így néz ki:

   ```output
   {
     "authorizationKey": "<authorizationKey>",
     "authorizationUseStatus": "Available",
     "etag": "W/\"cfd15a2f-43a1-4361-9403-6a0be00746ed\"",
     "id": "/subscriptions/<SubscriptionID>/resourceGroups/<Circuit2ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit2Name>/authorizations/<AuthorizationName>",
     "name": "<AuthorizationName>",
     "provisioningState": "Succeeded",
     "resourceGroup": "<Circuit2ResourceGroupName>",
     "type": "Microsoft.Network/expressRouteCircuits/authorizations"
   }
   ```

1. Jegyezze fel mind az erőforrás-azonosítót, mind a 2-es áramkör engedélyezési kulcsát.

1. Futtassa a következő parancsot az 1-es áramkörön, és adja át a 2-es áramkör erőforrás-azonosítóját és engedélyezési kulcsát:

   ```azurecli
   az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29> --authorization-key <authorizationKey>
   ```

Ha ez a művelet befejeződött, a két ExpressRoute-kapcsolaton keresztül mindkét oldalon a helyszíni hálózatok között lesz kapcsolat.

## <a name="get-and-verify-the-configuration"></a>A konfiguráció be- és ellenőrzése

A következő paranccsal ellenőrizze a konfigurációt azon az áramkörön, ahol a konfiguráció készült (az előző példában az 1. áramkör):

```azurecli
az network express-route show -n <CircuitName> -g <ResourceGroupName>
```

A CLI kimeneten megjelenik a *CircuitConnectionStatus*. Azt jelzi, hogy a két áramkör közötti kapcsolat létrejött-e ("Csatlakoztatva"), vagy nem ("Leválasztva"). 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>A helyszíni hálózatok közötti kapcsolat letiltása

A kapcsolat letiltásához futtassa a következő parancsot azon az áramkörön, ahol a konfiguráció készült (a korábbi példában az 1-es áramkör).

```azurecli
az network express-route peering connection delete -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName>
```

A ```show``` parancs segítségével ellenőrizze az állapotot.

Ha ez a művelet befejeződött, a továbbiakban nem lesz kapcsolat a helyszíni hálózatok között az ExpressRoute-áramkörökön keresztül.

## <a name="next-steps"></a>További lépések

* [További információ az ExpressRoute globális eléréséről](expressroute-global-reach.md)
* [ExpressRoute-kapcsolat ellenőrzése](expressroute-troubleshooting-expressroute-overview.md)
* [ExpressRoute-kapcsolat virtuális hálózattal](expressroute-howto-linkvnet-arm.md)
