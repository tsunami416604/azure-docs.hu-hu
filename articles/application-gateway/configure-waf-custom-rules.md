---
title: A webalkalmazási tűzfal v2 egyéni szabályainak konfigurálása Azure PowerShell használatával
description: Ismerje meg, hogyan konfigurálhatja a webalkalmazási tűzfal v2 egyéni szabályait a Azure PowerShell használatával
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 6/18/2019
ms.author: victorh
ms.openlocfilehash: 9e50b47e22f5760c213cd0cafad82ecca592dec8
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263745"
---
# <a name="configure-web-application-firewall-v2-custom-rules-by-using-azure-powershell"></a>A webalkalmazási tűzfal v2 egyéni szabályainak konfigurálása Azure PowerShell használatával

<!--- If you make any changes to the PowerShell in this article, also make the change in the corresponding Sample file: azure-docs-powershell-samples/application-gateway/waf-rules/waf-custom-rules.ps1 --->

Egyéni szabályokkal létrehozhatja saját szabályait, amelyeket a rendszer a webalkalmazási tűzfalon (WAF) áthaladó összes kérelem esetében kiértékel. Ezek a szabályok magasabb prioritással rendelkeznek, mint a felügyelt szabálykészlet többi szabálya. A teljes Testreszabás lehetővé tételéhez az egyéni szabályoknak van egy művelete (Engedélyezés vagy Letiltás), egy egyeztetési feltétel és egy operátor.

Ez a cikk egy egyéni szabályt használó Azure Application Gateway WAF v2-t hoz létre. Az egyéni szabály blokkolja a forgalmat, ha a kérelem fejléce felhasználói ügynök *evilbot*tartalmaz.

További példákért tekintse meg az [Egyéni webalkalmazási tűzfalszabályok létrehozását és használatát](create-custom-waf-rules.md)ismertető témakört.

Ha az ebben a cikkben szereplő Azure PowerShell kódot egy olyan folytonos parancsfájlban szeretné futtatni, amelyet másolhat, beilleszthet és futtathat, tekintse meg az [Azure Application Gateway PowerShell-minták](powershell-samples.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek
* [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

* Szüksége van egy Azure PowerShell modulra. Ha a Azure PowerShell helyi telepítését és használatát választja, akkor ehhez a parancsfájlhoz Azure PowerShell modul 2.1.0 vagy újabb verziójára van szükség. Tegye a következőket:

  1. A verzió megkereséséhez futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket.
  2. Az Azure-beli kapcsolatok létrehozásához futtassa a `Connect-AzAccount` parancsot.

## <a name="example-script"></a>Példaszkript

### <a name="set-up-variables"></a>Változók beállítása

Futtassa a következő kódot:

```azurepowershell
$rgname = "CustomRulesTest"

$location = "East US"

$appgwName = "WAFCustomRules"
```

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Futtassa a következő kódot:

```azurepowershell
$resourceGroup = New-AzResourceGroup -Name $rgname -Location $location
```

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Futtassa a következő kódot:

```azurepowershell
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -AddressPrefix "10.0.0.0/24"

$sub2 = New-AzVirtualNetworkSubnetConfig -Name "backendSubnet" -AddressPrefix "10.0.1.0/24"

$vnet = New-AzvirtualNetwork -Name "Vnet1" -ResourceGroupName $rgname -Location $location `
  -AddressPrefix "10.0.0.0/16" -Subnet @($sub1, $sub2)
```

### <a name="create-a-static-public-vip"></a>Statikus nyilvános VIP létrehozása

Futtassa a következő kódot:

```azurepowershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $rgname -name "AppGwIP" `
  -location $location -AllocationMethod Static -Sku Standard
```

### <a name="create-a-pool-and-front-end-port"></a>Készlet és előtér-port létrehozása

Futtassa a következő kódot:

```azurepowershell
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -VirtualNetwork $vnet

$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "AppGwIpConfig" -Subnet $gwSubnet

$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "fipconfig" -PublicIPAddress $publicip

$pool = New-AzApplicationGatewayBackendAddressPool -Name "pool1" `
  -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com

$fp01 = New-AzApplicationGatewayFrontendPort -Name "port1" -Port 80
```

### <a name="create-a-listener-http-setting-rule-and-autoscale"></a>Figyelő, HTTP-beállítás, szabály és az autoskálázás létrehozása

Futtassa a következő kódot:

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

### <a name="create-the-custom-rule-and-apply-it-to-waf-policy"></a>Az egyéni szabály létrehozása és alkalmazása a WAF szabályzatra

Futtassa a következő kódot:

```azurepowershell
$variable = New-AzApplicationGatewayFirewallMatchVariable -VariableName RequestHeaders -Selector User-Agent

$condition = New-AzApplicationGatewayFirewallCondition -MatchVariable $variable -Operator Contains -MatchValue "evilbot" -Transform Lowercase -NegationCondition $False  

$rule = New-AzApplicationGatewayFirewallCustomRule -Name blockEvilBot -Priority 2 -RuleType MatchRule -MatchCondition $condition -Action Block

$wafPolicy = New-AzApplicationGatewayFirewallPolicy -Name wafPolicy -ResourceGroup $rgname -Location $location -CustomRule $rule

$wafConfig = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

### <a name="create-an-application-gateway"></a>Alkalmazásátjáró létrehozása

Futtassa a következő kódot:

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

## <a name="next-steps"></a>Következő lépések

[További információ a webalkalmazási tűzfalról](waf-overview.md)
