---
title: Egy Azure Load Balancer-szabályt egy fürt
description: Állítsa be az Azure Load Balancerhez az Azure Service Fabric-fürt a portok megnyitásához.
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: ryanwi
ms.openlocfilehash: e41e5d48141b120ec1f80b0faa899bda07a2934e
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52962063"
---
# <a name="open-ports-for-a-service-fabric-cluster"></a>Nyissa meg a portokat a Service Fabric-fürt

A terheléselosztó az Azure Service Fabric-fürtben üzembe helyezett irányítja a forgalmat a csomóponton futó alkalmazásnak. Ha megváltoztatja az alkalmazás egy másik port használatára, a portok (vagy az útvonal egy másik portot) az Azure Load balancerben.

A Service Fabric-fürt üzembe helyezésekor az Azure-ba, az Ön számára automatikusan létrehozott egy terheléselosztót. Ha nem rendelkezik egy terheléselosztót, [egy internetkapcsolattal rendelkező load balancer konfigurálása](../load-balancer/load-balancer-get-started-internet-portal.md).

## <a name="configure-service-fabric"></a>A service fabric beállítása

A Service Fabric-alkalmazás **ServiceManifest.xml** konfigurációs fájl definiálja a használandó vár az alkalmazás végpontok. Miután a konfigurációs fájl frissült a végpontokat, a terheléselosztó frissíteni kell, hogy elérhetővé, amely (vagy egy másik) porton. A service fabric-végpont létrehozása további információkért lásd: [egy végpont beállítása](service-fabric-service-manifest-resources.md).

## <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása

A terheléselosztási szabállyal egy internetre irányuló portot nyitja meg, és továbbítja a forgalmat a belső csomópont port az alkalmazása által használt. Ha nem rendelkezik egy terheléselosztót, [egy internetkapcsolattal rendelkező load balancer konfigurálása](../load-balancer/load-balancer-get-started-internet-portal.md).

Hozzon létre egy terheléselosztó-szabályt, hogy kell gyűjtenie a következő információkat:

- Terheléselosztó neve.
- A load balancer és a service fabric-fürt erőforráscsoportja.
- Külső porton.
- Belső port.

## <a name="azure-cli"></a>Azure CLI
Mindössze egyetlen paranccsal hozhat létre egy terheléselosztó-szabályt az a **Azure CLI-vel**. Csak kell tudni, hogy mindkét hozzon létre egy új szabályt a terhelés terheléselosztó és az erőforrás csoport nevére.

>[!NOTE]
>Meg kell határoznia a terheléselosztó nevét, ha ez a parancs segítségével gyorsan hozzáférhet az összes terheléselosztókról, illetve a kapcsolódó erőforráscsoportok listája.
>
>`az network lb list --query "[].{ResourceGroup: resourceGroup, Name: name}"`
>


```azurecli
az network lb rule create --backend-port 40000 --frontend-port 39999 --protocol Tcp --lb-name LB-svcfab3 -g svcfab_cli -n my-app-rule
```

Az Azure CLI-paranccsal néhány az alábbi táblázatban ismertetett paraméterekkel rendelkezik:

| Paraméter | Leírás |
| --------- | ----------- |
| `--backend-port`  | A Service Fabric-alkalmazás port figyel. |
| `--frontend-port` | A port a terheléselosztót külső kapcsolatok tesz elérhetővé. |
| `-lb-name` | Módosíthatja, hogy a terheléselosztó neve. |
| `-g`       | Az erőforráscsoport, amely rendelkezik a load balancer és a Service Fabric-fürtön. |
| `-n`       | A szabály a kívánt nevet. |


>[!NOTE]
>Terheléselosztó létrehozása az Azure CLI-vel kapcsolatos további információkért lásd: [load balancer létrehozása az Azure CLI-vel](../load-balancer/load-balancer-get-started-internet-arm-cli.md).

## <a name="powershell"></a>PowerShell

PowerShell az egy kicsit bonyolultabb, mint az Azure parancssori felület. Kövesse az alábbi általános lépéseket szabály létrehozása:

1. A terheléselosztó lekérése az Azure-ból.
2. Hozzon létre egy szabályt.
3. A szabály hozzáadása a terheléselosztóhoz.
4. Frissítse a terheléselosztót.

>[!NOTE]
>Határozza meg a terheléselosztó nevét kell, ha ez a parancs segítségével gyorsan hozzáférhet a terheléselosztókkal és a kapcsolódó erőforráscsoportok listája.
>
>`Get-AzureRmLoadBalancer | Select Name, ResourceGroupName`

```powershell
# Get the load balancer
$lb = Get-AzureRmLoadBalancer -Name LB-svcfab3 -ResourceGroupName svcfab_cli

# Create the rule based on information from the load balancer.
$lbrule = New-AzureRmLoadBalancerRuleConfig -Name my-app-rule7 -Protocol Tcp -FrontendPort 39990 -BackendPort 40009 `
                                            -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
                                            -BackendAddressPool  $lb.BackendAddressPools[0] `
                                            -Probe $lb.Probes[0]

# Add the rule to the load balancer
$lb.LoadBalancingRules.Add($lbrule)

# Update the load balancer on Azure
$lb | Set-AzureRmLoadBalancer
```

Kapcsolatos a `New-AzureRmLoadBalancerRuleConfig` parancsot, a `-FrontendPort` jelenti. a portot a load balancer tesz elérhetővé a külső kapcsolatokat, és a `-BackendPort` jelenti. a portot, a service fabric-alkalmazás figyel.

>[!NOTE]
>Load balancer létrehozása a PowerShell-lel kapcsolatos további információkért lásd: [load balancer létrehozása a PowerShell-lel](../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## <a name="next-steps"></a>További lépések

Tudjon meg többet [hálózatkezelés a Service Fabric](service-fabric-patterns-networking.md).