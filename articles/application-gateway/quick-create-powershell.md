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
ms.date: 12/26/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: dc6e24df0480a43b1195a7b327b499ffebac978a
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54079119"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-powershell"></a>Gyors útmutató: A közvetlen webes forgalom az Azure Application Gatewayjel – Azure PowerShell-lel

Ez a rövid útmutató bemutatja, hogyan használható az Azure Portalon hozhat létre gyorsan egy application gateway két virtuális gépet a háttérkészletben. Ezután tesztelheti, hogy megfelelően működik-e. Az Azure Application Gatewayjel, az alkalmazás webes forgalom meghatározott forrásokhoz való közvetlen: figyelői portok, szabályok létrehozásával és hozzárendelésével hozzáadunk erőforrásokat egy háttérkészlet.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

## <a name="run-azure-powershell-locally"></a>Azure PowerShell helyi futtatása

Ha a helyi telepítése és használata az Azure PowerShell választja, az oktatóanyaghoz az Azure PowerShell modul 3.6-os vagy újabb verziójára. 

1. A verzió megkereséséhez futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. 
2. Kapcsolat létrehozása az Azure-ral, futtassa `Login-AzureRmAccount`.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az Azure-ban a kapcsolódó erőforrásokat egy erőforráscsoportba foglalhat. Hozzon létre egy erőforráscsoportot a használatával a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) parancsmag az alábbiak szerint: 

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása 

Hozzon létre egy virtuális hálózatot, így az application gateway és más erőforrások közötti kommunikációhoz. Ebben a példában két alhálózattal jönnek létre: egyet az application gateway és a másik pedig a háttérkiszolgálókhoz. 

1. Hozzon létre az alhálózat-konfigurációit meghívásával [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig).
2. A virtuális hálózat létrehozása az alhálózat-konfigurációit meghívásával [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork).
3. A nyilvános IP-cím létrehozása meghívásával [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress).

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

Ebben a példában két virtuális gépet a háttérkiszolgálókhoz való használata az application gateway az Azure-hoz létre. Az IIS-et is telepíti, győződjön meg arról, hogy az Azure sikeresen létrejött-e az application gateway a virtuális gépeket.

### <a name="create-two-virtual-machines"></a>Két virtuális gép létrehozása

1. Hozzon létre egy hálózati adaptert a [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) parancsmaggal. 
2. Hozzon létre egy virtuálisgép-konfigurációt a [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) parancsmaggal.
3. Hozza létre a virtuális gépet a [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) parancsmaggal.

A virtuális gépek létrehozásához a következő mintakód futtatásakor az Azure hitelesítő adatokat kér. Adja meg az *azureuser* felhasználónevet és az *Azure123456!* a jelszó:
    
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
    -VMSize Standard_DS2_v2
  Set-AzureRmVMOperatingSystem `
    -VM $vm `
    -Windows `
    -ComputerName myVM$i `
    -Credential $cred
  Set-AzureRmVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
  Add-AzureRmVMNetworkInterface `
    -VM $vm `
    -Id $nic.Id
  Set-AzureRmVMBootDiagnostics `
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

1. Használat [New-AzureRmApplicationGatewayIPConfiguration](/powershell/module/azurerm.network/new-azurermapplicationgatewayipconfiguration) az application gateway segítségével létrehozott a konfigurációt, amely hozzárendeli az alhálózat létrehozásához. 
2. Használat [New-AzureRmApplicationGatewayFrontendIPConfig](/powershell/module/azurerm.network/new-azurermapplicationgatewayfrontendipconfig) a konfigurációt, amelyet korábban létrehozott nyilvános IP-címet rendel hozzá az application gateway létrehozásához. 
3. Használat [New-AzureRmApplicationGatewayFrontendPort](/powershell/module/azurerm.network/new-azurermapplicationgatewayfrontendport) hozzárendelése az application gateway eléréséhez a 80-as porton.

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

1. A [New-AzureRmApplicationGatewayBackendAddressPool](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendaddresspool) parancsmaggal hozza létre a háttérkészletet az alkalmazásátjáróhoz. 
2. Konfigurálja a háttérkészlet beállításait [New-AzureRmApplicationGatewayBackendHttpSettings](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendhttpsettings).

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

Azure van szüksége ahhoz, hogy az application gateway irányítaná a forgalmat a háttérkészlet megfelelő, egy figyelőt. Az Azure egy szabály a figyelőt, hogy tudja, melyik használja a bejövő forgalmat a háttérkészlet is szükséges. 

1. Hozzon létre egy figyelőt a [New-AzureRmApplicationGatewayHttpListener](/powershell/module/azurerm.network/new-azurermapplicationgatewayhttplistener) az előtér-konfigurációjához és az elülső rétegbeli portot, amelyet korábban hozott létre. 
2. A [New-AzureRmApplicationGatewayRequestRoutingRule](/powershell/module/azurerm.network/new-azurermapplicationgatewayrequestroutingrule) parancsmaggal hozzon létre egy *rule1* nevű szabályt. 

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

Most, hogy létrehozta a szükséges támogató erőforrások, az application gateway létrehozásához:

1. Használat [New-AzureRmApplicationGatewaySku](/powershell/module/azurerm.network/new-azurermapplicationgatewaysku) , adja meg a paramétereket az application gateway számára.
2. Használat [New-AzureRmApplicationGateway](/powershell/module/azurerm.network/new-azurermapplicationgateway) az application gateway létrehozásához.

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

Bár az application gateway létrehozásához az IIS nem szükséges, telepítette azt ebben a rövid, ellenőrizze, hogy az Azure sikeresen létrejött-e az application gateway. Az IIS használatával az application gateway teszteléséhez:

1. Futtatás [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) , az application Gateway nyilvános IP-címének lekéréséhez. 
2. Másolja és illessze be a nyilvános IP-címet a böngésző címsorába. Amikor frissíti a böngészőben, megtekintheti a virtuális gép nevét.

```azurepowershell-interactive
Get-AzureRmPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Az alkalmazásátjáró tesztelése](./media/quick-create-powershell/application-gateway-iistest.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az application gateway segítségével létrehozott erőforrásokat, távolítsa el az erőforráscsoportot. Az erőforráscsoport eltávolításával is eltávolítja az application gateway és az összes kapcsolódó erőforrás. 

Távolítsa el az erőforráscsoportot, hívja meg a [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) parancsmag az alábbiak szerint:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Webes forgalom kezelése alkalmazásátjáróval az Azure PowerShell használatával](./tutorial-manage-web-traffic-powershell.md)

