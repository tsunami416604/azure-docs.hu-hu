---
title: Magas rendelkezésre állású portok konfigurálása az Azure Load Balancer |} A Microsoft Docs
description: Ismerje meg, hogyan használható a magas rendelkezésre állású portok terheléselosztási belső forgalmat az összes porton
services: load-balancer
documentationcenter: na
author: rdhillon
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2018
ms.author: kumud
ms.openlocfilehash: 8e1b94dbdef66c6423d16ffcbc9df4cd60a13c69
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50415542"
---
# <a name="configure-high-availability-ports-for-an-internal-load-balancer"></a>Magas rendelkezésre állású portok konfigurálása belső terheléselosztó

Ez a cikk magas rendelkezésre állású portok telepítését bemutató példát biztosít egy belső terheléselosztón. Konfigurációk hálózati virtuális berendezések (nva-k) vonatkozó további információkért tekintse meg a megfelelő szolgáltató webhelyeket.

>[!NOTE]
>Az Azure Load Balancer két különböző típust támogat: Alapszintű és Standard. Ez a cikk ismerteti a Standard Load Balancer. Alapszintű Load Balancer kapcsolatos további információkért lásd: [Load Balancer áttekintése](load-balancer-overview.md).

Az ábrán látható az ebben a cikkben leírt üzembe helyezési példában a következő konfigurációt:

- Az nva-k egy mögött a magas rendelkezésre állású portok konfigurálása belső load balancer háttérkészlethez vannak üzembe helyezve. 
- A felhasználó által megadott útvonal (UDR) alkalmazza a DMZ alhálózat útvonalak minden forgalmat az nva-k azáltal, hogy a következő ugrás a belső terheléselosztói virtuális IP. 
- A belső terheléselosztó osztja el a forgalmat az egyik az aktív nva-k a load balancer algoritmus alapján.
- Az nva-t a forgalom feldolgozza, és továbbítja azokat a háttérbeli alhálózat az eredeti helyre.
- A visszatérési elérési út is igénybe vehet az azonos útvonal, ha egy megfelelő UDR van konfigurálva, a háttérbeli alhálózat. 

![Magas rendelkezésre állású portok a központi telepítési példa](./media/load-balancer-configure-ha-ports/haports.png)



## <a name="configure-high-availability-ports"></a>Magas rendelkezésre állású portok konfigurálása

Magas rendelkezésre állású portok konfigurálása, állítsa be az nva-k a belső terheléselosztó a háttér-készletben. Állítsa be a megfelelő terheléselosztó egészségügyi mintavétel konfigurálásához NVA állapotát és a terheléselosztó-szabályt magas rendelkezésre állású portok az észleléséhez. Az általános terhelésétől konfigurációs foglalkozik [Ismerkedés](load-balancer-get-started-ilb-arm-portal.md). Ez a cikk a magas rendelkezésre állású portok konfigurációs emeli ki.

A konfigurációt igényli az előtérbeli portot és a háttér-port értékének beállítása **0**. A protokoll értékét állítsa **összes**. Ez a cikk ismerteti, hogyan lehet magas rendelkezésre állású portok konfigurálása az Azure portal, PowerShell és az Azure CLI használatával.

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-the-azure-portal"></a>Magas rendelkezésre állású portok terheléselosztási szabály konfigurálása az Azure portal használatával

Magas rendelkezésre állású portok konfigurálása az Azure portal használatával, válassza ki a **magas rendelkezésre ÁLLÁSÚ portok** jelölőnégyzetet. Kiválasztásakor a rendszer automatikusan kitölti a port és protokoll konfigurációs. 

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
lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HAPortsRule" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "All" -FrontendPort 0 -BackendPort 0
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-azure-cli"></a>Magas rendelkezésre állású portok terheléselosztási szabály konfigurálása az Azure CLI-vel

A 4. lépésben [hozzon létre egy belső terheléselosztó készletet](load-balancer-get-started-ilb-arm-cli.md), a magas rendelkezésre állású portok terheléselosztási szabály létrehozása a következő paranccsal:

```azurecli
azure network lb rule create --resource-group contoso-rg --lb-name contoso-ilb --name haportsrule --protocol all --frontend-port 0 --backend-port 0 --frontend-ip-name feilb --backend-address-pool-name beilb
```

## <a name="next-steps"></a>További lépések

Tudjon meg többet [magas rendelkezésre állású portok](load-balancer-ha-ports-overview.md).
