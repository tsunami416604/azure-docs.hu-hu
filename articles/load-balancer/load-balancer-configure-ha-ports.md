---
title: Magas rendelkezésre állású portok konfigurálása az Azure Load Balancerhez
titleSuffix: Azure Load Balancer
description: Ismerje meg, hogyan használható a magas rendelkezésre állású portok terheléselosztási belső forgalmat az összes porton
services: load-balancer
documentationcenter: na
author: rdhillon
manager: narayan
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2018
ms.author: allensu
ms.openlocfilehash: c2e787a1f81d9f3d31b981c31a0249dd362b7bb9
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225408"
---
# <a name="configure-high-availability-ports-for-an-internal-load-balancer"></a>Magas rendelkezésre állású portok konfigurálása belső terheléselosztó

Ez a cikk magas rendelkezésre állású portok telepítését bemutató példát biztosít egy belső terheléselosztón. Konfigurációk hálózati virtuális berendezések (nva-k) vonatkozó további információkért tekintse meg a megfelelő szolgáltató webhelyeket.

>[!NOTE]
>Az Azure Load Balancer két különböző típust támogat: Alapszintű és Standard. Ez a cikk ismerteti a Standard Load Balancer. További információ az alapszintű Load Balancerről: [Load Balancer Overview (áttekintés](load-balancer-overview.md)).

Az ábrán látható az ebben a cikkben leírt üzembe helyezési példában a következő konfigurációt:

- Az nva-k egy mögött a magas rendelkezésre állású portok konfigurálása belső load balancer háttérkészlethez vannak üzembe helyezve. 
- A felhasználó által megadott útvonal (UDR) alkalmazza a DMZ alhálózat útvonalak minden forgalmat az nva-k azáltal, hogy a következő ugrás a belső terheléselosztói virtuális IP. 
- A belső terheléselosztó osztja el a forgalmat az egyik az aktív nva-k a load balancer algoritmus alapján.
- Az nva-t a forgalom feldolgozza, és továbbítja azokat a háttérbeli alhálózat az eredeti helyre.
- A visszatérési elérési út is igénybe vehet az azonos útvonal, ha egy megfelelő UDR van konfigurálva, a háttérbeli alhálózat. 

![Magas rendelkezésre állású portok a központi telepítési példa](./media/load-balancer-configure-ha-ports/haports.png)

## <a name="configure-high-availability-ports"></a>Magas rendelkezésre állású portok konfigurálása

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Magas rendelkezésre állású portok konfigurálása, állítsa be az nva-k a belső terheléselosztó a háttér-készletben. Állítsa be a megfelelő terheléselosztó egészségügyi mintavétel konfigurálásához NVA állapotát és a terheléselosztó-szabályt magas rendelkezésre állású portok az észleléséhez. Az általános terheléselosztással kapcsolatos konfiguráció az első [lépések](load-balancer-get-started-ilb-arm-portal.md)során szerepel. Ez a cikk a magas rendelkezésre állású portok konfigurációs emeli ki.

A konfiguráció lényegében azt jelenti, hogy az előtér-portot és a háttér port értékét **0-ra**állítja. Állítsa a protokoll értékét az **összes**értékre. Ez a cikk ismerteti, hogyan lehet magas rendelkezésre állású portok konfigurálása az Azure portal, PowerShell és az Azure CLI használatával.

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-the-azure-portal"></a>Magas rendelkezésre állású portok terheléselosztási szabály konfigurálása az Azure portal használatával

Ha a magas rendelkezésre állású portokat a Azure Portal használatával szeretné konfigurálni, jelölje be a **Ha portok** jelölőnégyzetet. Kiválasztásakor a rendszer automatikusan kitölti a port és protokoll konfigurációs. 

![Magas rendelkezésre állású portok konfigurálása az Azure Portalon](./media/load-balancer-configure-ha-ports/haports-portal.png)

### <a name="configure-a-high-availability-ports-load-balancing-rule-via-the-resource-manager-template"></a>A Resource Manager-sablon használatával magas rendelkezésre állású portok terheléselosztási szabály konfigurálása

Konfigurálhatja magas rendelkezésre állású portok a 2017-08-01-es API-verzió használatával Microsoft.Network/loadBalancers a terheléselosztó erőforrás. A következő JSON-kódrészletben a terheléselosztói konfigurációban magas rendelkezésre állású portok a REST API-n keresztül változásokat mutatja be:

```json
    {
        "apiVersion": "2017-08-01",
        "type": "Microsoft.Network/loadBalancers",
        ...
        "sku":
        {
            "name": "Standard"
        },
        ...
        "properties": {
            "frontendIpConfigurations": [...],
            "backendAddressPools": [...],
            "probes": [...],
            "loadBalancingRules": [
             {
                "properties": {
                    ...
                    "protocol": "All",
                    "frontendPort": 0,
                    "backendPort": 0
                }
             }
            ],
       ...
       }
    }
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-powershell"></a>Magas rendelkezésre állású portok terheléselosztási szabály konfigurálása a PowerShell-lel

A következő paranccsal hozzon létre a magas rendelkezésre állású portok terheléselosztási szabályt, amíg a belső terheléselosztó létrehozása a PowerShell használatával:

```powershell
lbrule = New-AzLoadBalancerRuleConfig -Name "HAPortsRule" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "All" -FrontendPort 0 -BackendPort 0
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-azure-cli"></a>Magas rendelkezésre állású portok terheléselosztási szabály konfigurálása az Azure CLI-vel

A [belső terheléselosztó létrehozásának](load-balancer-get-started-ilb-arm-cli.md)4. lépésében használja a következő parancsot a magas rendelkezésre állású portok terheléselosztó szabályának létrehozásához:

```azurecli
azure network lb rule create --resource-group contoso-rg --lb-name contoso-ilb --name haportsrule --protocol all --frontend-port 0 --backend-port 0 --frontend-ip-name feilb --backend-address-pool-name beilb
```

## <a name="next-steps"></a>Következő lépések

További információ a [magas rendelkezésre állású portokról](load-balancer-ha-ports-overview.md).