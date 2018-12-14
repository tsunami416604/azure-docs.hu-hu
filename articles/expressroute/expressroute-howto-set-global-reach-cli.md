---
title: 'Globális elérhetőség az ExpressRoute konfigurálása: Az Azure CLI |} A Microsoft Docs'
description: Ez a cikk az ExpressRoute-Kapcsolatcsoportok privát hálózati a helyszíni hálózat között, és engedélyezze a globális elérhetőségű teszik hivatkozásra.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/12/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 9a8e0a5df9383d8e3d7159aa916b0e4fbfeea948
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53384052"
---
# <a name="configure-expressroute-global-reach-using-azure-cli-preview"></a>Konfigurálja az ExpressRoute globális elérhetőség az Azure CLI (előzetes verzió) használatával
Ez a cikk segít konfigurálása ExpressRoute globális elérhetőségű Azure CLI használatával. További információkért lásd: [ExpressRouteRoute globális elérhetőségű](expressroute-global-reach.md).
 
## <a name="before-you-begin"></a>Előkészületek
> [!IMPORTANT]
> A nyilvános előzetes verzióra nem vonatkozik szolgáltatói szerződés, és nem használható éles számítási feladatokra. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 


Mielőtt elkezdené a konfigurációt, ellenőrizze az alábbi követelményeknek kell.

* Telepítse az Azure CLI legújabb verzióját. Lásd: [az Azure CLI telepítése](/cli/azure/install-azure-cli) és [az Azure CLI használatának első lépései](/cli/azure/get-started-with-azure-cli).
* Megismerheti az ExpressRoute kapcsolatcsoport-kiépítési [munkafolyamatok](expressroute-workflows.md).
* Ellenőrizze, hogy az ExpressRoute-Kapcsolatcsoportok kiépített állapotban van.
* Ellenőrizze, hogy az ExpressRoute-Kapcsolatcsoportok az Azure privát társviszony-létesítés van konfigurálva.  

### <a name="log-into-your-azure-account"></a>Jelentkezzen be az Azure-fiókkal
A konfiguráció indításához az Azure-fiókjával be kell jelentkeznie. A parancs megnyitja a alapértelmezett böngészőt, és kérni az Azure-fiók bejelentkezési hitelesítő adatait.  

```azurecli
az login
```

Ha több Azure-előfizetéssel is rendelkezik, ellenőrizze a fiók előfizetéseit.

```azurecli
az account list
```

Válassza ki a használni kívánt előfizetést.

```azurecli
az account set --subscription <your subscription ID>
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>Az ExpressRoute-kapcsolatcsoportokat konfigurációs azonosítása
Engedélyezheti a globális elérhetőségű ExpressRoute bármely két ExpressRoute-Kapcsolatcsoportok között, amennyiben azok még található a támogatott országok, és másik társviszony-létesítési helyszínek során jönnek. Ha az előfizetés tulajdonosa mindkét Kapcsolatcsoportok vagy futtassa a konfigurációt az alábbi szakaszok a kapcsolatcsoport választhatja meg. Ha a két kapcsolatcsoporttal az Azure-előfizetések, egy Azure-előfizetésből engedélyezési kell, és a többi Azure-előfizetésben a konfigurációs parancs futtatásakor adja át a hitelesítési kulcsot.

## <a name="enable-connectivity-between-your-on-premises-networks"></a>A helyszíni hálózat közötti kapcsolat

Kapcsolat a következő parancs futtatásával, vegye figyelembe a következő értékeket:

* *társ-kapcsolatcsoport* kell lennie a teljes erőforrás-azonosítója. Példa: 

  ```
  /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}
  ```
* *-AddressPrefix* kell lennie egy/29 IPv4 alhálózati, például: "10.0.0.0/29". IP-címek használjuk ezt az alhálózatot a kapcsolatot a két ExpressRoute-Kapcsolatcsoportok között. Nem-címeket kell használnia az alhálózat az Azure Vnetekhez vagy a helyszíni hálózatokon.

Futtassa a következő CLI két ExpressRoute-kapcsolatcsoporttal csatlakozhat. A következő példa paranccsal:

```azurecli
az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29>
```

A CLI-kimenet az alábbihoz hasonlóan néz ki:

```azurecli
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

A fenti művelet befejeződése után kell kapcsolat segítségével a két ExpressRoute-Kapcsolatcsoportok mindkét oldalon az a helyszíni hálózat között.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>Az ExpressRoute-Kapcsolatcsoportok az Azure-előfizetések

Ha a két Kapcsolatcsoportok nem az Azure-előfizetéshez, engedélyt kell. A következő konfiguráció engedélyezése a kapcsolatcsoport 2 előfizetés létrehozása és a hitelesítési kulcs átadott kapcsolatcsoport 1.

Hitelesítési kulcs létrehozásához. 
```azurecli
az network express-route auth create --circuit-name <Circuit2Name> -g <Circuit2ResourceGroupName> -n <AuthorizationName>
```

A CLI-kimenet az alábbihoz hasonló.

```azurecli
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

Jegyezze fel a kapcsolatcsoport 2 erőforrás-azonosítóját, valamint a hitelesítési kulcsot.

Futtassa a következő parancsot a kapcsolatcsoport 1 ellen. Adja át a kapcsolatcsoport 2 erőforrás-azonosítóját és a hitelesítési kulcs 
```azurecli
az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29> --authorization-key <authorizationKey>
```

A fenti művelet befejeződése után kell kapcsolat segítségével a két ExpressRoute-Kapcsolatcsoportok mindkét oldalon az a helyszíni hálózat között.

## <a name="get-and-verify-the-configuration"></a>Első és a konfiguráció ellenőrzése

Használja a következő parancsot a kapcsolatcsoport a konfiguráció ellenőrzése, amelyen a konfigurációs történt, azaz 1 kapcsolatcsoport a fenti példában.

```azurecli
az network express-route show -n <CircuitName> -g <ResourceGroupName>
```

A parancssori felület kimeneti láthatja *CircuitConnectionStatus*. Azt jelzi, hogy a kapcsolat a két Kapcsolatcsoportok között létrejött, "Csatlakoztatva,"-e, illetve "Csatlakoztatva". 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>Tiltsa le a helyszíni hálózat közötti kapcsolat

Tiltsa le, futtassa a parancsokat a kapcsolatcsoport elleni, a konfiguráció készült, azaz 1 kapcsolatcsoport a fenti példában.

```azurecli
az network express-route peering connection delete -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName>
```

A Megjelenítés parancssori felület a állapotának ellenőrzése futtathatja. 

A fenti művelet befejezése után már nem elérhetőséget az ExpressRoute-Kapcsolatcsoportok keresztül a helyszíni hálózat között. 


## <a name="next-steps"></a>További lépések
* [További információ az ExpressRoute globális elérhetőségű](expressroute-global-reach.md)
* [Az ExpressRoute-kapcsolat ellenőrzése](expressroute-troubleshooting-expressroute-overview.md)
* [Azure virtuális hálózati kapcsolat ExpressRoute-kapcsolatcsoport](expressroute-howto-linkvnet-arm.md)


