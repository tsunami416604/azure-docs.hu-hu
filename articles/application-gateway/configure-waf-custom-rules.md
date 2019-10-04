---
title: Webalkalmazási tűzfal v2 egyéni szabályok Azure PowerShell-lel konfigurálása
description: Ismerje meg, hogyan konfigurálhatja a WAF v2 egyéni szabályok Azure PowerShell-lel
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 6/18/2019
ms.author: victorh
ms.openlocfilehash: f4d2fd7342e0efe95a1bc69e0dba77692053cf14
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164752"
---
# <a name="configure-web-application-firewall-v2--with-a-custom-rule-using-azure-powershell"></a>Webalkalmazási tűzfal v2 konfigurálása az Azure PowerShell-lel egy egyéni szabály

<!--- If you make any changes to the PowerShell in this article, also make the change in the corresponding Sample file: azure-docs-powershell-samples/application-gateway/waf-rules/waf-custom-rules.ps1 --->

Egyéni szabályok lehetővé teszik az egyes kérések, amely áthalad a webalkalmazási tűzfal (WAF) v2 értékeli ki a saját szabályokat hozhat létre. Ezek a szabályok egy magasabb prioritású, mint a szabályokat a többi felügyelt szabálykészletek tart. Az egyéni szabályokat kell vonatkozó műveletet (engedélyezés vagy letiltás), egyeztetési feltételt, és lehetővé teszi a teljes testreszabását az operátor.

Ebben a cikkben létrehoz egy Application Gateway WAF v2-környezetet, amely egy egyéni szabályt használ. Az egyéni szabály blokkolja a forgalmat, ha a kérelem fejléce tartalmazza a felhasználói ügynök *evilbot*.

Egyéni szabály további példákért lásd: [létrehozása és használata egyéni webalkalmazási tűzfalszabályok](create-custom-waf-rules.md)

Futtatási ebben a cikkben az Azure PowerShell egy folyamatos parancsfájl, amely másolása, beillesztése és futtatása, telepítéséről [Azure Application Gateway PowerShell-minták](powershell-samples.md).

## <a name="prerequisites"></a>Előfeltételek

### <a name="azure-powershell-module"></a>Azure PowerShell-modul

Ha a helyi telepítése és használata az Azure PowerShell választja, a szkriptnek szüksége van az Azure PowerShell modul 2.1.0-ás vagy újabb.

1. A verzió megkereséséhez futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket.
2. Kapcsolat létrehozása az Azure-ral, futtassa `Connect-AzAccount`.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="example-script"></a>Példaszkript

### <a name="set-up-variables"></a>Változók beállítása

```azurepowershell
$rgname = "CustomRulesTest"

$location = "East US"

$appgwName = "WAFCustomRules"
```

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

```azurepowershell
$resourceGroup = New-AzResourceGroup -Name $rgname -Location $location
```

### <a name="create-a-vnet"></a>Virtuális hálózat létrehozása

```azurepowershell
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -AddressPrefix "10.0.0.0/24"

$sub2 = New-AzVirtualNetworkSubnetConfig -Name "backendSubnet" -AddressPrefix "10.0.1.0/24"

$vnet = New-AzvirtualNetwork -Name "Vnet1" -ResourceGroupName $rgname -Location $location `
  -AddressPrefix "10.0.0.0/16" -Subnet @($sub1, $sub2)
```

### <a name="create-a-static-public-vip"></a>Hozzon létre egy statikus nyilvános virtuális IP-cím

```azurepowershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $rgname -name "AppGwIP" `
  -location $location -AllocationMethod Static -Sku Standard
```

### <a name="create-pool-and-frontend-port"></a>Hozzon létre a készletet és az előtérbeli port

```azurepowershell
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -VirtualNetwork $vnet

$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "AppGwIpConfig" -Subnet $gwSubnet

$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "fipconfig" -PublicIPAddress $publicip

$pool = New-AzApplicationGatewayBackendAddressPool -Name "pool1" `
  -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com

$fp01 = New-AzApplicationGatewayFrontendPort -Name "port1" -Port 80
```

### <a name="create-a-listener-http-setting-rule-and-autoscale"></a>Hozzon létre egy figyelőt, http-beállítás, a szabály és az automatikus méretezés

```azurepowershell
$listener01 = New-AzApplicationGatewayHttpListener -Name "listener1" -Protocol Http `
  -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01

$poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name "setting1" -Port 80 `
  -Protocol Http -CookieBasedAffinity Disabled

$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType basic `
  -BackendHttpSettings $poolSetting01 -HttpListener $listener01 -BackendAddressPool $pool

$autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 3

$sku = New-AzApplicationGatewaySku -Name WAF_v2 -Tier WAF_v2
```

### <a name="create-the-custom-rule-and-apply-it-to-waf-policy"></a>Az egyéni szabály létrehozásának és a alkalmazni a WAF-házirend

```azurepowershell
$variable = New-AzApplicationGatewayFirewallMatchVariable -VariableName RequestHeaders -Selector User-Agent

$condition = New-AzApplicationGatewayFirewallCondition -MatchVariable $variable -Operator Contains -MatchValue "evilbot" -Transform Lowercase -NegationCondition $False  

$rule = New-AzApplicationGatewayFirewallCustomRule -Name blockEvilBot -Priority 2 -RuleType MatchRule -MatchCondition $condition -Action Block

$wafPolicy = New-AzApplicationGatewayFirewallPolicy -Name wafPolicy -ResourceGroup $rgname -Location $location -CustomRule $rule

$wafConfig = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

### <a name="create-the-application-gateway"></a>Az Application Gateway létrehozása

```azurepowershell
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $rgname `
   -Location $location -BackendAddressPools $pool `
   -BackendHttpSettingsCollection  $poolSetting01 `
   -GatewayIpConfigurations $gipconfig -FrontendIpConfigurations $fipconfig01 `
   -FrontendPorts $fp01 -HttpListeners $listener01 `
   -RequestRoutingRules $rule01 -Sku $sku -AutoscaleConfiguration $autoscaleConfig `
   -WebApplicationFirewallConfig $wafConfig `
   -FirewallPolicy $wafPolicy
```

## <a name="next-steps"></a>További lépések

[További tudnivalók a webalkalmazási tűzfal](waf-overview.md)