---
title: "Magas rendelkezésre állás portok konfigurálása az Azure terheléselosztó |} Microsoft Docs"
description: "Az összes porton belső forgalom terheléselosztási magas rendelkezésre állás portok használata"
services: load-balancer
documentationcenter: na
author: rdhillon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/02/2017
ms.author: kumud
ms.openlocfilehash: 36bc3d7a35f41384706cbc7101457d00848639b2
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="configure-high-availability-ports-for-an-internal-load-balancer"></a>Magas rendelkezésre állás portok konfigurálása a belső terheléselosztók

Ez a cikk a magas rendelkezésre állás portok telepítését bemutató példát Ez a belső terheléselosztót. Konfigurációk (NVAs) virtuális készülékekre vonatkozó további információkért tekintse meg a megfelelő szolgáltató webhelyeket.

>[!NOTE]
> A magas rendelkezésre állás portok funkció jelenleg előzetes verzió. Az előzetes változatban a szolgáltatás esetleg nincs azonos szintű rendelkezésre állást és megbízhatóságot, szolgáltatások, amelyek általában a rendelkezésre állási kiadási. További részletekért lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az ábrán a központi telepítési példa a cikkben a következő konfigurációt:

- A NVAs a háttér-készlet egy belső terheléselosztó mögött a magas rendelkezésre állás portok vannak telepítve. 
- A felhasználó által megadott útvonal (UDR) alkalmazza a DMZ alhálózati útvonalak az összes forgalom a NVAs azáltal, hogy a következő ugrás a belső terheléselosztó virtuális IP-cím betölteni. 
- A belső terheléselosztó osztja el a forgalmat a load balancer algoritmus alapján aktív NVAs egyikére.
- Az NVA dolgozza fel a forgalmat, majd továbbítja azt az eredeti cél a háttér-alhálózat.
- A visszaadott elérési út esetén megfelelő UDR van konfigurálva, a háttér-alhálózaton is igénybe vehet a azonos útvonal. 

![Például telepítése magas rendelkezésre állás portok](./media/load-balancer-configure-ha-ports/haports.png)


## <a name="preview-sign-up"></a>Előnézet-előfizetés

Az előzetes szabványos Azure Load Balancer magas rendelkezésre állás portok szolgáltatásának részt, regisztrálja az előfizetés hozzáférést nyerni Azure CLI 2.0 vagy a PowerShell használatával. Az előfizetés regisztrálása a [szokásos terhelés terheléselosztó előzetes](https://aka.ms/lbpreview#preview-sign-up).

>[!NOTE]
>Szabványos Load Balancer előzetes regisztráció egy óráig is eltarthat.

## <a name="configure-high-availability-ports"></a>Magas rendelkezésre állás portok konfigurálása

Magas rendelkezésre állás portok konfigurálása, állítsa be a NVAs a belső terheléselosztót a háttér-készletben. Egy megfelelő terheléselosztó állapotfigyelő mintavétel-konfiguráció beállítása NVA állapotát és a magas rendelkezésre állás portokkal terheléselosztó szabályhoz. Az általános terhelésétől konfigurációs tárgyalja [Ismerkedés](load-balancer-get-started-ilb-arm-portal.md). Ez a cikk a magas rendelkezésre állás portok konfigurációját mutatja be.

A konfigurációs igényli az előtér-port és a háttér-port érték beállítása **0**. Adja meg a protokoll értéket **összes**. Ez a cikk ismerteti a magas rendelkezésre állás portok konfigurálása az Azure portál, a PowerShell és az Azure CLI 2.0 használatával.

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-the-azure-portal"></a>Egy magas rendelkezésre állás portok terheléselosztási szabály konfigurálása az Azure-portálon

Magas rendelkezésre állás portok konfigurálása az Azure-portál használatával, jelölje ki a **magas rendelkezésre ÁLLÁSÚ portok** jelölőnégyzetet. Ha ki van választva, a port és protokoll konfigurációs automatikusan feltöltődik értékkel. 

![Magas rendelkezésre állás portok konfigurálása az Azure-portálon](./media/load-balancer-configure-ha-ports/haports-portal.png)


### <a name="configure-a-high-availability-ports-load-balancing-rule-via-the-resource-manager-template"></a>A Resource Manager-sablon segítségével magas rendelkezésre állás portok terheléselosztási szabály konfigurálása

Magas rendelkezésre állás portok konfigurálhatja 2017-08-01 API-verzió Microsoft.Network/loadBalancers használ a Load Balancer erőforrás. A következő JSON-részlet a terheléselosztó-konfiguráció a REST API-n keresztül magas rendelkezésre állás portok változásait mutatja be:

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

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-powershell"></a>A PowerShell segítségével egy magas rendelkezésre állás portok terheléselosztási szabály konfigurálása

A következő paranccsal a magas rendelkezésre állás portok terheléselosztási szabály létrehozásához, amíg a belső terheléselosztó létrehozása a PowerShell használatával:

```powershell
lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HAPortsRule" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "All" -FrontendPort 0 -BackendPort 0
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-azure-cli-20"></a>Azure CLI 2.0 egy magas rendelkezésre állás portok terheléselosztási szabály konfigurálása

4. lépésben [hozzon létre egy belső terheléselosztó](load-balancer-get-started-ilb-arm-cli.md), a magas rendelkezésre állás portok terheléselosztási szabály létrehozásához használja a következő parancsot:

```azurecli
azure network lb rule create --resource-group contoso-rg --lb-name contoso-ilb --name haportsrule --protocol all --frontend-port 0 --backend-port 0 --frontend-ip-name feilb --backend-address-pool-name beilb
```

## <a name="next-steps"></a>Következő lépések

További információ [magas rendelkezésre állás portok](load-balancer-ha-ports-overview.md).
