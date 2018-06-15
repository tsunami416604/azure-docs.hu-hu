---
title: Rövid útmutató – Webes forgalom irányítása az Azure Application Gatewayjel – Azure PowerShell | Microsoft Docs
description: Megismerheti, hogyan hozhat létre az Azure PowerShell-lel egy olyan Azure Application Gatewayt, amely a háttérkészletben lévő virtuális gépekhez irányítja a webes forgalmat.
services: application-gateway
author: vhorne
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: azurepowershell
ms.topic: quickstart
ms.workload: infrastructure-services
ms.date: 01/25/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 95b806eaabaf0ba93b1e8b6823340e82842b0f1c
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2018
ms.locfileid: "33202048"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-powershell"></a>Rövid útmutató: Webes forgalom irányítása az Azure Application Gatewayjel – Azure PowerShell

Az Azure Application Gateway segítségével a figyelők portokhoz rendelésével, szabályok létrehozásával és az erőforrások háttérkészlethez adásával adott erőforrások felé irányíthatja alkalmazásai webes forgalmát.

Ez rövid útmutató bemutatja, hogyan hozhat létre gyorsan az Azure Portallal egy olyan alkalmazásátjárót, amelynek háttérkészletében két virtuális gép található. Ezután tesztelheti, hogy megfelelően működik-e.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az Azure PowerShell-modul 3.6-os vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az erőforráscsoportokban mindig létre kell hoznia erőforrásokat. Hozzon létre egy Azure-erőforráscsoportot a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) parancsmaggal. 

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása 

Ahhoz, hogy az alkalmazásátjáró kommunikálhasson más erőforrásokkal, létre kell hoznia egy virtuális hálózatot. Ebben a példában két alhálózatot hozunk létre: egyet az alkalmazásátjáróhoz, egy másikat pedig a háttérkiszolgálókhoz. 

Hozza létre az alhálózatkonfigurációkat a [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) parancsmaggal. Hozza létre a virtuális hálózatot a [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) parancsmag és az alhálózat-konfigurációk használatával. Végül hozza létre a nyilvános IP-címet a [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) parancsmaggal.

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

Ebben a példában két virtuális gépet hozunk létre, amelyeket az alkalmazásátjáró háttérkiszolgálóiként fogunk használni. 

A virtuális gépeken emellett telepíti az IIS-t annak ellenőrzéséhez, hogy az alkalmazásátjáró sikeresen létrejött-e.

### <a name="create-two-virtual-machines"></a>Két virtuális gép létrehozása

Hozzon létre egy hálózati adaptert a [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) parancsmaggal. Hozzon létre egy virtuálisgép-konfigurációt a [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) parancsmaggal. A következő parancsok futtatásakor a rendszer a hitelesítő adatok megadására kéri. Adja meg az *azureuser* felhasználónevet és az *Azure123456!* jelszót. Hozza létre a virtuális gépet a [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) parancsmaggal.

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

### <a name="create-the-ip-configurations-and-frontend-port"></a>Az IP-konfigurációk és az előtérbeli port létrehozása

A [New-AzureRmApplicationGatewayIPConfiguration](/powershell/module/azurerm.network/new-azurermapplicationgatewayipconfiguration) parancsmaggal hozza létre azt a konfigurációt, amely a korábban létrehozott alhálózatot az alkalmazásátjáróhoz társítja. A [New-AzureRmApplicationGatewayFrontendIPConfig](/powershell/module/azurerm.network/new-azurermapplicationgatewayfrontendipconfig) parancsmaggal hozza létre azt a konfigurációt, amely a korábban szintén létrehozott nyilvános IP-címet az alkalmazásátjáróhoz rendeli. A [New-AzureRmApplicationGatewayFrontendPort](/powershell/module/azurerm.network/new-azurermapplicationgatewayfrontendport) parancsmaggal rendelje hozzá az alkalmazásátjáró eléréséhez használt 80-as portot.

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

### <a name="create-the-backend-pool"></a>A háttérkészlet létrehozása

A [New-AzureRmApplicationGatewayBackendAddressPool](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendaddresspool) parancsmaggal hozza létre a háttérkészletet az alkalmazásátjáróhoz. Konfigurálja a háttérkészlet beállításait a [New-AzureRmApplicationGatewayBackendHttpSettings](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendhttpsettings) parancsmaggal.

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

### <a name="create-the-listener-and-add-a-rule"></a>Figyelő létrehozása és szabály hozzáadása

A figyelő ahhoz szükséges, hogy az alkalmazásátjáró megfelelően irányíthassa a forgalmat a háttérkészlethez. Hozzon létre egy figyelőt a [New-AzureRmApplicationGatewayHttpListener](/powershell/module/azurerm.network/new-azurermapplicationgatewayhttplistener) parancsmaggal, a korábban létrehozott előtér-konfigurációval és előtérbeli porttal. A szabály ahhoz szükséges, hogy a figyelő tudja, melyik háttérkészletet használja a bejövő forgalomhoz. A [New-AzureRmApplicationGatewayRequestRoutingRule](/powershell/module/azurerm.network/new-azurermapplicationgatewayrequestroutingrule) parancsmaggal hozzon létre egy *rule1* nevű szabályt.

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

Miután létrehozta a szükséges támogató erőforrásokat, a [New-AzureRmApplicationGatewaySku](/powershell/module/azurerm.network/new-azurermapplicationgatewaysku) parancsmaggal adja meg az alkalmazásátjáró paramétereit, majd a [New-AzureRmApplicationGateway](/powershell/module/azurerm.network/new-azurermapplicationgateway) parancsmaggal hozza létre az átjárót.

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

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

Az IIS telepítése nem szükséges az alkalmazásátjáró létrehozásához. Ebben a rövid útmutatóban azért telepítettük, hogy ellenőrizhessük, sikeres volt-e az alkalmazásátjáró létrehozása. A [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) parancsmaggal kérje le az alkalmazásátjáró nyilvános IP-címét. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába.

```azurepowershell-interactive
Get-AzureRmPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Az alkalmazásátjáró tesztelése](./media/quick-create-powershell/application-gateway-iistest.png)

Amikor frissíti a böngészőt, megjelenik a másik virtuális gép neve.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Tekintse át az alkalmazásátjáróval együtt létrehozott erőforrásokat, és ha már nincs rájuk szükség, a [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) parancsmaggal eltávolíthatja az erőforráscsoportot, az alkalmazásátjárót és az összes kapcsolódó erőforrást.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Webes forgalom kezelése alkalmazásátjáróval az Azure PowerShell használatával](./tutorial-manage-web-traffic-powershell.md)

