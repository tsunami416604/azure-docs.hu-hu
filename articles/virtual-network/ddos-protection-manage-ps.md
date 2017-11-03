---
title: "Kezelése Azure DDoS védelem szabványos Azure PowerShell használatával |} Microsoft Docs"
description: "Útmutató: Azure DDoS védelem szabványos Azure PowerShell használatával kezelheti."
services: virtual-network
documentationcenter: na
author: kumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: kumud
ms.openlocfilehash: a1a3688d4ff215d05d2f78cdfa7d402e3fc20be2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-ddos-protection-standard-using-azure-powershell"></a>Azure DDoS védelem szabványos az Azure PowerShell kezelése

>[!IMPORTANT]
>Az Azure DDoS védelem szabványos (DDoS-védelem) jelenleg előzetes verzió. Korlátozott számú Azure-erőforrások támogatása DDoS-védelem, valamint a select több régióban. Kell [a szolgáltatás regisztrálása](http://aka.ms/ddosprotection) a korlátozott előzetes DDoS-védelem engedélyezve van az előfizetéshez tartozó eléréséhez. Próbál kapcsolódni, az Azure DDoS csoport regisztráláskor végigvezeti az engedélyezési folyamat által. Amerikai keleti, Velünk nyugati és nyugati középső Régiójában régiók DDoS-védelem nem áll rendelkezésre. Előzetes akkor nem számolnak szolgáltatásunkat használja.

Ez a cikk bemutatja, hogyan használható az Azure PowerShell DDoS-védelem engedélyezése, DDoS-védelem letiltásához, és telemetriai adatokat a támadás elhárítása érdekében.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. Ha szeretné telepíteni vagy frissíteni az Azure PowerShell című [telepítése Azure PowerShell modul](/powershell/azure/install-azurerm-ps).

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a `Login-AzureRmAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

```powershell
Login-AzureRmAccount
```

## <a name="enable-ddos-protection"></a>DDoS-védelem engedélyezése

### <a name="create-a-new-virtual-network-and-enable-ddos-protection"></a>Hozzon létre egy új virtuális hálózatot és DDoS-védelem engedélyezése

DDoS-védelemmel ellátott virtuális hálózat létrehozásához futtassa az alábbi példa:

```powershell
New-AzureRmResourceGroup -Name <ResourceGroupName> -Location westcentralus 
$frontendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name <frontendSubnet> -AddressPrefix "10.0.1.0/24" 
$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name <backendSubnet> -AddressPrefix "10.0.2.0/24" 
New-AzureRmVirtualNetwork -Name <MyVirtualNetwork> -ResourceGroupName <ResourceGroupName>  -Location westcentralus  -AddressPrefix "10.0.0.0/16" -Subnet $frontendSubnet,$backendSubnet -DnsServer 10.0.1.5,10.0.1.6 -EnableDDoSProtection
```

Ebben a példában egy virtuális hálózatot hoz létre két alhálózat és a két DNS-kiszolgálók. A hatását, hogy a DNS-kiszolgálók megadása a virtuális hálózaton, hogy a hálózati adapterek vagy virtuális gépeket létrehozni a virtuális hálózatban üzembe helyezett öröklik a DNS-kiszolgálókhoz, alapértelmezett értéke. DDoS-védelem engedélyezve van a védett erőforrások, a virtuális hálózat.

### <a name="enable-ddos-protection-on-an-existing-virtual-network"></a>DDoS-védelem engedélyezése az egy meglévő virtuális hálózathoz

A meglévő virtuális hálózat DDoS-védelem engedélyezéséhez futtassa a következő példa:

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps.enableDdosProtection = $true
Set-AzureRmResource -PropertyObject $vnetProps -ResourceGroupName "ResourceGroupName" -ResourceName "ResourceName" -ResourceType Microsoft.Network/virtualNetworks
```

## <a name="disable-ddos-protection-on-a-virtual-network"></a>DDoS-védelem letiltása a virtuális hálózaton

A virtuális hálózaton DDoS-védelem letiltásához futtassa az alábbi példa:

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps.enableDdosProtection = $false
Set-AzureRmResource -PropertyObject $vnetProps -ResourceGroupName <RessourceGroupName> -ResourceName <ResourceName> -ResourceType "Microsoft.Network/virtualNetworks"
```

## <a name="review-the-ddos-protection-status-of-virtual-networks"></a>Virtuális hálózatok DDoS védelem állapotának áttekintése 

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps
```

## <a name="use-ddos-protection-telemetry"></a>DDoS-védelem telemetriai adatok használata

A támadás telemetriai valós idejű figyelése Azure keresztül valósul meg. A telemetriai adatok csak a megoldás alatt álló egy nyilvános IP-cím időtartamára érhető el. Telemetria előtt vagy után a támadás elhárítására nem látják.

### <a name="configure-alerts-on-ddos-protection-metrics"></a>DDoS-védelem mérőszámokat riasztások konfigurálása

Használhatja az Azure-figyelő riasztás konfigurálásában, kiválaszthatja bármelyik elérhető DDoS-védelem metrikai riasztást küld, ha egy aktív megoldás a támadás során.

#### <a name="configure-email-alert-rules-via-azure-powershell"></a>E-mail értesítési szabályok Azure PowerShell konfigurálása

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

3. Olyan szabály létrehozására, először rendelkezik néhány fontos adatot kell. 

    - Az erőforrás-azonosítója az erőforrás be szeretné állítani egy riasztást.
    - A metrikai meghatározásainak az adott erőforráshoz. Egy az erőforrás-azonosító eléréséhez módja az Azure-portálon. Ha az erőforrás létrehozása már be van állítva, válassza ki azt az Azure portálon. A következő oldalon válassza ki *tulajdonságok* alatt a *beállítások* szakasz. A **erőforrás-azonosító** mező a következő oldalra. Egy másik módja a [Azure erőforrás-kezelő](https://resources.azure.com/). Lehet például egy nyilvános IP-cím erőforrás-azonosító:`/subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Network/publicIPAddresses/mypublicip`

    Az alábbi példa beállítja, egy riasztás egy virtuális hálózathoz tartozó nyilvános IP-címen. A riasztás létrehozása a mérték **alatt DDoS támadás, vagy nem**. Ez az egy logikai érték 1 vagy 0. A **1** azt jelenti, hogy támadás alatt áll. A **0** azt jelenti, hogy nem támadás alatt áll. A riasztás akkor jön létre, ha az utolsó 5 percen belül támadás alatt áll.

    A webhook létrehozása vagy e-mailt küld, ha riasztás jön létre, először létre kell hoznia az e-mailek és/vagy a webhook. Majd azonnal a szabály létrehozása után a - műveletek címkével, és az alábbi példában látható. Nem társítható webhook vagy e-mailek már hozott létre a szabályokat PowerShell-lel.

    ```powershell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com 
    Add-AzureRmMetricAlertRule -Name <myMetricRuleWithEmail> -Location "West Central US" -ResourceGroup <myresourcegroup> -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mypublicip -MetricName "IfUnderDDoSAttack" -Operator GreaterThan -Threshold 0 -WindowSize 00:05:00 -TimeAggregationOperator Total -Actions $actionEmail-Description "Under DDoS Attack" 
    ```

4. Ellenőrizheti, hogy a riasztások elkészültek megfelelően az egyes szabályok alapján.

    ```powershell
    Get-AzureRmAlertRule -Name myMetricRuleWithEmail -ResourceGroup myresourcegroup -DetailedOutput 
    Get-AzureRmAlertRule -Name myLogAlertRule -ResourceGroup myresourcegroup -DetailedOutput
    ```

Még többet is megtudhat arról [konfigurálása webhookokkal](../monitoring-and-diagnostics/insights-webhooks-alerts.md) és [a logic apps](../logic-apps/logic-apps-what-are-logic-apps.md) riasztások létrehozásához.

## <a name="configure-logging-on-ddos-protection-metrics"></a>DDoS-védelem mérőszámokat naplózás konfigurálása

Tekintse meg a [PowerShell quick start minták](../monitoring-and-diagnostics/insights-powershell-samples.md) eléréséhez, és konfigurálni az Azure diagnosztikai naplózás PowerShell nyújtanak segítséget.

## <a name="next-steps"></a>Következő lépések

- [További tudnivalók az Azure diagnosztikai naplók](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)
- [A Naplóelemzési az Azure storage naplóinak elemzése](../log-analytics/log-analytics-azure-storage.md)
- [Bevezetés az Event Hubs használatába](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)