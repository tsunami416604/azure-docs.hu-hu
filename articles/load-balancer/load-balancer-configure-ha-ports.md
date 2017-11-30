---
title: "Magas rendelkezésre állás portok konfigurálása az Azure terheléselosztó |} Microsoft Docs"
description: "Az összes porton belső forgalom terheléselosztási magas rendelkezésre állású portok használata"
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
ms.openlocfilehash: 646ade828e96810bdc3b07d4dc5c0276a1621969
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2017
---
# <a name="how-to-configure-high-availability-ports-for-internal-load-balancer"></a>Belső terheléselosztó magas rendelkezésre állású portok konfigurálása

Ez a cikk magas rendelkezésre ÁLLÁS telepítését bemutató példát-portokat biztosít egy belső terheléselosztón. Hálózati virtuális készülékek (NVAs) specifikus konfigurációk tekintse meg a megfelelő szolgáltató webhelyeket.

>[!NOTE]
> Magas rendelkezésre állás portok funkció jelenleg előzetes verzió. Az előzetes kiadás során a szolgáltatás rendelkezésre állása és megbízhatósága eltérő lehet az általánosan elérhető kiadásétól. További részletekért lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

1. ábra azt mutatja be, a jelen cikkben ismertetett telepítési példában a következő konfigurációt:
- A NVAs egy belső terheléselosztó mögött a magas rendelkezésre ÁLLÁSÚ portok konfigurációs háttérkészlethez vannak telepítve. 
- A UDR alkalmazza a DMZ alhálózati útvonalak az összes forgalom a NVAs azáltal, hogy a belső terheléselosztási terheléselosztó virtuális IP-cím, a következő ugrás. 
- Belső terheléselosztó osztja el a forgalmat a Terheléselosztó algoritmus alapján aktív NVAs egyikét.
- NVA dolgozza fel a forgalmat, és továbbítja a backend alhálózathoz az eredeti célra.
- A visszaadott elérési út is esetén is igénybe vehet a azonos útvonal a megfelelő UDR konfigurálva van a backend alhálózathoz. 

![magas rendelkezésre állású portok a központi telepítési példa](./media/load-balancer-configure-ha-ports/haports.png)

1. ábra - hálózat virtuális készülékek a magas rendelkezésre állású portokkal egy belső terheléselosztó mögé telepítették 

## <a name="preview-sign-up"></a>Előnézet-előfizetés

Az előzetes betöltési terheléselosztó szabványos magas rendelkezésre ÁLLÁSÚ portok szolgáltatásának részt, regisztrálja az előfizetés hozzáférést Azure CLI 2.0 vagy a PowerShell használatával. Az előfizetés regisztrálása a [Load Balancer szabványos előzetes](https://aka.ms/lbpreview#preview-sign-up).

>[!NOTE]
>A Load Balancer szabványos előzetes regisztrációja egy óráig is eltarthat.

## <a name="configuring-ha-ports"></a>Magas rendelkezésre ÁLLÁSÚ portok konfigurálása

A magas rendelkezésre ÁLLÁSÚ portok konfigurációja magában foglalja a egy megfelelő terheléselosztó állapotfigyelő mintavétel-konfiguráció észleléséhez NVA állapotát, és a magas rendelkezésre ÁLLÁSÚ porttal rendelkező terheléselosztó szabály egy belső terheléselosztó, a háttérkészletben NVAs a beállítása. Az általános terheléselosztóhoz kapcsolódó konfigurációs tárgyalja [Ismerkedés](load-balancer-get-started-ilb-arm-portal.md). Ez a cikk a magas rendelkezésre ÁLLÁSÚ portok konfigurációját mutatja be.

A konfigurációs igényli érték az elülső rétegbeli portot és a háttérkiszolgáló port **0**, és a protokoll értéket **összes**. Ez a cikk ismerteti az Azure-portálon, a PowerShell és az Azure CLI 2.0 használatával magas rendelkezésre állású portok konfigurálása.

### <a name="configure-ha-ports-load-balancer-rule-with-the-azure-portal"></a>Magas rendelkezésre ÁLLÁSÚ portok terheléselosztási szabály konfigurálása az Azure-portálon

Az Azure portálon a **magas rendelkezésre ÁLLÁSÚ portok** beállítást ehhez a konfigurációhoz jelölőnégyzetet keresztül. Ha ki van választva, a port és protokoll konfigurációs automatikusan feltöltődik értékkel. 

![magas rendelkezésre állású portok konfigurálása Azure-portálon](./media/load-balancer-configure-ha-ports/haports-portal.png)

2. ábra – magas rendelkezésre ÁLLÁSÚ portok konfigurációs portálon

### <a name="configure-ha-ports-lb-rule-via-resource-manager-template"></a>Magas rendelkezésre ÁLLÁSÚ portok LB szabály keresztül Resource Manager-sablon konfigurálása

Konfigurálhatja a magas rendelkezésre ÁLLÁSÚ portokat 2017-08-01 API-verzió Microsoft.Network/loadBalancers használ a Load Balancer erőforrás. A következő JSON-részlet a módosításokat a terheléselosztó-konfigurációban a magas rendelkezésre ÁLLÁSÚ portokhoz REST API-n keresztül mutatja be.

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

### <a name="configure-ha-ports-load-balancer-rule-with-powershell"></a>A PowerShell segítségével magas rendelkezésre ÁLLÁSÚ portok terheléselosztási szabály konfigurálása

A következő parancs segítségével a magas rendelkezésre ÁLLÁSÚ portok terheléselosztó szabály létrehozása a PowerShell használatával a belső terheléselosztó létrehozása közben:

```powershell
lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HAPortsRule" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "All" -FrontendPort 0 -BackendPort 0
```

### <a name="configure-ha-ports-load-balancer-rule-with-azure-cli-20"></a>Azure CLI 2.0 magas rendelkezésre ÁLLÁSÚ portok terheléselosztási szabály konfigurálása

A(z) # 4. lépést [létrehozása egy belső Load Balancer](load-balancer-get-started-ilb-arm-cli.md), a magas rendelkezésre ÁLLÁSÚ portok terheléselosztó szabály létrehozása az alábbi parancs segítségével.

```azurecli
azure network lb rule create --resource-group contoso-rg --lb-name contoso-ilb --name haportsrule --protocol all --frontend-port 0 --backend-port 0 --frontend-ip-name feilb --backend-address-pool-name beilb
```

## <a name="next-steps"></a>Következő lépések

- További információ [magas rendelkezésre állású portok](load-balancer-ha-ports-overview.md)
