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
ms.date: 09/26/2017
ms.author: kumud
ms.openlocfilehash: 7256548b988812c64ca9a9f8a84fec377646635d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-high-availability-ports-for-internal-load-balancer"></a>Belső terheléselosztó magas rendelkezésre állású portok konfigurálása

Ez a cikk magas rendelkezésre ÁLLÁS telepítését bemutató példát-portokat biztosít egy belső terheléselosztón. Virtuális készülékek hálózati konfigurációkkal tekintse meg a megfelelő szolgáltató webhelyeket.

>[!NOTE]
> Magas rendelkezésre állás portok funkció jelenleg előzetes verzió. Az előzetes kiadás során a szolgáltatás rendelkezésre állása és megbízhatósága eltérő lehet az általánosan elérhető kiadásétól. További részletekért lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

1. ábra azt mutatja be, a jelen cikkben ismertetett telepítési példában a következő konfigurációt:
- A NVAs egy belső terheléselosztó mögött a magas rendelkezésre ÁLLÁSÚ portok konfigurációs háttérkészlethez vannak telepítve. 
- A UDR alkalmazza a DMZ alhálózati útvonalak az összes forgalom a <>? azáltal, hogy a belső terheléselosztási terheléselosztó virtuális IP-cím, a következő ugrás. 
- Belső terheléselosztó osztja el a forgalmat a Terheléselosztó algoritmus alapján aktív NVAs egyikét.
- NVA dolgozza fel a forgalmat, és továbbítja a backend alhálózathoz az eredeti célra.
- A visszaadott elérési út is esetén is igénybe vehet a azonos útvonal a megfelelő UDR konfigurálva van a backend alhálózathoz. 

![magas rendelkezésre állású portok a központi telepítési példa](./media/load-balancer-configure-ha-ports/haports.png)

1. ábra - hálózat virtuális készülékek a magas rendelkezésre állású portokkal egy belső terheléselosztó mögé telepítették 

## <a name="preview-sign-up"></a>Előnézet-előfizetés

Előzetes verziójú Load Balancer Standard Termékváltozat portok funkcióját a magas rendelkezésre ÁLLÁSÚ részt, előfizetése hozzáférhet a PowerShell vagy Azure CLI 2.0 használatával regisztrálja.

- Regisztráció PowerShell használatával

   ```powershell
   Register-AzureRmProviderFeature -FeatureName AllowILBAllPortsRule -ProviderNamespace Microsoft.Network
    ```

- Regisztráljon az Azure CLI 2.0 verziót használja

    ```cli
  az feature register --name AllowILBAllPortsRule --namespace Microsoft.Network  
    ```

## <a name="configuring-ha-ports"></a>Magas rendelkezésre ÁLLÁSÚ portok konfigurálása

A magas rendelkezésre ÁLLÁSÚ portok konfigurációja magában foglalja a egy megfelelő terheléselosztó állapotfigyelő mintavétel-konfiguráció észleléséhez NVA állapotát, és a magas rendelkezésre ÁLLÁSÚ porttal rendelkező terheléselosztó szabály egy belső terheléselosztó, a háttérkészletben NVAs a beállítása. Az általános terheléselosztóhoz kapcsolódó konfigurációs tárgyalja [Ismerkedés](load-balancer-get-started-ilb-arm-portal.md). Ez a cikk a magas rendelkezésre ÁLLÁSÚ portok konfigurációját mutatja be.

A konfigurációs igényli érték az elülső rétegbeli portot és a háttérkiszolgáló port **0**, és a protokoll értéket **összes**. Ez a cikk ismerteti az Azure-portálon, a PowerShell és az Azure CLI 2.0 használatával magas rendelkezésre állású portok konfigurálása.

### <a name="configure-ha-ports-load-balancer-rule-with-the-azure-portal"></a>Magas rendelkezésre ÁLLÁSÚ portok terheléselosztási szabály konfigurálása az Azure-portálon

Az Azure portálon a **magas rendelkezésre ÁLLÁSÚ portok** beállítást ehhez a konfigurációhoz jelölőnégyzetet keresztül. Ha ki van választva, a port és protokoll konfigurációs automatikusan feltöltődik értékkel. 

![magas rendelkezésre állású portok konfigurálása Azure-portálon](./media/load-balancer-configure-ha-ports/haports-portal.png)

2. ábra – magas rendelkezésre ÁLLÁSÚ portok konfigurációs portálon

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
