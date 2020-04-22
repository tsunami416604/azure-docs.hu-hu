---
title: Webhelyenkénti WAF-házirendek konfigurálása a PowerShell használatával
titleSuffix: Azure Web Application Firewall
description: Megtudhatja, hogyan konfigurálhatja a webhelyenkénti webalkalmazás-tűzfal házirendeket egy alkalmazásátjárón az Azure PowerShell használatával.
services: web-application-firewall
author: winthrop28
ms.service: web-application-firewall
ms.date: 01/24/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 1301db56cab36ae623bb94cfac97b8e4bdb934e5
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682478"
---
# <a name="configure-per-site-waf-policies-using-azure-powershell"></a>Webhelyenkénti WAF-házirendek konfigurálása az Azure PowerShell használatával

A WEBAlkalmazás-tűzfal (WAF) beállításai a WAF-házirendekben találhatók, és a WAF-konfiguráció módosításához módosítja a WAF-házirendet.

Az Application Gateway-hez társítva a szabályzatok és az összes beállítás globálisan tükröződnek. Tehát, ha öt webhely van a WAF mögött, mind az öt webhelyet ugyanaz a WAF-házirend védi. Ez nagyszerű, ha minden webhelyhez ugyanazokra a biztonsági beállításokra van szüksége. De waf-házirendeket is alkalmazhat az egyes figyelőkre, hogy lehetővé tegye a helyspecifikus WAF-konfigurációt.

Ha WAF-házirendeket alkalmaz egy figyelőre, konfigurálhatja az egyes webhelyek WAF-beállításait anélkül, hogy a módosítások minden helyet érintenek. A legkonkrétabb politika precedenst teremt. Ha van egy globális szabályzat, és egy telephelyenkénti házirend (a WAF-szabályzat társított figyelő), majd a per-site házirend felülbírálja a globális WAF-házirend, hogy a figyelő. Más hallgatók nélkül saját házirendcsak érinti a globális WAF politika.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * A hálózat beállítása
> * WAF-házirend létrehozása
> * Alkalmazásátjáró létrehozása engedélyezett WAF-fel
> * A WAF-házirend globális, webhelyenkénti és URI-nkénti alkalmazása
> * Virtuálisgép-méretezési csoport létrehozása
> * Tárfiók létrehozása és diagnosztika konfigurálása
> * Az alkalmazásátjáró tesztelése

![Példa webalkalmazási tűzfalra](../media/tutorial-restrict-web-traffic-powershell/scenario-waf.png)

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha úgy dönt, hogy helyileg telepíti és használja a PowerShellt, ez a cikk az Azure PowerShell-modul 1.0.0-s vagy újabb verzióját igényli. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, `Login-AzAccount` az Azure-ral való kapcsolat létrehozásához is futnia kell.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Hozzon létre egy Azure-erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)használatával.  

```azurepowershell-interactive
$rgname = New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása 

Hozza létre a *myBackendSubnet* és *a myAGSubnet* nevű alhálózati konfigurációkat a [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig)használatával. Hozza létre a *myVNet* nevű virtuális hálózatot a [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) használatával az alhálózati konfigurációkkal. És végül hozza létre a *myAGPublicIPAddress* nevű nyilvános IP-címet a [New-AzPublicIpAddress használatával.](/powershell/module/az.network/new-azpublicipaddress) Ezek az erőforrások biztosítják az alkalmazásátjáró és a hozzá kapcsolódó erőforrások hálózati kapcsolatát.

```azurepowershell-interactive
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24

$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.2.0/24

$vnet = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $backendSubnetConfig, $agSubnetConfig

$pip = New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Static `
  -Sku Standard
```

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

Ebben a szakaszban olyan erőforrásokat hoz létre, amelyek támogatják az alkalmazásátjárót, majd végül létrehoznak azt és egy WAF-ot. A következő erőforrásokat hozza létre:

- *IP-konfigurációk és az előtérbeli port* – A korábban létrehozott alhálózatot társítja az alkalmazásátjáróhoz, és hozzárendel egy portot, amelyen keresztül elérhető.
- *Alapértelmezett készlet* – Minden alkalmazásátjárónak rendelkeznie kell legalább egy háttérkiszolgáló-készlettel.
- *Alapértelmezett figyelő és szabály* – Az alapértelmezett figyelő figyeli a forgalmat a hozzárendelt porton, az alapértelmezett szabály pedig az alapértelmezett készletre irányítja a forgalmat.

### <a name="create-the-ip-configurations-and-frontend-port"></a>Az IP-konfigurációk és az előtérbeli port létrehozása

Társítsa a korábban létrehozott *myAGSubnet-et* az alkalmazásátjáróhoz a [New-AzApplicationGatewayIPConfiguration használatával.](/powershell/module/az.network/new-azapplicationgatewayipconfiguration) Rendelje hozzá *a myAGPublicIPAddress címet* az alkalmazásátjáróhoz a [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig)használatával.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet

$subnet=$vnet.Subnets[1]

$gipconfig = New-AzApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet

$fipconfig = New-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip

$frontendport80 = New-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 80
  
$frontendport8080 = New-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 8080
```

### <a name="create-the-backend-pool-and-settings"></a>A háttérkészlet létrehozása és beállítása

Hozza létre az *appGatewayBackendPool* nevű háttérkészletet az alkalmazásátjáróhoz a [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool)használatával. Adja meg a háttércímkészletek beállításait a [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting)használatával.

```azurepowershell-interactive
$defaultPool = New-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool 

$poolSettings = New-AzApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-two-waf-policies"></a>Két WAF-házirend létrehozása

Hozzon létre két WAF-házirendet, egy globális és egy globális és egy-egy webhelyet, és adjon hozzá egyéni szabályokat. 

A webhelyenkénti házirend a fájlfeltöltési korlátot 5 MB-ra korlátozza. Minden más ugyanaz.

```azurepowershell-interactive
$variable = New-AzApplicationGatewayFirewallMatchVariable -VariableName RequestUri
$condition = New-AzApplicationGatewayFirewallCondition -MatchVariable $variable -Operator Contains -MatchValue "globalAllow" 
$rule = New-AzApplicationGatewayFirewallCustomRule -Name globalAllow -Priority 5 -RuleType MatchRule -MatchCondition $condition -Action Allow

$variable1 = New-AzApplicationGatewayFirewallMatchVariable -VariableName RequestUri
$condition1 = New-AzApplicationGatewayFirewallCondition -MatchVariable $variable1 -Operator Contains -MatchValue "globalBlock" 
$rule1 = New-AzApplicationGatewayFirewallCustomRule -Name globalAllow -Priority 10 -RuleType MatchRule -MatchCondition $condition1 -Action Block

$variable2 = New-AzApplicationGatewayFirewallMatchVariable -VariableName RequestUri
$condition2 = New-AzApplicationGatewayFirewallCondition -MatchVariable $variable2 -Operator Contains -MatchValue "siteAllow" 
$rule2 = New-AzApplicationGatewayFirewallCustomRule -Name globalAllow -Priority 5 -RuleType MatchRule -MatchCondition $condition2 -Action Allow

$variable3 = New-AzApplicationGatewayFirewallMatchVariable -VariableName RequestUri
$condition3 = New-AzApplicationGatewayFirewallCondition -MatchVariable $variable3 -Operator Contains -MatchValue "siteBlock" 
$rule3 = New-AzApplicationGatewayFirewallCustomRule -Name globalAllow -Priority 10 -RuleType MatchRule -MatchCondition $condition3 -Action Block

$variable4 = New-AzApplicationGatewayFirewallMatchVariable -VariableName RequestUri
$condition4 = New-AzApplicationGatewayFirewallCondition -MatchVariable $variable4 -Operator Contains -MatchValue "URIAllow" 
$rule4 = New-AzApplicationGatewayFirewallCustomRule -Name globalAllow -Priority 5 -RuleType MatchRule -MatchCondition $condition4 -Action Allow

$variable5 = New-AzApplicationGatewayFirewallMatchVariable -VariableName RequestUri
$condition5 = New-AzApplicationGatewayFirewallCondition -MatchVariable $variable5 -Operator Contains -MatchValue "URIBlock" 
$rule5 = New-AzApplicationGatewayFirewallCustomRule -Name globalAllow -Priority 10 -RuleType MatchRule -MatchCondition $condition5 -Action Block

$policySettingGlobal = New-AzApplicationGatewayFirewallPolicySetting `
  -Mode Prevention `
  -State Enabled `
  -MaxRequestBodySizeInKb 100 `
  -MaxFileUploadInMb 256

$wafPolicyGlobal = New-AzApplicationGatewayFirewallPolicy `
  -Name wafpolicyGlobal `
  -ResourceGroup myResourceGroupAG `
  -Location eastus `
  -PolicySetting $PolicySettingGlobal `
  -CustomRule $rule, $rule1

$policySettingSite = New-AzApplicationGatewayFirewallPolicySetting `
  -Mode Prevention `
  -State Enabled `
  -MaxRequestBodySizeInKb 100 `
  -MaxFileUploadInMb 5

$wafPolicySite = New-AzApplicationGatewayFirewallPolicy `
  -Name wafpolicySite `
  -ResourceGroup myResourceGroupAG `
  -Location eastus `
  -PolicySetting $PolicySettingSite `
  -CustomRule $rule2, $rule3
```

### <a name="create-the-default-listener-and-rule"></a>Az alapértelmezett figyelő és szabály létrehozása

A figyelő ahhoz szükséges, hogy az alkalmazásátjáró megfelelően irányíthassa a forgalmat a háttércímkészletekhez. Ebben a példában egy alapszintű figyelőt hoz létre, amely a gyökér URL-cím forgalmát figyeli. 

Hozzon létre egy *mydefaultListener* nevű figyelőt a [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) használatával a korábban létrehozott előtér-konfigurációval és előtér-porttal. A szabály ahhoz szükséges, hogy a figyelő tudja, melyik háttérkészletet használja a bejövő forgalomhoz. Hozzon létre egy *1.* [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule)

```azurepowershell-interactive
$globalListener = New-AzApplicationGatewayHttpListener `
  -Name mydefaultListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport80

$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $globallistener `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings
  
$siteListener = New-AzApplicationGatewayHttpListener `
  -Name mydefaultListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport8080 `
  -FirewallPolicy $wafPolicySite
  
$frontendRuleSite = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule2 `
  -RuleType Basic `
  -HttpListener $siteListener `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway-with-the-waf"></a>Alkalmazásátjáró létrehozása a WAF-fel

Most, hogy létrehozta a szükséges támogató erőforrásokat, adja meg az alkalmazásátjáró paramétereit a [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku)használatával. Adja meg a tűzfalházirendet a [New-AzApplicationGatewayFirewallPolicy használatával.](/powershell/module/az.network/new-azapplicationgatewayfirewallpolicy) Ezután hozza létre a *myAppGateway* nevű alkalmazásátjárót a [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway)használatával.

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name WAF_v2 `
  -Tier WAF_v2 `
  -Capacity 2

$appgw = New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $defaultPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport80 `
  -HttpListeners $globallistener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku `
  -FirewallPolicy $wafPolicyGlobal
```

### <a name="apply-a-per-uri-policy"></a>URI-házirend alkalmazása

Uri-nkénti házirend alkalmazásához egyszerűen hozzon létre egy új házirendet, és alkalmazza azt az elérési útszabály konfigurációjára. 

```azurepowershell-interactive
$policySettingURI = New-AzApplicationGatewayFirewallPolicySetting `
  -Mode Prevention `
  -State Enabled `
  -MaxRequestBodySizeInKb 100 `
  -MaxFileUploadInMb 5

$wafPolicyURI = New-AzApplicationGatewayFirewallPolicy `
  -Name wafpolicySite `
  -ResourceGroup myResourceGroupAG `
  -Location eastus `
  -PolicySetting $PolicySettingURI `
  -CustomRule $rule4, $rule5

$Gateway = Get-AzApplicationGateway -Name "myAppGateway"

$PathRuleConfig = New-AzApplicationGatewayPathRuleConfig -Name "base" `
  -Paths "/base" `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings `
  -FirewallPolicy $wafPolicyURI

$PathRuleConfig1 = New-AzApplicationGatewayPathRuleConfig `
  -Name "base" -Paths "/test" `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings

$URLPathMap = New-AzApplicationGatewayUrlPathMapConfig -Name "PathMap" `
  -PathRules $PathRuleConfig, $PathRuleConfig1 `
  -DefaultBackendAddressPoolId $defaultPool.Id `
  -DefaultBackendHttpSettingsId $poolSettings.Id

Add-AzApplicationGatewayRequestRoutingRule -ApplicationGateway $AppGw `
  -Name "RequestRoutingRule" `
  -RuleType PathBasedRouting `
  -HttpListener $siteListener `
  -UrlPathMapId $URLPathMap.Id
```

## <a name="create-a-virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport létrehozása

Ebben a példában egy olyan virtuálisgép-méretezési csoportot hoz létre, amely kiszolgálókat biztosít a háttérkészlet számára az alkalmazásátjáróban. Az IP-beállítások konfigurálásakor hozzárendeli a méretezési csoportot a háttérkészlethez.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet

$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$backendPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name defaultPool `
  -ApplicationGateway $appgw

$ipConfig = New-AzVmssIpConfig `
  -Name myVmssIPConfig `
  -SubnetId $vnet.Subnets[1].Id `
  -ApplicationGatewayBackendAddressPoolsId $backendPool.Id

$vmssConfig = New-AzVmssConfig `
  -Location eastus `
  -SkuCapacity 2 `
  -SkuName Standard_DS2 `
  -UpgradePolicyMode Automatic

Set-AzVmssStorageProfile $vmssConfig `
  -ImageReferencePublisher MicrosoftWindowsServer `
  -ImageReferenceOffer WindowsServer `
  -ImageReferenceSku 2016-Datacenter `
  -ImageReferenceVersion latest `
  -OsDiskCreateOption FromImage

Set-AzVmssOsProfile $vmssConfig `
  -AdminUsername azureuser `
  -AdminPassword "Azure123456!" `
  -ComputerNamePrefix myvmss

Add-AzVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name myVmssNetConfig `
  -Primary $true `
  -IPConfiguration $ipConfig

New-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmssConfig
```

### <a name="install-iis"></a>Az IIS telepítése

```azurepowershell-interactive
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }

$vmss = Get-AzVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss

Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings

Update-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmss
```

## <a name="create-a-storage-account-and-configure-diagnostics"></a>Tárfiók létrehozása és diagnosztika konfigurálása

Ebben a cikkben az alkalmazásátjáró egy tárfiókot használ az adatok tárolására észlelési és megelőzési célokra. Az Azure Monitor naplóivagy az Event Hub használatával is rögzítheti az adatokat.

### <a name="create-the-storage-account"></a>A tárfiók létrehozása

Hozzon létre egy *myagstore1* nevű tárfiókot a [New-AzStorageAccount segítségével.](/powershell/module/az.storage/new-azstorageaccount)

```azurepowershell-interactive
$storageAccount = New-AzStorageAccount `
  -ResourceGroupName myResourceGroupAG `
  -Name myagstore1 `
  -Location eastus `
  -SkuName "Standard_LRS"
```

### <a name="configure-diagnostics"></a>Diagnosztika konfigurálása

Konfigurálja a diagnosztikát úgy, hogy adatokat rögzítsen az ApplicationGatewayAccessLog, AzApplicationGatewayPerformanceLog és ApplicationGatewayFirewallLog naplókba a [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting)segítségével.

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$store = Get-AzStorageAccount `
  -ResourceGroupName myResourceGroupAG `
  -Name myagstore1

Set-AzDiagnosticSetting `
  -ResourceId $appgw.Id `
  -StorageAccountId $store.Id `
  -Categories ApplicationGatewayAccessLog, ApplicationGatewayPerformanceLog, ApplicationGatewayFirewallLog `
  -Enabled $true `
  -RetentionEnabled $true `
  -RetentionInDays 30
```

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

A [Get-AzPublicIPAddress használatával](/powershell/module/az.network/get-azpublicipaddress) lejuthat az alkalmazásátjáró nyilvános IP-címével. Ezután használja ezt az IP-címet a göndörellen (cserélje ki az alábbi 1.1.1.1.-et). 

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress

#should be blocked
curl 1.1.1.1/globalBlock
curl 1.1.1.1/?1=1

#should be allowed
curl 1.1.1.1/globalAllow?1=1

#should be blocked
curl 1.1.1.1:8080/siteBlock
curl 1.1.1.1/?1=1

#should be allowed
curl 1.1.1.1:8080/siteAllow?1=1

#should be blocked
curl 1.1.1.1/URIBlock
curl 1.1.1.1/?1=1

#should be allowed
curl 1.1.1.1/URIAllow?1=1
```

![Az alap URL-cím tesztelése az alkalmazásátjáróban](../media/tutorial-restrict-web-traffic-powershell/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, távolítsa el az erőforráscsoportot, az alkalmazásátjárót és az összes kapcsolódó erőforrást az [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)segítségével.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>További lépések

[Webalkalmazási tűzfalszabályok testreszabása](application-gateway-customize-waf-rules-portal.md)
