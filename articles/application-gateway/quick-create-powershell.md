---
title: Rövid útmutató – Webes forgalom irányítása az Azure Application Gatewayjel – Azure PowerShell | Microsoft Docs
description: Megismerheti, hogyan hozhat létre az Azure PowerShell-lel egy olyan Azure Application Gatewayt, amely a háttérkészletben lévő virtuális gépekhez irányítja a webes forgalmat.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 1/11/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: d41480def95137e1dc938c845f8cec1d59e26036
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/12/2019
ms.locfileid: "59521784"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-powershell"></a>Gyors útmutató: A közvetlen webes forgalom az Azure Application Gatewayjel – Azure PowerShell-lel

Ez a rövid útmutató bemutatja, hogyan hozhat létre gyorsan egy application gateway az Azure PowerShell használatával.  Az application gateway létrehozása után tesztelje, hogy ellenőrizze, hogy azok megfelelően működnek. Az Azure Application Gatewayjel az alkalmazás webes forgalom az erőforrásoknál figyelői hozzárendelése portokat, szabályok és hozzáadunk erőforrásokat egy háttérkészlet közvetlen. Az egyszerűség kedvéért a jelen cikk egy egyszerű beállítás nyilvános előtérbeli IP-cím, egy alapszintű figyelő gazdagéphez az application gateway-en egyetlen hely, a háttérkészlet és a egy egyszerű kérelem-útválasztási szabály használható két virtuális gépet használ.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

## <a name="prerequisites"></a>Előfeltételek

### <a name="azure-powershell-module"></a>Azure PowerShell-modul

Ha helyi telepítése és használata az Azure PowerShell választja, az oktatóanyaghoz az Azure PowerShell-modul verzióját 1.0.0 vagy újabb.

1. A verzió megkereséséhez futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. 
2. Kapcsolat létrehozása az Azure-ral, futtassa `Login-AzAccount`.

### <a name="resource-group"></a>Erőforráscsoport

Az Azure-ban a kapcsolódó erőforrásokat egy erőforráscsoportba foglalhat. Használjon egy meglévő erőforráscsoportot, vagy hozzon létre egy újat. Ebben a példában létrehozunk egy új erőforráscsoport használata a [New-AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup) parancsmag az alábbiak szerint: 

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

### <a name="required-network-resources"></a>Szükséges hálózati erőforrások

Az Azure-hoz az erőforrások közötti kommunikációt, hogy hozzon létre egy virtuális hálózat szükséges.  Az application gateway alhálózatának csak az application Gateway-átjárókon is tartalmazhat. Egyéb erőforrások nem engedélyezettek.  Hozzon létre egy új alhálózatot az Application Gateway számára, vagy használjon egy meglévőt. Ebben a példában két alhálózat ebben a példában hoz létre: egyet az application gateway, a másik pedig a háttérkiszolgálókhoz. Az előtérbeli IP-címét az Application Gateway nyilvános vagy privát kell a használati eset megfelelően konfigurálhatja. Ebben a példában választjuk ki egy nyilvános előtérbeli IP-címet.

1. Hozzon létre az alhálózat-konfigurációit meghívásával [New-AzVirtualNetworkSubnetConfig](/powershell/module/Az.network/new-Azvirtualnetworksubnetconfig).
2. A virtuális hálózat létrehozása az alhálózat-konfigurációit meghívásával [New-AzVirtualNetwork](/powershell/module/Az.network/new-Azvirtualnetwork). 
3. A nyilvános IP-cím létrehozása meghívásával [New-AzPublicIpAddress](/powershell/module/Az.network/new-Azpublicipaddress). 

```azurepowershell-interactive
$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.1.0/24
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.2.0/24
New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $agSubnetConfig, $backendSubnetConfig
New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Dynamic
```
### <a name="backend-servers"></a>Háttérkiszolgálók

Háttérbeli hálózati adapterek, a virtual machine scale sets, nyilvános IP-címek állhat, belső IP-címek, teljesen minősített neve (FQDN), és több-bérlős háttéralkalmazások hasonlóan az Azure App Service-ben. Ebben a példában két virtuális gépet a háttérkiszolgálókhoz való használata az application gateway az Azure-hoz létre. Az IIS-et is telepíti, győződjön meg arról, hogy az Azure sikeresen létrejött-e az application gateway a virtuális gépeket.

#### <a name="create-two-virtual-machines"></a>Két virtuális gép létrehozása

1. Hozzon létre egy hálózati adaptert a [New-AzNetworkInterface](/powershell/module/Az.network/new-Aznetworkinterface). 
2. Hozzon létre egy virtuálisgép-konfigurációt a [New-AzVMConfig](/powershell/module/Az.compute/new-Azvmconfig).
3. A virtuális gép létrehozása [New-azvm parancsmag](/powershell/module/Az.compute/new-Azvm).

A virtuális gépek létrehozásához a következő mintakód futtatásakor az Azure hitelesítő adatokat kér. Adja meg az *azureuser* felhasználónevet és az *Azure123456!* a jelszó:
    
```azurepowershell-interactive
$vnet   = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name myBackendSubnet
$cred = Get-Credential
for ($i=1; $i -le 2; $i++)
{
  $nic = New-AzNetworkInterface `
    -Name myNic$i `
    -ResourceGroupName myResourceGroupAG `
    -Location EastUS `
    -SubnetId $subnet.Id
  $vm = New-AzVMConfig `
    -VMName myVM$i `
    -VMSize Standard_DS2_v2
  Set-AzVMOperatingSystem `
    -VM $vm `
    -Windows `
    -ComputerName myVM$i `
    -Credential $cred
  Set-AzVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
  Add-AzVMNetworkInterface `
    -VM $vm `
    -Id $nic.Id
  Set-AzVMBootDiagnostics `
    -VM $vm `
    -Disable
  New-AzVM -ResourceGroupName myResourceGroupAG -Location EastUS -VM $vm
  Set-AzVMExtension `
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

1. Használat [New-AzApplicationGatewayIPConfiguration](/powershell/module/Az.network/new-Azapplicationgatewayipconfiguration) az application gateway segítségével létrehozott a konfigurációt, amely hozzárendeli az alhálózat létrehozásához. 
2. Használat [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/Az.network/new-Azapplicationgatewayfrontendipconfig) a konfigurációt, amelyet korábban létrehozott nyilvános IP-címet rendel hozzá az application gateway létrehozásához. 
3. Használat [New-AzApplicationGatewayFrontendPort](/powershell/module/Az.network/new-Azapplicationgatewayfrontendport) hozzárendelése az application gateway eléréséhez a 80-as porton.

```azurepowershell-interactive
$vnet   = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name myAGSubnet
$pip    = Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress 
$gipconfig = New-AzApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet
$fipconfig = New-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip
$frontendport = New-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 80
```

### <a name="create-the-backend-pool"></a>A háttérkészlet létrehozása

1. Használat [New-AzApplicationGatewayBackendAddressPool](/powershell/module/Az.network/new-Azapplicationgatewaybackendaddresspool) létrehozása az application Gateway háttérkészlet. 
2. Konfigurálja a háttérkészlet beállításait [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/Az.network/new-Azapplicationgatewaybackendhttpsettings).

```azurepowershell-interactive
$address1 = Get-AzNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic1
$address2 = Get-AzNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic2
$backendPool = New-AzApplicationGatewayBackendAddressPool `
  -Name myAGBackendPool `
  -BackendIPAddresses $address1.ipconfigurations[0].privateipaddress, $address2.ipconfigurations[0].privateipaddress
$poolSettings = New-AzApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-listener-and-add-a-rule"></a>Figyelő létrehozása és szabály hozzáadása

Azure van szüksége ahhoz, hogy az application gateway irányítaná a forgalmat a háttérkészlet megfelelő, egy figyelőt. Az Azure egy szabály a figyelőt, hogy tudja, melyik használja a bejövő forgalmat a háttérkészlet is szükséges. 

1. Hozzon létre egy figyelőt a [New-AzApplicationGatewayHttpListener](/powershell/module/Az.network/new-Azapplicationgatewayhttplistener) az előtér-konfigurációjához és az elülső rétegbeli portot, amelyet korábban hozott létre. 
2. Használat [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/Az.network/new-Azapplicationgatewayrequestroutingrule) nevű szabály létrehozásához *felhasználóval a rule1*. 

```azurepowershell-interactive
$defaultlistener = New-AzApplicationGatewayHttpListener `
  -Name myAGListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport
$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $backendPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>Application Gateway létrehozása

Most, hogy létrehozta a szükséges támogató erőforrások, az application gateway létrehozásához:

1. Használat [New-AzApplicationGatewaySku](/powershell/module/Az.network/new-Azapplicationgatewaysku) , adja meg a paramétereket az application gateway számára.
2. Használat [New-AzApplicationGateway](/powershell/module/Az.network/new-Azapplicationgateway) az application gateway létrehozásához.

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
  -Capacity 2
New-AzApplicationGateway `
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

1. Futtatás [Get-AzPublicIPAddress](/powershell/module/Az.network/get-Azpublicipaddress) , az application Gateway nyilvános IP-címének lekéréséhez. 
2. Másolja és illessze be a nyilvános IP-címet a böngésző címsorába. Amikor frissíti a böngészőben, megtekintheti a virtuális gép nevét. Érvényes válasz ellenőrzi, hogy az application gateway létrehozása sikeresen megtörtént, és képes sikeresen csatlakozott a háttérszolgáltatás használatára.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Az alkalmazásátjáró tesztelése](./media/quick-create-powershell/application-gateway-iistest.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az application gateway segítségével létrehozott erőforrásokat, távolítsa el az erőforráscsoportot. Az erőforráscsoport eltávolításával is eltávolítja az application gateway és az összes kapcsolódó erőforrás. 

Távolítsa el az erőforráscsoportot, hívja meg a [Remove-AzResourceGroup](/powershell/module/Az.resources/remove-Azresourcegroup) parancsmag az alábbiak szerint:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Webes forgalom kezelése alkalmazásátjáróval az Azure PowerShell használatával](./tutorial-manage-web-traffic-powershell.md)

