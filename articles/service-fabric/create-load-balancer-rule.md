---
title: Fürt Azure Load Balancer szabály létrehozása
description: Konfigurálja az Azure Load Balancer megnyitásához az Azure Service Fabric-fürt által használt portokat.
services: service-fabric
documentationcenter: na
author: thraka
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: adegeo
ms.openlocfilehash: 53dcd6c0705faa94e83d6e44f813fa9c575843e8
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="open-ports-for-a-service-fabric-cluster"></a>Nyissa meg a Service Fabric-fürt által használt portokat

A load balancer telepítik az Azure Service Fabric-fürt irányítja az egy csomóponton futó alkalmazás felé irányuló forgalom. Ha megváltoztatja az alkalmazás egy másik port használatára, tesz közzé, hogy a port (vagy az útvonal egy másik portot) a az Azure Load Balancer.

A Service Fabric-fürt üzembe helyezésekor az Azure-ba, a terheléselosztó automatikusan létrejött meg. Ha nem rendelkezik olyan terheléselosztóhoz, lásd: [egy internetre irányuló terheléskiegyenlítő](..\load-balancer\load-balancer-get-started-internet-portal.md).

## <a name="configure-service-fabric"></a>A service fabric konfigurálása

A Service Fabric-alkalmazás **ServiceManifest.xml** konfigurációs fájl határozza meg a végpontokat, az alkalmazás használatához vár. Miután a konfigurációs fájl frissült a végpont meghatározása, a terheléselosztó frissíteni kell, amely (vagy egy másik) port. A service fabric-végpont létrehozásával kapcsolatos további információkért lásd: [egy végpont beállítása](service-fabric-service-manifest-resources.md).

## <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása

Terheléselosztó szabály egy internetre irányuló portot nyit, és továbbítja az alkalmazás által használt belső csomópont port forgalmat. Ha nem rendelkezik olyan terheléselosztóhoz, lásd: [egy internetre irányuló terheléskiegyenlítő](..\load-balancer\load-balancer-get-started-internet-portal.md).

Terheléselosztó szabály létrehozásához kell összegyűjtenie a következő információkat:

- Terheléselosztó neve.
- A betöltési terheléselosztó és a service fabric-fürt erőforráscsoportot.
- Külső porton.
- Belső port.

## <a name="azure-cli"></a>Azure CLI
Csak az olyan terheléselosztó szabályhoz létrehozásához egy parancs vesz igénybe a **Azure CLI**. Egyszerűen mindkét a nevére, a betöltés terheléselosztó és erőforrás csoportjának hozzon létre egy új szabályt.

>[!NOTE]
>Ha meg kell határoznia a terheléselosztó nevét, a parancs segítségével gyorsan az összes terheléselosztókról, illetve a kapcsolódó erőforrás-csoportok listájának beolvasása.
>
>`az network lb list --query "[].{ResourceGroup: resourceGroup, Name: name}"`
>


```azurecli
az network lb rule create --backend-port 40000 --frontend-port 39999 --protocol Tcp --lb-name LB-svcfab3 -g svcfab_cli -n my-app-rule
```

Az Azure CLI parancsnak néhány paramétert, amely a következő táblázat ismerteti:

| Paraméter | Leírás |
| --------- | ----------- |
| `--backend-port`  | A port a Service Fabric-alkalmazás figyel. |
| `--frontend-port` | A port a terheléselosztó elérhetővé teszi a külső kapcsolatot. |
| `-lb-name` | A terheléselosztó módosításához neve. |
| `-g`       | Az erőforráscsoport, amelyen a terheléselosztó és a Service Fabric-fürt. |
| `-n`       | A szabály a kívánt nevet. |


>[!NOTE]
>A terheléselosztó létrehozása az Azure parancssori felület további információkért lásd: [hozzon létre egy terhelés-kiegyenlítő az Azure parancssori felület](..\load-balancer\load-balancer-get-started-internet-arm-cli.md).

## <a name="powershell"></a>PowerShell

PowerShell egy kicsit bonyolultabb, mint az Azure parancssori felület. Olyan szabály létrehozására fogalmi lépései:

1. A load balancer beolvasása az Azure-ból.
2. Hozzon létre egy szabályt.
3. A szabály hozzáadása a terheléselosztóhoz.
4. A terheléselosztó frissítése.

>[!NOTE]
>Ha meg kell határoznia a terheléselosztó nevét, a parancs segítségével gyorsan a terheléselosztókról, illetve a kapcsolódó erőforráscsoport-sablonok listájának beolvasása.
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

Vonatkozó a `New-AzureRmLoadBalancerRuleConfig` parancs, a `-FrontendPort` a portot, a terheléselosztó elérhetővé teszi a külső kapcsolatot jelenti és a `-BackendPort` a service fabric-alkalmazás figyel a portot jelenti.

>[!NOTE]
>Terheléselosztó létrehozása a PowerShell használatával kapcsolatos további információkért lásd: [terheléselosztó létrehozása a PowerShell használatával](..\load-balancer\load-balancer-get-started-internet-arm-ps.md).

## <a name="next-steps"></a>További lépések

További információ [hálózatkezelés a Service Fabric](service-fabric-patterns-networking.md).