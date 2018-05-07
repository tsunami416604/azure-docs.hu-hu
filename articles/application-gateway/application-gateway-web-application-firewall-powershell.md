---
title: Hozzon létre egy alkalmazást a webalkalmazási tűzfal - Azure PowerShell |} Microsoft Docs
description: Megtudhatja, hogyan hozzon létre egy alkalmazás webalkalmazási tűzfalakat Azure PowerShell használatával.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/25/2018
ms.author: victorh
ms.openlocfilehash: 23ef085159030bef03c71f21d546739a64cffb59
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2018
---
# <a name="create-an-application-gateway-with-a-web-application-firewall-using-azure-powershell"></a>Hozzon létre egy alkalmazást az Azure PowerShell webalkalmazási tűzfal

Azure PowerShell segítségével hozzon létre egy [Alkalmazásátjáró](application-gateway-introduction.md) rendelkező egy [webalkalmazási tűzfal](application-gateway-web-application-firewall-overview.md) (waf-ot), amely használ egy [virtuálisgép-méretezési csoport](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) háttérkiszolgálókhoz. A WAF használatát [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) szabályokat, hogy az alkalmazás védelme. Ezek a szabályok közé tartoznak például az SQL-injektálás támadások, a többhelyes parancsfájlok futtatására és a munkamenet kihasználásának elleni védelem. 

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * A hálózat beállítása
> * Hozzon létre egy alkalmazás WAF engedélyezve
> * Virtuálisgép-méretezési csoport létrehozása
> * Hozzon létre egy tárfiókot, és diagnosztika konfigurálása

![Webes alkalmazás tűzfal – példa](./media/application-gateway-web-application-firewall-powershell/scenario-waf.png)

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az Azure PowerShell-modul 3.6-os vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: ` Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Hozzon létre egy Azure-erőforrás csoport használatával [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup).  

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása 

Nevű alhálózat-konfigurációk létrehozása *myBackendSubnet* és *myAGSubnet* használatával [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). Hozzon létre a virtuális hálózat nevű *myVNet* használatával [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) az alhálózati konfigurációjú. És végezetül hozza létre a nyilvános IP-cím nevű *myAGPublicIPAddress* használatával [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress). Ezeket az erőforrásokat segítségével adja meg a hálózati kapcsolat az Alkalmazásátjáró és a kapcsolódó erőforrások.

```azurepowershell-interactive
$backendSubnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24
$agSubnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.2.0/24
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $backendSubnetConfig, $agSubnetConfig
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Dynamic
```

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

### <a name="create-the-ip-configurations-and-frontend-port"></a>Az IP-konfigurációk és elülső rétegbeli portot létrehozása

Társítsa *myAGSubnet* átjáró használatával korábban létrehozott [New-AzureRmApplicationGatewayIPConfiguration](/powershell/module/azurerm.network/new-azurermapplicationgatewayipconfiguration). Rendelje hozzá *myAGPublicIPAddress* átjáró használatával [New-AzureRmApplicationGatewayFrontendIPConfig](/powershell/module/azurerm.network/new-azurermapplicationgatewayfrontendipconfig).

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet
$subnet=$vnet.Subnets[0]
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip
$frontendport = New-AzureRmApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 80
```

### <a name="create-the-backend-pool-and-settings"></a>A háttérkészlet és a beállítások létrehozása

Hozzon létre nevű háttérkészlet *appGatewayBackendPool* átjáró használatára vonatkozó [New-AzureRmApplicationGatewayBackendAddressPool](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendaddresspool). Adja meg a háttér-címkészletek használatával beállításait [New-AzureRmApplicationGatewayBackendHttpSettings](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendhttpsettings).

```azurepowershell-interactive
$defaultPool = New-AzureRmApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool 
$poolSettings = New-AzureRmApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-default-listener-and-rule"></a>Az alapértelmezett figyelő és szabály létrehozása

Egy figyelő szükséges ahhoz, hogy a forgalom megfelelő irányításához a háttér-címkészletek az Alkalmazásátjáró. Ebben a példában hozzon létre egy alapszintű figyelő, amely figyeli a forgalmat a gyökér URL-címen. 

Hozzon létre egy figyelőt nevű *mydefaultListener* használatával [New-AzureRmApplicationGatewayHttpListener](/powershell/module/azurerm.network/new-azurermapplicationgatewayhttplistener) előtérbeli konfigurációja és elülső rétegbeli portot, amelyet korábban hozott létre. A szabály a figyelőt, hogy tudja, melyik háttérkészlet, a bejövő forgalmat használandó szükség. Hozzon létre egy egyszerű szabályt nevű *Szabály1* használatával [New-AzureRmApplicationGatewayRequestRoutingRule](/powershell/module/azurerm.network/new-azurermapplicationgatewayrequestroutingrule).

```azurepowershell-interactive
$defaultlistener = New-AzureRmApplicationGatewayHttpListener `
  -Name mydefaultListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport
$frontendRule = New-AzureRmApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway-with-the-waf"></a>Az Alkalmazásátjáró létrehozása a WAF

Most, hogy létrehozta a szükséges támogató erőforrásokat, adja meg az átjáró használatára vonatkozó paramétert [New-AzureRmApplicationGatewaySku](/powershell/module/azurerm.network/new-azurermapplicationgatewaysku). Adja meg a használt WAF konfiguráció [New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration](/powershell/module/azurerm.network/new-azurermapplicationgatewaywebapplicationfirewallconfiguration). Majd hozza létre az Alkalmazásátjáró nevű *myAppGateway* használatával [New-AzureRmApplicationGateway](/powershell/module/azurerm.network/new-azurermapplicationgateway).

```azurepowershell-interactive
$sku = New-AzureRmApplicationGatewaySku `
  -Name WAF_Medium `
  -Tier WAF `
  -Capacity 2
$wafConfig = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration `
  -Enabled $true `
  -FirewallMode "Detection"
$appgw = New-AzureRmApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $defaultPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultlistener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku `
  -WebApplicationFirewallConfig $wafConfig
```

## <a name="create-a-virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport létrehozása

Ebben a példában hozzon létre egy virtuálisgép-méretezési kiszolgálók biztosít az Alkalmazásátjáró a háttérkészlet beállítása. A méretezési készletben a háttérkészletbe, amikor konfigurálja az IP-beállításokat rendel.

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet
$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$backendPool = Get-AzureRmApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool `
  -ApplicationGateway $appgw
$ipConfig = New-AzureRmVmssIpConfig `
  -Name myVmssIPConfig `
  -SubnetId $vnet.Subnets[1].Id `
  -ApplicationGatewayBackendAddressPoolsId $backendPool.Id
$vmssConfig = New-AzureRmVmssConfig `
  -Location eastus `
  -SkuCapacity 2 `
  -SkuName Standard_DS2 `
  -UpgradePolicyMode Automatic
Set-AzureRmVmssStorageProfile $vmssConfig `
  -ImageReferencePublisher MicrosoftWindowsServer `
  -ImageReferenceOffer WindowsServer `
  -ImageReferenceSku 2016-Datacenter `
  -ImageReferenceVersion latest
Set-AzureRmVmssOsProfile $vmssConfig `
  -AdminUsername azureuser `
  -AdminPassword "Azure123456!" `
  -ComputerNamePrefix myvmss
Add-AzureRmVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name myVmssNetConfig `
  -Primary $true `
  -IPConfiguration $ipConfig
New-AzureRmVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmssConfig
```

### <a name="install-iis"></a>Az IIS telepítése

```azurepowershell-interactive
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/vhorne/samplescripts/master/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
$vmss = Get-AzureRmVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings
Update-AzureRmVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmss
```

## <a name="create-a-storage-account-and-configure-diagnostics"></a>Hozzon létre egy tárfiókot, és diagnosztika konfigurálása

Ebben az oktatóanyagban az Alkalmazásátjáró tárfiók adatok használ felderítésére és megelőzésére célokra. Log Analytics vagy az Eseményközpont kiválasztásával adatok rögzítéséhez is használhatja.

### <a name="create-the-storage-account"></a>A storage-fiók létrehozása

Hozzon létre egy tárfiókot, nevű *myagstore1* használatával [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount).

```azurepowershell-interactive
$storageAccount = New-AzureRmStorageAccount `
  -ResourceGroupName myResourceGroupAG `
  -Name myagstore1 `
  -Location eastus `
  -SkuName "Standard_LRS"
```

### <a name="configure-diagnostics"></a>Diagnosztika konfigurálása

Azokat a ApplicationGatewayAccessLog ApplicationGatewayPerformanceLog, erőforrásrekord-adatokat a diagnosztika konfigurálása és ApplicationGatewayFirewallLog naplók segítségével [Set-AzureRmDiagnosticSetting](/powershell/module/azurerm.insights/set-azurermdiagnosticsetting).

```azurepowershell-interactive
$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$store = Get-AzureRmStorageAccount `
  -ResourceGroupName myResourceGroupAG `
  -Name myagstore1
Set-AzureRmDiagnosticSetting `
  -ResourceId $appgw.Id `
  -StorageAccountId $store.Id `
  -Categories ApplicationGatewayAccessLog, ApplicationGatewayPerformanceLog, ApplicationGatewayFirewallLog `
  -Enabled $true `
  -RetentionEnabled $true `
  -RetentionInDays 30
```

## <a name="test-the-application-gateway"></a>Az Alkalmazásátjáró tesztelése

Használhat [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) lekérni az alkalmazás átjáró nyilvános IP-címét. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába.

```azurepowershell-interactive
Get-AzureRmPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Az alkalmazás átjáró alap URL-cím tesztelése](./media/application-gateway-web-application-firewall-powershell/application-gateway-iistest.png)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A hálózat beállítása
> * Hozzon létre egy alkalmazás WAF engedélyezve
> * Virtuálisgép-méretezési csoport létrehozása
> * Hozzon létre egy tárfiókot, és diagnosztika konfigurálása

További információt a alkalmazásátjárót és a kapcsolódó erőforrások, továbbra is a útmutatókat.
