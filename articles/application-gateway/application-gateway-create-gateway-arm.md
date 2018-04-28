---
title: Hozzon létre egy alkalmazás - Azure PowerShell |} Microsoft Docs
description: Megtudhatja, hogyan hozhat létre olyan átjárót Azure PowerShell használatával.
services: application-gateway
author: davidmu1
manager: timlt
editor: ''
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: azurepowershell
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/25/2018
ms.author: davidmu
ms.openlocfilehash: d0956a4e7e6f9cd6a1b075efc2b87c27d3660233
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="create-an-application-gateway-using-azure-powershell"></a>Az Azure PowerShell Alkalmazásátjáró létrehozása

Azure PowerShell használatával létrehozása vagy kezelése alkalmazásátjárót, a parancssorban vagy parancsfájlokban. A gyors üzembe helyezés bemutatja, hogyan hozzon létre a hálózati erőforrásokhoz, a háttérkiszolgálók, illetve az Alkalmazásátjáró.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az Azure PowerShell-modul 3.6-os vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa `Get-Module -ListAvailable AzureRM` . Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy Azure-erőforrás csoport használatával [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása 

Az alhálózati beállítások használatával hozzon létre [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). Hozzon létre a virtuális hálózat használatával [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) és az alhálózati beállítások. És végezetül hozza létre a nyilvános IP cím használatával [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress). Ezeket az erőforrásokat segítségével adja meg a hálózati kapcsolat az Alkalmazásátjáró és a kapcsolódó erőforrások.

```azurepowershell-interactive
$backendSubnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.1.0/24
$agSubnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.2.0/24
New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $backendSubnetConfig, $agSubnetConfig
New-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Dynamic
```
## <a name="create-backend-servers"></a>Háttérkiszolgálók létrehozása

Ebben a példában két virtuális gép az Alkalmazásátjáró háttér-kiszolgálóként használandó hoz létre. Is telepíteni az IIS ellenőrizze, hogy az Alkalmazásátjáró sikeresen létrejött-e a virtuális gépeken.

### <a name="create-two-virtual-machines"></a>Két virtuális gép létrehozása

Hozzon létre egy hálózati illesztő – [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface). Hozzon létre egy virtuálisgép-konfiguráció [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig). A következő parancsok futtatásakor kéri a hitelesítő adatokat. Adja meg *azureuser* a felhasználónév és *Azure123456!* a jelszó. Hozza létre a virtuális gépet a [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) parancsmaggal.

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$cred = Get-Credential
for ($i=1; $i -le 2; $i++)
{
  $nic = New-AzureRmNetworkInterface `
    -Name myNic$i `
    -ResourceGroupName myResourceGroupAG `
    -Location EastUS `
    -SubnetId $vnet.Subnets[1].Id
  $vm = New-AzureRmVMConfig `
    -VMName myVM$i `
    -VMSize Standard_DS2
  $vm = Set-AzureRmVMOperatingSystem `
    -VM $vm `
    -Windows `
    -ComputerName myVM$i `
    -Credential $cred
  $vm = Set-AzureRmVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
  $vm = Add-AzureRmVMNetworkInterface `
    -VM $vm `
    -Id $nic.Id
  $vm = Set-AzureRmVMBootDiagnostics `
    -VM $vm `
    -Disable
  New-AzureRmVM -ResourceGroupName myResourceGroupAG -Location EastUS -VM $vm
  Set-AzureRmVMExtension `
    -ResourceGroupName myResourceGroupAG `
    -ExtensionName IIS `
    -VMName myVM$i `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
    -Location EastUS
}
```

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

### <a name="create-the-ip-configurations-and-frontend-port"></a>Az IP-konfigurációk és elülső rétegbeli portot létrehozása

Használjon [New-AzureRmApplicationGatewayIPConfiguration](/powershell/module/azurerm.network/new-azurermapplicationgatewayipconfiguration) az, hogy a korábban létrehozott alkalmazás átjáró alhálózati konfiguráció létrehozásához. Használjon [New-AzureRmApplicationGatewayFrontendIPConfig](/powershell/module/azurerm.network/new-azurermapplicationgatewayfrontendipconfig) a konfiguráció, amely a nyilvános IP-cím is korábban létrehozott rendel az Alkalmazásátjáró létrehozásához. Használjon [New-AzureRmApplicationGatewayFrontendPort](/powershell/module/azurerm.network/new-azurermapplicationgatewayfrontendport) rendelhető hozzá lehet férni az Alkalmazásátjáró 80-as porton.

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$pip = Get-AzureRmPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress 
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

### <a name="create-the-backend-pool"></a>A háttér-készlet létrehozása

Használjon [New-AzureRmApplicationGatewayBackendAddressPool](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendaddresspool) az alkalmazás átjáró háttérkészlet létrehozásához. Adja meg a beállításokat a háttér címkészletet használatára vonatkozó [New-AzureRmApplicationGatewayBackendHttpSettings](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendhttpsettings).

```azurepowershell-interactive
$address1 = Get-AzureRmNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic1
$address2 = Get-AzureRmNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic2
$backendPool = New-AzureRmApplicationGatewayBackendAddressPool `
  -Name myAGBackendPool `
  -BackendIPAddresses $address1.ipconfigurations[0].privateipaddress, $address2.ipconfigurations[0].privateipaddress
$poolSettings = New-AzureRmApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-listener-and-add-a-rule"></a>Hozzon létre a figyelő és szabály hozzáadása

Egy figyelő szükséges ahhoz, hogy a forgalom megfelelő irányításához a háttérkészletbe az Alkalmazásátjáró. Hozzon létre egy figyelőt használatával [New-AzureRmApplicationGatewayHttpListener](/powershell/module/azurerm.network/new-azurermapplicationgatewayhttplistener) előtérbeli konfigurációja és elülső rétegbeli portot, amelyet korábban hozott létre. A szabály a figyelőt, hogy tudja, melyik háttérkészlet, a bejövő forgalmat használandó szükség. Használjon [New-AzureRmApplicationGatewayRequestRoutingRule](/powershell/module/azurerm.network/new-azurermapplicationgatewayrequestroutingrule) nevű szabály létrehozásához *Szabály1*.

```azurepowershell-interactive
$defaultlistener = New-AzureRmApplicationGatewayHttpListener `
  -Name myAGListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport
$frontendRule = New-AzureRmApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $backendPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>Application Gateway létrehozása

Most, hogy létrehozta a szükséges támogató erőforrásokat, [New-AzureRmApplicationGatewaySku](/powershell/module/azurerm.network/new-azurermapplicationgatewaysku) meg az Alkalmazásátjáró paramétereit, és ezután [New-AzureRmApplicationGateway](/powershell/module/azurerm.network/new-azurermapplicationgateway) számára hozza létre.

```azurepowershell-interactive
$sku = New-AzureRmApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
  -Capacity 2
New-AzureRmApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $backendPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultlistener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku
```

## <a name="test-the-application-gateway"></a>Az Alkalmazásátjáró tesztelése

Használjon [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) lekérni az alkalmazás átjáró nyilvános IP-címét. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába.

```azurepowershell-interactive
Get-AzureRmPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Alkalmazásátjáró tesztelése](./media/application-gateway-create-gateway-arm/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége, használhatja a [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) távolítsa el az erőforráscsoportot, Alkalmazásátjáró parancsot, és az összes kapcsolódó erőforrásokat.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy erőforráscsoportot, hálózati erőforrásokat és háttérkiszolgálókat hozott létre. Alkalmazásátjáró létrehozásához használt erőforrások majd. További információt a alkalmazásátjárót és a kapcsolódó erőforrások, továbbra is a útmutatókat.

