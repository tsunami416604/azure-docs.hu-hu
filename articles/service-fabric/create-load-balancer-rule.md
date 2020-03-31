---
title: Azure-terheléselosztó szabály létrehozása fürthöz
description: Konfiguráljon egy Azure Load Balancer-t az Azure Service Fabric-fürt portjainak megnyitásához.
ms.topic: conceptual
ms.date: 12/06/2017
ms.openlocfilehash: f4599b2e0174381ab7df04aeeb33db7e3ee60f26
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025384"
---
# <a name="open-ports-for-a-service-fabric-cluster"></a>Service Fabric-fürt portjainak megnyitása

Az Azure Service Fabric-fürtdel telepített terheléselosztó a forgalmat egy csomóponton futó alkalmazásra irányítja. Ha módosítja az alkalmazást, hogy egy másik portot használjon, meg kell adnia a portot (vagy egy másik portot) az Azure Load Balancer-ben.

Amikor üzembe helyezte a Service Fabric-fürtöt az Azure-ba, a rendszer automatikusan létrehozott egy terheléselosztót. Ha nem rendelkezik terheléselosztóval, olvassa el [az Internetfelé néző terheléselosztó konfigurálása](../load-balancer/load-balancer-get-started-internet-portal.md)című témakört.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-service-fabric"></a>Szolgáltatásháló konfigurálása

A Service Fabric alkalmazás **ServiceManifest.xml** konfigurációs fájl határozza meg a végpontok az alkalmazás várhatóan használni. Miután a konfigurációs fájl frissítése megtörtént egy végpont meghatározásához, a terheléselosztót frissíteni kell az adott (vagy egy másik) port elérhetővé tételéhez. A szolgáltatásháló végpontjának létrehozásáról további információt a Végpont beállítása című témakörben [talál.](service-fabric-service-manifest-resources.md)

## <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása

A terheléselosztó szabály megnyit egy internetfelé néző portot, és továbbítja a forgalmat az alkalmazás által használt belső csomópont portjára. Ha nem rendelkezik terheléselosztóval, olvassa el [az Internetfelé néző terheléselosztó konfigurálása](../load-balancer/load-balancer-get-started-internet-portal.md)című témakört.

Terheléselosztó szabály létrehozásához a következő adatokat kell gyűjtenie:

- Terheléselosztó neve.
- A terheléselosztó és a szolgáltatásháló-fürt erőforráscsoportja.
- Külső port.
- Belső port.

## <a name="azure-cli"></a>Azure CLI
Csak egy parancsot, hogy hozzon létre egy terheléselosztó szabály az **Azure CLI.** Az új szabály létrehozásához csak ismernie kell a terheléselosztó és az erőforráscsoport nevét is.

>[!NOTE]
>Ha meg kell határoznia a terheléselosztó nevét, ezzel a paranccsal gyorsan lekaphatja az összes terheléselosztó és a kapcsolódó erőforráscsoportok listáját.
>
>`az network lb list --query "[].{ResourceGroup: resourceGroup, Name: name}"`
>


```azurecli
az network lb rule create --backend-port 40000 --frontend-port 39999 --protocol Tcp --lb-name LB-svcfab3 -g svcfab_cli -n my-app-rule
```

Az Azure CLI parancs néhány paramétert, amelyek az alábbi táblázatban ismertetik:

| Paraméter | Leírás |
| --------- | ----------- |
| `--backend-port`  | A Port a Service Fabric alkalmazás figyel. |
| `--frontend-port` | A terheléselosztó által a külső kapcsolatokhoz elérhető port. |
| `-lb-name` | A módosítandó terheléselosztó neve. |
| `-g`       | Az erőforráscsoport, amely a terheléselosztó és a Service Fabric-fürt is rendelkezik. |
| `-n`       | A szabály kívánt neve. |


>[!NOTE]
>Ha többet szeretne tudni arról, hogyan hozhat létre terheléselosztót az Azure CLI-vel, olvassa [el a Terheléselosztó létrehozása az Azure CLI-vel című témakört.](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)

## <a name="powershell"></a>PowerShell

PowerShell egy kicsit bonyolultabb, mint az Azure CLI. Szabály létrehozásához kövesse az alábbi általános lépéseket:

1. Töltse le a terheléselosztót az Azure-ból.
2. Hozzon létre egy szabályt.
3. Adja hozzá a szabályt a terheléselosztóhoz.
4. Frissítse a terheléselosztót.

>[!NOTE]
>Ha meg kell határoznia a terheléselosztó nevét, ezzel a paranccsal gyorsan lekaphatja az összes terheléselosztó és a hozzá tartozó erőforráscsoportok listáját.
>
>`Get-AzLoadBalancer | Select Name, ResourceGroupName`

```powershell
# Get the load balancer
$lb = Get-AzLoadBalancer -Name LB-svcfab3 -ResourceGroupName svcfab_cli

# Create the rule based on information from the load balancer.
$lbrule = New-AzLoadBalancerRuleConfig -Name my-app-rule7 -Protocol Tcp -FrontendPort 39990 -BackendPort 40009 `
                                            -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
                                            -BackendAddressPool  $lb.BackendAddressPools[0] `
                                            -Probe $lb.Probes[0]

# Add the rule to the load balancer
$lb.LoadBalancingRules.Add($lbrule)

# Update the load balancer on Azure
$lb | Set-AzLoadBalancer
```

Ami `New-AzLoadBalancerRuleConfig` a parancsot, a `-FrontendPort` jelöli a portot a terheléselosztó `-BackendPort` elérhetővé teszi a külső kapcsolatok, és a képviseli a port a service fabric alkalmazás hallgatja.

>[!NOTE]
>Ha többet szeretne tudni arról, hogyan hozhat létre terheléselosztót a PowerShell használatával, olvassa [el a Terheléselosztó létrehozása a PowerShell használatával című témakört.](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)

## <a name="next-steps"></a>További lépések

További információ a [Service Fabric hálózatba való védelméről.](service-fabric-patterns-networking.md)
