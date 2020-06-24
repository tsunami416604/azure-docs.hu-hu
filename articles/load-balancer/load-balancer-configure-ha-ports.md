---
title: Magas rendelkezésre állású portok konfigurálása Azure Load Balancerhoz
titleSuffix: Azure Load Balancer
description: Ismerje meg, hogyan használhatók magas rendelkezésre állású portok a belső forgalom terheléselosztásához az összes porton
services: load-balancer
documentationcenter: na
author: rdhillon
manager: narayan
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2018
ms.author: allensu
ms.openlocfilehash: a71a01629f334534d8eb26847a8f3400efbbeafe
ms.sourcegitcommit: ad66392df535c370ba22d36a71e1bbc8b0eedbe3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/16/2020
ms.locfileid: "84807826"
---
# <a name="configure-high-availability-ports-for-an-internal-load-balancer"></a>Magas rendelkezésre állású portok konfigurálása belső terheléselosztó számára

Ez a cikk példát mutat be a magas rendelkezésre állású portok belső terheléselosztó általi üzembe helyezésére. A hálózati virtuális berendezésekre (NVA) jellemző konfigurációkról a megfelelő szolgáltatói webhelyeken talál további információt.

>[!NOTE]
>Az Azure Load Balancer két különböző típust támogat: Alapszintű és Standard. Ez a cikk standard Load Balancer ismerteti. További információ az alapszintű Load Balancerről: [Load Balancer Overview (áttekintés](load-balancer-overview.md)).

Az ábrán a jelen cikkben ismertetett telepítési példa következő konfigurációja látható:

- A NVA a magas rendelkezésre állású portok konfigurálása mögötti belső terheléselosztó háttér-készletében helyezik üzembe. 
- A DMZ alhálózaton alkalmazott, felhasználó által megadott útvonal (UDR) az NVA-ra irányítja az összes forgalmat, ha a következő ugrást a belső terheléselosztó virtuális IP-címévé teszi. 
- A belső terheléselosztó a terheléselosztó algoritmusa alapján osztja el a forgalmat az egyik aktív NVA.
- A NVA feldolgozza a forgalmat, és továbbítja azt az eredeti célhelyre a háttérbeli alhálózatban.
- Ha egy megfelelő UDR van konfigurálva a háttérbeli alhálózatban, a visszatérési útvonal is elvégezhető. 

![Magas rendelkezésre állású portok – példa központi telepítésre](./media/load-balancer-configure-ha-ports/haports.png)

## <a name="configure-high-availability-ports"></a>Magas rendelkezésre állású portok konfigurálása

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A magas rendelkezésre állású portok konfigurálásához állítson be egy belső terheléselosztó NVA a háttér-készletben. Állítson be egy megfelelő terheléselosztó állapot-mintavételi konfigurációt a NVA állapot és a terheléselosztó-szabály magas rendelkezésre állású portokkal való észleléséhez. Az általános terheléselosztással kapcsolatos konfiguráció az első [lépések](load-balancer-get-started-ilb-arm-portal.md)során szerepel. Ez a cikk a magas rendelkezésre állású portok konfigurációját emeli ki.

A konfiguráció lényegében azt jelenti, hogy az előtér-portot és a háttér port értékét **0-ra**állítja. Állítsa a protokoll értékét az **összes**értékre. Ez a cikk a magas rendelkezésre állású portok konfigurálását ismerteti a Azure Portal, a PowerShell és az Azure CLI használatával.

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-the-azure-portal"></a>Magas rendelkezésre állású portok terheléselosztó-szabályának konfigurálása a Azure Portal

Ha a magas rendelkezésre állású portokat a Azure Portal használatával szeretné konfigurálni, jelölje be a **Ha portok** jelölőnégyzetet. Ha be van jelölve, a rendszer automatikusan kitölti a kapcsolódó portot és a protokoll konfigurációját. 

![Magas rendelkezésre állású portok konfigurálása a Azure Portal használatával](./media/load-balancer-configure-ha-ports/haports-portal.png)

### <a name="configure-a-high-availability-ports-load-balancing-rule-via-the-resource-manager-template"></a>Magas rendelkezésre állású portok terheléselosztási szabályának konfigurálása a Resource Manager-sablon használatával

A magas rendelkezésre állású portokat a Load Balancer erőforrásban a Microsoft. Network/loadBalancers 2017-08-01 API-verziójának használatával állíthatja be. A következő JSON-kódrészlet a magas rendelkezésre állású portok terheléselosztó-konfigurációjának változásait mutatja be a REST APIon keresztül:

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

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-powershell"></a>Magas rendelkezésre állású portok terheléselosztó-szabályának konfigurálása a PowerShell-lel

A következő parancs használatával hozza létre a magas rendelkezésre állású portok terheléselosztó-szabályát a belső terheléselosztó PowerShell-lel való létrehozásakor:

```powershell
lbrule = New-AzLoadBalancerRuleConfig -Name "HAPortsRule" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "All" -FrontendPort 0 -BackendPort 0
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-azure-cli"></a>Magas rendelkezésre állású portok terheléselosztó-szabályának konfigurálása az Azure CLI-vel

A [belső terheléselosztó létrehozásának](load-balancer-get-started-ilb-arm-cli.md)4. lépésében használja a következő parancsot a magas rendelkezésre állású portok terheléselosztó szabályának létrehozásához:

```azurecli
az network lb rule create --resource-group contoso-rg --lb-name contoso-ilb --name haportsrule --protocol all --frontend-port 0 --backend-port 0 --frontend-ip-name feilb --backend-address-pool-name beilb
```

## <a name="next-steps"></a>További lépések

További információ a [magas rendelkezésre állású portokról](load-balancer-ha-ports-overview.md).