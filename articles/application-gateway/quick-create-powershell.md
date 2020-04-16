---
title: 'Rövid útmutató: Közvetlen webes forgalom a PowerShell használatával'
titleSuffix: Azure Application Gateway
description: Megtudhatja, hogyan hozhat létre az Azure PowerShell segítségével egy Olyan Azure Application Gateway-t, amely a webes forgalmat egy háttérkészletben lévő virtuális gépekre irányítja.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 04/15/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 3e1ca14d967b0e88ea7eb559fd9962a3824ff9b0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406213"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway-using-azure-powershell"></a>Rövid útmutató: Közvetlen webes forgalom az Azure PowerShell használatával az Azure Application Gateway használatával

Ebben a rövid útmutatóban az Azure PowerShell használatával hozzon létre egy alkalmazásátjárót. Ezután tesztelje, hogy megfelelően működik-e. 

Az alkalmazásátjáró az alkalmazás webforgalmát egy háttérkészlet ben lévő erőforrásokhoz irányítja. A figyelők portokhoz rendelhető, szabályokat hozhat létre, és erőforrásokat adhat hozzá egy háttérkészlethez. Az egyszerűség kedvéért ez a cikk egy egyszerű beállítást használ egy nyilvános előtér-IP-cím, egy alapszintű figyelő egyetlen hely üzemeltetéséhez az alkalmazásátjárón, egy alapvető kérelem-útválasztási szabályt és két virtuális gépet a háttérkészletben.

Ezt a rövid útmutatót az [Azure CLI](quick-create-cli.md) vagy az Azure Portal használatával is elvégezheti. [Azure portal](quick-create-portal.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

- Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Az Azure PowerShell 1.0.0-s vagy újabb verziója](/powershell/azure/install-az-ps) (ha az Azure PowerShellt helyileg futtatja).

## <a name="connect-to-azure"></a>Csatlakozás az Azure szolgáltatáshoz

Az Azure-ral `Connect-AzAccount`való kapcsolatfelvételhez futtassa a futtassa a futtassa a futtassa

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-ban kapcsolódó erőforrásokat oszt meg egy erőforráscsoporthoz. Használhat egy meglévő erőforráscsoportot, vagy létrehozhat egy újat.

Új erőforráscsoport létrehozásához használja `New-AzResourceGroup` a parancsmabot: 

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```
## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása

Ahhoz, hogy az Azure kommunikáljon a létrehozott erőforrások között, virtuális hálózatra van szüksége.  Az alkalmazásátjáró alhálózata csak alkalmazásátjárókat tartalmazhat. Más erőforrások nem engedélyezettek.  Létrehozhat egy új alhálózatot az Application Gateway számára, vagy használhat egy meglévőt. Ebben a példában két alhálózatot hoz létre ebben a példában: egyet az alkalmazásátjáróhoz, egyet pedig a háttérkiszolgálókhoz. Az alkalmazásátjáró előtér-IP-címét beállíthatja nyilvánosnak vagy privátnak a használati esetnek hasonlóan. Ebben a példában egy nyilvános előtér-IP-címet választ.

1. Hozza létre az alhálózati konfigurációkat a használatával. `New-AzVirtualNetworkSubnetConfig`
2. Hozza létre a virtuális hálózatot `New-AzVirtualNetwork`az alhálózati konfigurációkkal a használatával. 
3. Hozza létre a `New-AzPublicIpAddress`nyilvános IP-címet a használatával. 

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
  -AllocationMethod Static `
  -Sku Standard
```
## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

### <a name="create-the-ip-configurations-and-frontend-port"></a>Az IP-konfigurációk és az előtérbeli port létrehozása

1. A `New-AzApplicationGatewayIPConfiguration` létrehozott alhálózatot az alkalmazásátjáróhoz társító konfiguráció létrehozásához használható. 
2. A `New-AzApplicationGatewayFrontendIPConfig` korábban létrehozott nyilvános IP-címet az alkalmazásátjáróhoz rendelt konfiguráció létrehozásához használhatja. 
3. A `New-AzApplicationGatewayFrontendPort` 80-as port hozzárendelése az alkalmazásátjáró eléréséhez.

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

1. Az `New-AzApplicationGatewayBackendAddressPool` alkalmazásátjáró háttérkészletének létrehozásához használható. A háttérkészlet egyelőre üres lesz. Amikor a következő szakaszban létrehozza a háttérkiszolgáló hálózati adaptereit, hozzáadja őket a háttérkészlethez.
2. Adja meg a háttérkészlet `New-AzApplicationGatewayBackendHttpSetting`beállításait a alkalmazással.

```azurepowershell-interactive
$backendPool = New-AzApplicationGatewayBackendAddressPool `
  -Name myAGBackendPool
$poolSettings = New-AzApplicationGatewayBackendHttpSetting `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 30
```

### <a name="create-the-listener-and-add-a-rule"></a>Figyelő létrehozása és szabály hozzáadása

Az Azure-nak szüksége van egy figyelőre, hogy engedélyezze az alkalmazásátjáró t a háttérkészlet megfelelő útválasztási forgalomhoz. Az Azure-nak szüksége van egy szabályra is, hogy a figyelő tudja, hogy melyik háttérkészletet használja a bejövő forgalomhoz. 

1. Hozzon létre `New-AzApplicationGatewayHttpListener` egy figyelőt az előtér-konfigurációval és a korábban létrehozott előtér-porttal. 
2. Szabály1 nevű szabály `New-AzApplicationGatewayRequestRoutingRule` *létrehozásához*használható. 

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

Most, hogy létrehozta a szükséges támogató erőforrásokat, hozza létre az alkalmazásátjárót:

1. Az `New-AzApplicationGatewaySku` alkalmazásátjáró paramétereinek megadására használható.
2. Az `New-AzApplicationGateway` alkalmazásátjáró létrehozásához használható.

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_v2 `
  -Tier Standard_v2 `
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

### <a name="backend-servers"></a>Háttérkiszolgálók

Most, hogy létrehozta az Application Gateway, hozza létre a háttérvirtuális virtuális gépek, amelyek a webhelyek et üzemelteti. Háttérkapcsolat ic-kből, virtuálisgép-méretezési csoportokból, nyilvános IP-címeket, belső IP-címeket, teljesen minősített tartományneveket (FQDN) és több-bérlős háttérrendszerekből, például az Azure App Service-ből állhat. Ebben a példában két virtuális gépet hoz létre az Azure számára, amelyek az alkalmazásátjáró háttérkiszolgálóiként használhatók. Az IIS-t a virtuális gépekre is telepíti annak ellenőrzéséhez, hogy az Azure sikeresen létrehozta-e az alkalmazásátjárót.

#### <a name="create-two-virtual-machines"></a>Két virtuális gép létrehozása

1. Az alkalmazásátjáró nemrég létrehozott háttérkészletének `Get-AzApplicationGatewayBackendAddressPool`konfigurációjának beszereznie a segítségével.
2. Hozzon létre `New-AzNetworkInterface`egy hálózati adaptert a segítségével.
3. Hozzon létre egy `New-AzVMConfig`virtuális gép konfigurációját a segítségével.
4. Hozza létre a `New-AzVM`virtuális gépet a segítségével.

Amikor futtatja a következő kódmintát a virtuális gépek létrehozásához, az Azure hitelesítő adatokat kér. Adja meg az *azureuser* nevet és a jelszót:
    
```azurepowershell-interactive
$appgw = Get-AzApplicationGateway -ResourceGroupName myResourceGroupAG -Name myAppGateway
$backendPool = Get-AzApplicationGatewayBackendAddressPool -Name myAGBackendPool -ApplicationGateway $appgw
$vnet   = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name myBackendSubnet
$cred = Get-Credential
for ($i=1; $i -le 2; $i++)
{
  $nic = New-AzNetworkInterface `
    -Name myNic$i `
    -ResourceGroupName myResourceGroupAG `
    -Location EastUS `
    -Subnet $subnet `
    -ApplicationGatewayBackendAddressPool $backendpool
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
  Set-AzVMBootDiagnostic `
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

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

Bár az IIS nem szükséges az alkalmazásátjáró létrehozásához, ebben a rövid útmutatóban telepítette azt annak ellenőrzésére, hogy az Azure sikeresen létrehozta-e az alkalmazásátjárót. Az alkalmazásátjáró teszteléséhez használja az IIS-t:

1. Futtassa `Get-AzPublicIPAddress` az alkalmazásátjáró nyilvános IP-címét. 
2. Másolja és illessze be a nyilvános IP-címet a böngésző címsorába. A böngésző frissítésekor látnia kell a virtuális gép nevét. Egy érvényes válasz ellenőrzi, hogy az alkalmazásátjáró sikeresen létrejött-e, és sikeresen csatlakozhat a háttérrendszerhez.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Az alkalmazásátjáró tesztelése](./media/quick-create-powershell/application-gateway-iistest.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az alkalmazásátjáróval létrehozott erőforrásokra, törölje az erőforráscsoportot. Az erőforráscsoport törlésekor az alkalmazásátjárót és annak összes kapcsolódó erőforrását is törli. 

Az erőforráscsoport törléséhez `Remove-AzResourceGroup` hívja meg a parancsmast:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Webes forgalom kezelése alkalmazásátjáróval az Azure PowerShell használatával](./tutorial-manage-web-traffic-powershell.md)

