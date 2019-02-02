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
ms.openlocfilehash: be10489d731b9e01d148ce1ac7892cb6de956662
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2019
ms.locfileid: "55659320"
---
# <a name="configure-expressroute-global-reach-by-using-the-azure-cli-preview"></a>Globális elérhetőségű ExpressRoute konfigurálása az Azure CLI (előzetes verzió) használatával

Ez a cikk segít az Azure ExpressRoute globális elérhetőségű konfigurálása az Azure CLI használatával. További információ: [ExpressRoute Global Reach](expressroute-global-reach.md).
 
## <a name="before-you-begin"></a>Előkészületek

> [!IMPORTANT]
> A nyilvános előzetes verzióra nem vonatkozik szolgáltatói szerződés, és nem használható éles számítási feladatokra. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Mielőtt elkezdené a konfigurációt, hajtsa végre az alábbi követelményeknek:

* Telepítse az Azure CLI legújabb verzióját. További információk: [Az Azure CLI telepítése](/cli/azure/install-azure-cli) és [Bevezetés az Azure CLI használatába](/cli/azure/get-started-with-azure-cli).
* Megismerheti az ExpressRoute kapcsolatcsoport-kiépítési [munkafolyamatok](expressroute-workflows.md).
* Ellenőrizze, hogy az ExpressRoute-Kapcsolatcsoportok kiépített állapotban van.
* Ellenőrizze, hogy az ExpressRoute-Kapcsolatcsoportok az Azure privát társviszony-létesítés van konfigurálva.  

### <a name="sign-in-to-your-azure-account"></a>Jelentkezzen be az Azure-fiókjába

Konfigurációs megkezdéséhez jelentkezzen be az Azure-fiókjával. A következő parancsot az alapértelmezett böngészőben nyílik meg, és az Azure-fiók bejelentkezési hitelesítő adatokat kér:  

```azurecli
az login
```

Ha több Azure-előfizetéssel rendelkezik, tekintse meg a fiókhoz tartozó előfizetések:

```azurecli
az account list
```

Válassza ki a használni kívánt előfizetést:

```azurecli
az account set --subscription <your subscription ID>
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>Az ExpressRoute-kapcsolatcsoportokat konfigurációs azonosítása

Engedélyezheti a globális elérhetőségű ExpressRoute bármely két ExpressRoute-Kapcsolatcsoportok között mindaddig, amíg Ön található támogatott országok és a létrehozásuk másik társviszony-létesítési helyszínek. Ha az előfizetés tulajdonosa mindkét Kapcsolatcsoportok, vagy kapcsolatcsoport futtassa a konfigurációt, a cikk későbbi részében leírtak választhatja meg. Ha a két kapcsolatcsoporttal az Azure-előfizetések, rendelkeznie kell egy Azure-előfizetésből engedélyezési, és át kell az engedélyezési kulcsot a többi Azure-előfizetésben a konfigurációs parancs futtatásakor.

## <a name="enable-connectivity-between-your-on-premises-networks"></a>A helyszíni hálózat közötti kapcsolat

Amikor a kapcsolat a következő parancs futtatásával, vegye figyelembe a paraméter értékét az alábbi követelményeket:

* *társ-kapcsolatcsoport* kell lennie a teljes erőforrás-azonosítója. Példa:

  > /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}

* *címelőtag* kell lennie egy "/ 29" IPv4 alhálózati (például "10.0.0.0/29"). IP-cím az alhálózat, a két ExpressRoute-Kapcsolatcsoportok közötti kapcsolatot létesíteni használjuk. Nem-címeket kell használnia az alhálózat az Azure virtuális hálózataihoz vagy a helyszíni hálózatokon.

Futtassa a következő CLI-parancsot két ExpressRoute-kapcsolatcsoporttal csatlakozhat:

```azurecli
az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29>
```

A CLI-kimenet a következőhöz hasonló:

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

Ez a művelet befejeződése után rendelkezni fog kapcsolat mindkét oldalán keresztül a két ExpressRoute-Kapcsolatcsoportok az a helyszíni hálózat között.

## <a name="enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions"></a>Az ExpressRoute-Kapcsolatcsoportok az Azure-előfizetések közötti kapcsolat

Ha a két Kapcsolatcsoportok nem szerepelnek az Azure-előfizetéshez, engedélyezési kell. A következő konfigurációt létrehozni az engedélyezési kapcsolatcsoport 2 előfizetés, és át kell adnia az engedélyezési kulcsot a kapcsolatcsoport az 1.

1. Hitelesítési kulcs létrehozásához:

   ```azurecli
   az network express-route auth create --circuit-name <Circuit2Name> -g <Circuit2ResourceGroupName> -n <AuthorizationName>
   ```

   A CLI-kimenet a következőhöz hasonló:

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

1. Jegyezze fel az erőforrás-azonosítója és a hitelesítési kulcs kapcsolatcsoport 2.

1. Futtassa a következő parancsot a kapcsolatcsoport 1, 2 a kapcsolatcsoport erőforrás azonosítója és a hitelesítési kulcs átadásával szemben:

   ```azurecli
   az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29> --authorization-key <authorizationKey>
   ```

Ez a művelet befejeződése után rendelkezni fog kapcsolat mindkét oldalán keresztül a két ExpressRoute-Kapcsolatcsoportok az a helyszíni hálózat között.

## <a name="get-and-verify-the-configuration"></a>Első és a konfiguráció ellenőrzése

Használja a következő parancsot a kapcsolatcsoport a konfiguráció ellenőrzése, amelyen a konfigurációs történt (1 a kapcsolatcsoport az előző példában):

```azurecli
az network express-route show -n <CircuitName> -g <ResourceGroupName>
```

A CLI kimenetben láthatja *CircuitConnectionStatus*. Megadja, hogy hogy van-e a kapcsolat a két Kapcsolatcsoportok között létrehozott ("Connected"), vagy nem meghatározott ("leválasztott"). 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>Tiltsa le a helyszíni hálózat közötti kapcsolat

Tiltsa le a kapcsolati, a következő parancs futtatásával ellen a kapcsolatcsoportot, ahol a konfiguráció értéke arról (1 a kapcsolatcsoport az előző példában).

```azurecli
az network express-route peering connection delete -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName>
```

Használja a ```show``` paranccsal ellenőrizheti az állapotot.

Ez a művelet befejeződése után már nem lesz kapcsolat az ExpressRoute-Kapcsolatcsoportok keresztül a helyszíni hálózat között.

## <a name="next-steps"></a>További lépések

* [További információ az ExpressRoute globális elérhetőségű](expressroute-global-reach.md)
* [Az ExpressRoute-kapcsolat ellenőrzése](expressroute-troubleshooting-expressroute-overview.md)
* [Virtuális hálózat ExpressRoute-kapcsolatcsoport összekapcsolása](expressroute-howto-linkvnet-arm.md)
