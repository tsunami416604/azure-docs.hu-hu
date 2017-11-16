---
title: "Kezelése Azure DDoS védelem szabványos Azure PowerShell használatával |} Microsoft Docs"
description: "Útmutató: Azure DDoS védelem szabványos Azure PowerShell használatával kezelheti."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2017
ms.author: jdial
ms.openlocfilehash: baac97db61b84000557e7150a64ffb64d81ce00c
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2017
---
# <a name="manage-azure-ddos-protection-standard-using-azure-powershell"></a>Azure DDoS védelem szabványos az Azure PowerShell kezelése

Megtudhatja, hogyan engedélyezése és elosztott szolgáltatásmegtagadásos (DDoS-) szolgáltatás védelem letiltásához, és telemetriai adatokat a Azure DDoS védelem standard DDoS-támadások mérséklése. DDoS védelem szabványos védelmet nyújt az Azure erőforrások, például a virtuális gépek terheléselosztók és alkalmazásátjárót, amelyek egy Azure [nyilvános IP-cím](virtual-network-public-ip-address.md) rendelve. DDoS védelem szabványos és platformképességei kapcsolatos további információkért lásd: [DDoS védelem szabványos áttekintése](ddos-protection-overview.md). 

>[!IMPORTANT]
>Az Azure DDoS védelem szabványos (DDoS-védelem) jelenleg előzetes verzió. Azure-erőforrások korlátozott számú DDoS-védelem támogatása, és azt el csak select több régióban. Elérhető régiók listáját lásd: [DDoS védelem szabványos áttekintése](ddos-protection-overview.md). Kell [a szolgáltatás regisztrálása](http://aka.ms/ddosprotection) a korlátozott előzetes DDoS-védelem engedélyezve van az előfizetéshez tartozó eléréséhez. A regisztrálás után végigvezeti Önt az engedélyezési folyamat Azure DDoS csoport által próbál kapcsolódni.


## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a `Login-AzureRmAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. Ha szeretné telepíteni vagy frissíteni az Azure PowerShell című [telepítése Azure PowerShell modul](/powershell/azure/install-azurerm-ps).

```powershell
Login-AzureRmAccount
```

## <a name="enable-ddos-protection-standard---new-virtual-network"></a>DDoS védelem szabványos – új virtuális hálózat engedélyezése

DDoS-védelemmel ellátott virtuális hálózat létrehozásához futtassa az alábbi példa:

```powershell
New-AzureRmResourceGroup -Name <ResourceGroupName> -Location westcentralus 
$frontendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name <frontendSubnet> -AddressPrefix "10.0.1.0/24" 
$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name <backendSubnet> -AddressPrefix "10.0.2.0/24" 
New-AzureRmVirtualNetwork -Name <MyVirtualNetwork> -ResourceGroupName <ResourceGroupName>  -Location westcentralus  -AddressPrefix "10.0.0.0/16" -Subnet $frontendSubnet,$backendSubnet -EnableDDoSProtection
```

Ebben a példában egy virtuális hálózatot hoz létre két alhálózat és a két DNS-kiszolgálók. A hatását, hogy a DNS-kiszolgálók megadása a virtuális hálózaton, hogy a hálózati adapterek vagy virtuális gépeket létrehozni a virtuális hálózatban üzembe helyezett öröklik a DNS-kiszolgálókhoz, alapértelmezett értéke. DDoS-védelem engedélyezve van a védett erőforrások, a virtuális hálózat.

> [!WARNING]
> A régió kiválasztásakor válassza a listából egy támogatott régióban [Azure DDoS védelem szabványos áttekintése](ddos-protection-overview.md).

## <a name="enable-ddos-protection-on-an-existing-virtual-network"></a>DDoS-védelem engedélyezése az egy meglévő virtuális hálózathoz

A meglévő virtuális hálózat DDoS-védelem engedélyezéséhez futtassa a következő példa:

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps.enableDdosProtection = $true
Set-AzureRmResource -PropertyObject $vnetProps -ResourceGroupName "ResourceGroupName" -ResourceName "ResourceName" -ResourceType Microsoft.Network/virtualNetworks -Force
```

> [!WARNING]
> A virtuális hálózati léteznie kell egy támogatott régióban. A támogatott régiók listáját lásd: [Azure DDoS védelem szabványos áttekintése](ddos-protection-overview.md).

## <a name="disable-ddos-protection-on-a-virtual-network"></a>DDoS-védelem letiltása a virtuális hálózaton

A virtuális hálózaton DDoS-védelem letiltásához futtassa az alábbi példa:

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps.enableDdosProtection = $false
Set-AzureRmResource -PropertyObject $vnetProps -ResourceGroupName <RessourceGroupName> -ResourceName <ResourceName> -ResourceType "Microsoft.Network/virtualNetworks" -Force
```

## <a name="review-the-ddos-protection-status-of-a-virtual-network"></a>A virtuális hálózati DDoS védelem állapotának áttekintése 

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps
```

## <a name="use-ddos-protection-telemetry"></a>DDoS-védelem telemetriai adatok használata

A támadás telemetriai valós idejű figyelése Azure keresztül valósul meg. A telemetriai adatok csak a megoldás alatt álló egy nyilvános IP-cím időtartamára érhető el. Telemetria előtt vagy után a támadás elhárítására nem látják.

### <a name="configure-alerts-on-ddos-protection-metrics"></a>DDoS-védelem mérőszámokat riasztások konfigurálása

Használhatja az Azure-figyelő riasztás konfigurálásában, kiválaszthatja bármelyik elérhető DDoS-védelem metrikai riasztást küld, ha egy aktív megoldás a támadás során.

#### <a name="configure-email-alert-rules-via-azure"></a>E-mail értesítési szabályok keresztül Azure konfigurálása

1. Listáját, az előfizetéssel elérhető rendelkezik. Győződjön meg arról, hogy a megfelelő előfizetés dolgozik. Ha nem, akkor állítsa az értékét használja a Get-AzureRmSubscription kimenetét a megfelelőt. 

    ```powershell
    Get-AzureRmSubscription 
    Get-AzureRmContext 
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```

2. Egy erőforráscsoportot a meglévő szabályok listájában, használja a következő parancsot: 

    ```powershell
    Get-AzureRmAlertRule -ResourceGroup <myresourcegroup> -DetailedOutput
    ```

3. Olyan szabály létrehozására, a következő adatokat először rendelkeznie kell: 

    - Az erőforrás-azonosítója az erőforrás be szeretné állítani egy riasztást.
    - A metrikai meghatározásainak az adott erőforráshoz. Egy az erőforrás-azonosító eléréséhez módja az Azure-portálon. Ha az erőforrás létrehozása már be van állítva, válassza ki azt az Azure portálon. A következő oldalon válassza ki *tulajdonságok* alatt a *beállítások* szakasz. A **erőforrás-azonosító** mező a következő oldalra. Egy másik módja a [Azure erőforrás-kezelő](https://resources.azure.com/). Lehet például egy nyilvános IP-cím erőforrás-azonosító:`/subscriptions/<Id>/resourceGroups/myresourcegroupname/providers/Microsoft.Network/publicIPAddresses/mypublicip`

    Az alábbi példában egy virtuális hálózat olyan erőforráshoz tartozó nyilvános IP-cím riasztást hoz létre. A riasztás létrehozása a mérték **alatt DDoS támadás, vagy nem**. Ez az egy logikai érték 1 vagy 0. A **1** azt jelenti, hogy támadás alatt áll. A **0** azt jelenti, hogy nem támadás alatt áll. A riasztás akkor jön létre, az utolsó 5 percen belül a támadás indításakor.

    A webhook létrehozása vagy e-mailt küld, ha riasztás jön létre, először létre kell hoznia az e-mailek és/vagy a webhook. Miután létrehozta az e-mailben vagy webhook, azonnal a szabály létrehozása a `-Actions` címke, a következő példában látható módon. PowerShell-lel meglévő szabályok nem webhook vagy e-mailek társítani.

    ```powershell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com 
    Add-AzureRmMetricAlertRule -Name <myMetricRuleWithEmail> -Location "West Central US" -ResourceGroup <myresourcegroup> -TargetResourceId /subscriptions/<Id>/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mypublicip -MetricName "IfUnderDDoSAttack" -Operator GreaterThan -Threshold 0 -WindowSize 00:05:00 -TimeAggregationOperator Total -Actions $actionEmail -Description "Under DDoS Attack"
    ```

4. Győződjön meg arról, hogy a riasztás létre megfelelően, ha megnézi a szabály:

    ```powershell
    Get-AzureRmAlertRule -Name myMetricRuleWithEmail -ResourceGroup myresourcegroup -DetailedOutput 
    ```

Még többet is megtudhat arról [konfigurálása webhookokkal](../monitoring-and-diagnostics/insights-webhooks-alerts.md?toc=%2fazure%2fvirtual-network%2ftoc.json) és [a logic apps](../logic-apps/logic-apps-what-are-logic-apps.md) riasztások létrehozásához.

## <a name="configure-logging-on-ddos-protection-metrics"></a>DDoS-védelem mérőszámokat naplózás konfigurálása

Tekintse meg a [PowerShell quick start minták](../monitoring-and-diagnostics/insights-powershell-samples.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eléréséhez, és konfigurálni az Azure diagnosztikai naplózás PowerShell nyújtanak segítséget.

## <a name="next-steps"></a>Következő lépések

- [További tudnivalók az Azure diagnosztikai naplók](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [A Naplóelemzési az Azure storage naplóinak elemzése](../log-analytics/log-analytics-azure-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Bevezetés az Event Hubs használatába](../event-hubs/event-hubs-csharp-ephcs-getstarted.md?toc=%2fazure%2fvirtual-network%2ftoc.json)