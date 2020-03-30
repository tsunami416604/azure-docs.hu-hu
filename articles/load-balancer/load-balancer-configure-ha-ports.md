---
title: Magas rendelkezésre állású portok konfigurálása az Azure Load Balancer számára
titleSuffix: Azure Load Balancer
description: További információ a magas rendelkezésre állású portok használatáról a terheléselosztás belső forgalmához az összes porton
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
ms.openlocfilehash: c6b8ecb443408f23ae604bd9c8139cb0a2afcd12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477782"
---
# <a name="configure-high-availability-ports-for-an-internal-load-balancer"></a>Magas rendelkezésre állású portok konfigurálása belső terheléselosztóhoz

Ez a cikk egy példa a magas rendelkezésre állású portok egy belső terheléselosztó. A hálózati virtuális készülékek (NVA-k) konfigurációival kapcsolatos további információkért tekintse meg a megfelelő szolgáltatówebhelyeket.

>[!NOTE]
>Az Azure Load Balancer két különböző típust támogat: Alapszintű és Standard. Ez a cikk a Standard Load Balancer-t ismerteti. Az alapszintű terheléselosztóról a [Terheléselosztó áttekintéscímű témakörben olvashat bővebben.](load-balancer-overview.md)

Az ábrán a jelen cikkben ismertetett telepítési példa következő konfigurációja látható:

- Az NVA-k a magas rendelkezésre állású portok konfigurációja mögötti belső terheléselosztó háttérkészletében vannak telepítve. 
- A DMZ alhálózaton alkalmazott felhasználó által definiált útvonal (UDR) az összes forgalmat a nva-khoz irányítja azáltal, hogy a következő ugrást a belső terheléselosztó virtuális IP-jeként teszi. 
- A belső terheléselosztó a terheléselosztó algoritmusszerint osztja el a forgalmat az egyik aktív NVA-nak.
- Az NVA feldolgozza a forgalmat, és továbbítja azt az eredeti célaa a háttér-alhálózatban.
- A visszatérési útvonal ugyanazt az útvonalat is megteheti, ha a megfelelő UDR van konfigurálva a háttér-alhálózatban. 

![Magas rendelkezésre állású portok példa telepítésre](./media/load-balancer-configure-ha-ports/haports.png)

## <a name="configure-high-availability-ports"></a>Magas rendelkezésre állású portok konfigurálása

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A magas rendelkezésre állású portok konfigurálásához állítson be egy belső terheléselosztót a háttérkészletben lévő nva-kkal. Állítsa be a megfelelő terheléselosztó állapotminta konfigurációjának észlelésére NVA állapotát és a terheléselosztó szabály magas rendelkezésre állású portok. Az általános terheléselosztóval kapcsolatos konfigurációt az [Első lépések](load-balancer-get-started-ilb-arm-portal.md)című segédprogram lefedi. Ez a cikk kiemeli a magas rendelkezésre állású portok konfigurációját.

A konfiguráció lényegében magában foglalja az előtér-port és a háttér-port **érték0-re**való beállítását. Állítsa a protokoll értékét **Az Összes**értékre. Ez a cikk ismerteti, hogyan konfigurálhatja a magas rendelkezésre állású portok az Azure Portalon, a PowerShell és az Azure CLI használatával.

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-the-azure-portal"></a>Magas rendelkezésre állású portok terheléselosztószabályának konfigurálása az Azure Portalon

Ha az Azure Portal használatával szeretné konfigurálni a magas rendelkezésre állású portokat, jelölje be a **HA-portok** jelölőnégyzetet. Ha be van jelölve, a kapcsolódó port- és protokollkonfiguráció automatikusan kitöltődik. 

![Magas rendelkezésre állású portok konfigurációja az Azure portalon keresztül](./media/load-balancer-configure-ha-ports/haports-portal.png)

### <a name="configure-a-high-availability-ports-load-balancing-rule-via-the-resource-manager-template"></a>Magas rendelkezésre állású portok terheléselosztási szabályának konfigurálása az Erőforrás-kezelő sablonon keresztül

A magas rendelkezésre állású portokat a 2017-08-01 API-verzió segítségével konfigurálhatja a Microsoft.Network/loadBalancers számára a terheléselosztó erőforrásban. A következő JSON-kódrészlet a MAGAS rendelkezésre állású portok terheléselosztó-konfigurációjának változásait mutatja be a REST API-n keresztül:

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

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-powershell"></a>Magas rendelkezésre állású portok terheléselosztószabályának konfigurálása a PowerShell használatával

A következő paranccsal hozza létre a magas rendelkezésre állású portok terheléselosztó szabály létrehozása közben a belső terheléselosztó a PowerShell:

```powershell
lbrule = New-AzLoadBalancerRuleConfig -Name "HAPortsRule" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "All" -FrontendPort 0 -BackendPort 0
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-azure-cli"></a>Magas rendelkezésre állású portok terheléselosztószabályának konfigurálása az Azure CLI-vel

A Belső [terheléselosztó készlet létrehozása](load-balancer-get-started-ilb-arm-cli.md)4.

```azurecli
az network lb rule create --resource-group contoso-rg --lb-name contoso-ilb --name haportsrule --protocol all --frontend-port 0 --backend-port 0 --frontend-ip-name feilb --backend-address-pool-name beilb
```

## <a name="next-steps"></a>További lépések

További információ a [magas rendelkezésre állású portokról.](load-balancer-ha-ports-overview.md)