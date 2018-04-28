---
title: Hozzon létre egy alkalmazás URL-cím elérési út-alapú útválasztási szabályokat - Azure PowerShell |} Microsoft Docs
description: Megtudhatja, hogyan URL-cím elérési út-alapú útválasztási szabályok létrehozása egy alkalmazás átjáró és a virtuális gép méretezési készletben, Azure PowerShell használatával.
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/26/2018
ms.author: davidmu
ms.openlocfilehash: 6e4e681c9a45e31b13165a2e2e8491fef9bf2fc8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="create-an-application-gateway-with-url-path-based-routing-rules-using-azure-powershell"></a>Hozzon létre egy alkalmazás URL-cím elérési út-alapú útválasztási szabályokat Azure PowerShell használatával

Azure PowerShell segítségével konfigurálhatja [URL-cím elérési út-alapú útválasztási szabályok](application-gateway-url-route-overview.md) létrehozásakor egy [Alkalmazásátjáró](application-gateway-introduction.md). Ebben az oktatóanyagban háttérkészletek használatával létrehozhat egy [virtuálisgép-méretezési csoport](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). Ezután hozzon létre útválasztási szabályokat, győződjön meg arról, hogy a webes forgalom érkezik a készletek a megfelelő kiszolgálókat.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * A hálózat beállítása
> * Hozzon létre egy alkalmazás URL-cím térkép
> * A háttérkészlet hozzon létre virtuálisgép-méretezési csoportok

![URL-cím útválasztási – példa](./media/application-gateway-create-url-route-arm-ps/scenario.png)

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az Azure PowerShell-modul 3.6-os vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa ` Get-Module -ListAvailable AzureRM` . Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Hozzon létre egy Azure-erőforrás csoport használatával [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup).  

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása

Az alhálózat-konfigurációk létrehozása *myAGSubnet* és *myBackendSubnet* használatával [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). Hozzon létre a virtuális hálózat nevű *myVNet* használatával [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) az alhálózati konfigurációjú. És végezetül hozza létre a nyilvános IP-cím nevű *myAGPublicIPAddress* használatával [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress). Ezeket az erőforrásokat segítségével adja meg a hálózati kapcsolat az Alkalmazásátjáró és a kapcsolódó erőforrások.

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

Társítsa *myAGSubnet* átjáró használatával korábban létrehozott [New-AzureRmApplicationGatewayIPConfiguration](/powershell/module/azurerm.network/new-azurermapplicationgatewayipconfiguration). Rendelje hozzá a *myAGPublicIPAddress* átjáró használatával [New-AzureRmApplicationGatewayFrontendIPConfig](/powershell/module/azurerm.network/new-azurermapplicationgatewayfrontendipconfig).

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet
$subnet=$vnet.Subnets[0]
$pip = Get-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Name myAGPublicIPAddress
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

### <a name="create-the-default-pool-and-settings"></a>Az alapértelmezett készletet és a beállítások létrehozása

Hozzon létre nevű alapértelmezett háttérkészlet *appGatewayBackendPool* átjáró használatára vonatkozó [New-AzureRmApplicationGatewayBackendAddressPool](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendaddresspool). Adja meg a beállításokat a háttér címkészletet használatára vonatkozó [New-AzureRmApplicationGatewayBackendHttpSettings](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendhttpsettings).

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

Egy figyelő szükséges ahhoz, hogy a forgalom megfelelő irányításához a háttérkészletbe az Alkalmazásátjáró. Ebben az oktatóanyagban létrehoz két figyelők. Az Ön által létrehozott első alapvető figyelő sem figyeli a forgalmat a gyökér URL-címen. A második figyelő az Ön által létrehozott figyeli az adott URL-címek-forgalmat.

Hozzon létre az alapértelmezett figyelőt nevű *myDefaultListener* használatával [New-AzureRmApplicationGatewayHttpListener](/powershell/module/azurerm.network/new-azurermapplicationgatewayhttplistener) előtérbeli konfigurációja és elülső rétegbeli portot, amelyet korábban hozott létre. A szabály a figyelőt, hogy tudja, melyik háttérkészlet, a bejövő forgalmat használandó szükség. Hozzon létre egy egyszerű szabályt nevű *Szabály1* használatával [New-AzureRmApplicationGatewayRequestRoutingRule](/powershell/module/azurerm.network/new-azurermapplicationgatewayrequestroutingrule).

```azurepowershell-interactive
$defaultlistener = New-AzureRmApplicationGatewayHttpListener `
  -Name myDefaultListener `
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

### <a name="create-the-application-gateway"></a>Application Gateway létrehozása

Most, hogy létrehozta a szükséges támogató erőforrásokat, adja meg az Alkalmazásátjáró nevű paramétereinek *myAppGateway* használatával [New-AzureRmApplicationGatewaySku](/powershell/module/azurerm.network/new-azurermapplicationgatewaysku), majd létre szeretne hozni, használatával[ Új AzureRmApplicationGateway](/powershell/module/azurerm.network/new-azurermapplicationgateway).

```azurepowershell-interactive
$sku = New-AzureRmApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
  -Capacity 2
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
  -Sku $sku
```

### <a name="add-image-and-video-backend-pools-and-port"></a>Adja hozzá a lemezkép és a videó háttérkészlet és port

Hozzáadhat nevű háttérkészletek *imagesBackendPool* és *videoBackendPool* az Alkalmazásátjáró használatával [Add-AzureRmApplicationGatewayBackendAddressPool](/powershell/module/azurerm.network/add-azurermapplicationgatewaybackendaddresspool). A készletek használatával elülső rétegbeli portot hozzáadhat [Add-AzureRmApplicationGatewayFrontendPort](/powershell/module/azurerm.network/add-azurermapplicationgatewayfrontendport). Ezután küldenie kell az átjárót használó módosításai [Set-AzureRmApplicationGateway](/powershell/module/azurerm.network/set-azurermapplicationgateway).

```azurepowershell-interactive
$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
Add-AzureRmApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name imagesBackendPool 
Add-AzureRmApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name videoBackendPool
Add-AzureRmApplicationGatewayFrontendPort `
  -ApplicationGateway $appgw `
  -Name bport `
  -Port 8080
Set-AzureRmApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-backend-listener"></a>Adja hozzá a háttér-figyelő

Adja hozzá a háttér-figyelő nevű *backendListener* irányíthatja a forgalmat használata szükséges, amely [Add-AzureRmApplicationGatewayHttpListener](/powershell/module/azurerm.network/add-azurermapplicationgatewayhttplistener).

```azurepowershell-interactive
$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$backendPort = Get-AzureRmApplicationGatewayFrontendPort `
  -ApplicationGateway $appgw `
  -Name bport
$fipconfig = Get-AzureRmApplicationGatewayFrontendIPConfig `
  -ApplicationGateway $appgw
Add-AzureRmApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name backendListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $backendPort
Set-AzureRmApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-url-path-map"></a>URL-cím elérési út-társítás hozzáadása

URL-cím elérési út maps győződjön meg arról, hogy adott URL-címek meghatározott háttérkészletek legyenek átirányítva. URL-cím elérési út maps nevű hozhat létre *imagePathRule* és *videoPathRule* használatával [New-AzureRmApplicationGatewayPathRuleConfig](/powershell/module/azurerm.network/new-azurermapplicationgatewaypathruleconfig) és [ Adja hozzá AzureRmApplicationGatewayUrlPathMapConfig](/powershell/module/azurerm.network/add-azurermapplicationgatewayurlpathmapconfig).

```azurepowershell-interactive
$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$poolSettings = Get-AzureRmApplicationGatewayBackendHttpSettings `
  -ApplicationGateway $appgw `
  -Name myPoolSettings
$imagePool = Get-AzureRmApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name imagesBackendPool
$videoPool = Get-AzureRmApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name videoBackendPool
$defaultPool = Get-AzureRmApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name appGatewayBackendPool
$imagePathRule = New-AzureRmApplicationGatewayPathRuleConfig `
  -Name imagePathRule `
  -Paths "/images/*" `
  -BackendAddressPool $imagePool `
  -BackendHttpSettings $poolSettings
$videoPathRule = New-AzureRmApplicationGatewayPathRuleConfig `
  -Name videoPathRule `
    -Paths "/video/*" `
    -BackendAddressPool $videoPool `
    -BackendHttpSettings $poolSettings
Add-AzureRmApplicationGatewayUrlPathMapConfig `
  -ApplicationGateway $appgw `
  -Name urlpathmap `
  -PathRules $imagePathRule, $videoPathRule `
  -DefaultBackendAddressPool $defaultPool `
  -DefaultBackendHttpSettings $poolSettings
Set-AzureRmApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-routing-rule"></a>-Útválasztási szabály hozzáadása

A szabály az URL-cím ábrázolási társítja a létrehozott figyelőt. Nevű szabályt is hozzáadhat **rule2* használatával [Add-AzureRmApplicationGatewayRequestRoutingRule](/powershell/module/azurerm.network/add-azurermapplicationgatewayrequestroutingrule).

```azurepowershell-interactive
$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$backendlistener = Get-AzureRmApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name backendListener
$urlPathMap = Get-AzureRmApplicationGatewayUrlPathMapConfig `
  -ApplicationGateway $appgw `
  -Name urlpathmap
Add-AzureRmApplicationGatewayRequestRoutingRule `
  -ApplicationGateway $appgw `
  -Name rule2 `
  -RuleType PathBasedRouting `
  -HttpListener $backendlistener `
  -UrlPathMap $urlPathMap
Set-AzureRmApplicationGateway -ApplicationGateway $appgw
```

## <a name="create-virtual-machine-scale-sets"></a>Hozzon létre virtuálisgép-méretezési csoportok

Ebben a példában hoz létre, amely támogatja a három háttérkészletek létrehozott három virtuális gép méretezési készlet. A méretezési csoportok az Ön által létrehozott megnevezett *myvmss1*, *myvmss2*, és *myvmss3*. Minden méretezési készlet két virtuálisgép-példány, amelyre telepíti az IIS tartalmaz. A méretezési készletben a háttérkészletbe, amikor konfigurálja az IP-beállításokat rendel.

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
$imagesPool = Get-AzureRmApplicationGatewayBackendAddressPool `
  -Name imagesBackendPool `
  -ApplicationGateway $appgw
$videoPool = Get-AzureRmApplicationGatewayBackendAddressPool `
  -Name videoBackendPool `
  -ApplicationGateway $appgw
for ($i=1; $i -le 3; $i++)
{
  if ($i -eq 1)
  {
     $poolId = $backendPool.Id
  }
  if ($i -eq 2) 
  {
    $poolId = $imagesPool.Id
  }
  if ($i -eq 3)
  {
    $poolId = $videoPool.Id
  }
  $ipConfig = New-AzureRmVmssIpConfig `
    -Name myVmssIPConfig$i `
    -SubnetId $vnet.Subnets[1].Id `
    -ApplicationGatewayBackendAddressPoolsId $poolId
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
    -ComputerNamePrefix myvmss$i
  Add-AzureRmVmssNetworkInterfaceConfiguration `
    -VirtualMachineScaleSet $vmssConfig `
    -Name myVmssNetConfig$i `
    -Primary $true `
    -IPConfiguration $ipConfig
  New-AzureRmVmss `
    -ResourceGroupName myResourceGroupAG `
    -Name myvmss$i `
    -VirtualMachineScaleSet $vmssConfig
}
```

### <a name="install-iis"></a>Az IIS telepítése

```azurepowershell-interactive
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/davidmu1/samplescripts/master/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }

for ($i=1; $i -le 3; $i++)
{
  $vmss = Get-AzureRmVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss$i
  Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $publicSettings

  Update-AzureRmVmss `
    -ResourceGroupName myResourceGroupAG `
    -Name myvmss$i `
    -VirtualMachineScaleSet $vmss
}
```

## <a name="test-the-application-gateway"></a>Az Alkalmazásátjáró tesztelése

Használhat [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) lekérni az alkalmazás átjáró nyilvános IP-címét. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába. Például a *http://52.168.55.24*, *http://52.168.55.24:8080/images/test.htm*, vagy *http://52.168.55.24:8080/video/test.htm*.

```azurepowershell-interactive
Get-AzureRmPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Az alkalmazás átjáró alap URL-cím tesztelése](./media/application-gateway-create-url-route-arm-ps/application-gateway-iistest.png)

Módosítsa az URL-cím http://<ip-address>:8080/video/test.htm, és a < ip-cím > IP-címet, és az alábbihoz hasonlót kell megjelennie:

![Az alkalmazás átjáró képek URL tesztelése](./media/application-gateway-create-url-route-arm-ps/application-gateway-iistest-images.png)

Módosítsa az URL-cím http://<ip-address>:8080/video/test.htm, és az alábbihoz hasonlót kell megjelennie:

![Az alkalmazás átjáró Videó URL tesztelése](./media/application-gateway-create-url-route-arm-ps/application-gateway-iistest-video.png)

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan:

> [!div class="checklist"]
> * A hálózat beállítása
> * Hozzon létre egy alkalmazás URL-cím térkép
> * A háttérkészlet hozzon létre virtuálisgép-méretezési csoportok

További információt a alkalmazásátjárót és a kapcsolódó erőforrások, továbbra is a útmutatókat.
