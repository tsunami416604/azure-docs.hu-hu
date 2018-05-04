---
title: A HTTP, HTTPS átirányítás - Azure PowerShell hozhat létre olyan átjárót |} Microsoft Docs
description: Megtudhatja, hogyan hozzon létre egy alkalmazás átirányított forgalmat a HTTP és HTTPS Azure PowerShell használatával.
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/23/2018
ms.author: davidmu
ms.openlocfilehash: 01e22ba6e15b526602e9c07800bdcb422984bbff
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-azure-powershell"></a>HTTP, HTTPS átirányítása Azure PowerShell használatával hozhat létre olyan átjárót

Az Azure PowerShell segítségével hozzon létre egy [Alkalmazásátjáró](overview.md) egy SSL-lezárást tanúsítványával. Útválasztási szabályainak használt HTTP-forgalom átirányítása a HTTPS-portot az Alkalmazásátjáró. Ebben a példában is létrehozhat egy [virtuálisgép-méretezési csoport](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) az Alkalmazásátjáró két virtuálisgép-példányok tartalmazó háttérkészlet számára. 

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Önaláírt tanúsítvány létrehozása
> * A hálózat beállítása
> * Hozzon létre egy alkalmazást a tanúsítvány
> * Figyelő és átirányítási szabály hozzáadása
> * Hozzon létre egy virtuálisgép-méretezési állítható be alapértelmezett háttérkészlet

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

Az oktatóanyaghoz az Azure PowerShell-modul 3.6-os vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ebben az oktatóanyagban a parancsok futtatásához is futtatásához szükséges `Login-AzureRmAccount` az Azure VPN-kapcsolat létrehozásához.

## <a name="create-a-self-signed-certificate"></a>Önaláírt tanúsítvány létrehozása

Az éles környezetben való használathoz importálnia kell egy megbízható szolgáltató által aláírt érvényes tanúsítványt. Ebben az oktatóanyagban létrehoz egy önaláírt tanúsítványt használó [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate). Használhat [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate) által eredményül adott pfx-fájlba exportálhatja a tanúsítványt az ujjlenyomattal.

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

Ez az eredmény hasonlót kell megjelennie:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

A pfx-fájl létrehozásához használja az ujjlenyomat:

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456!" -Force -AsPlainText
Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Hozzon létre egy Azure erőforráscsoport nevű *myResourceGroupAG* használatával [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). 

```powershell
New-AzureRmResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása

Az alhálózat-konfigurációk létrehozása *myBackendSubnet* és *myAGSubnet* használatával [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). Hozzon létre a virtuális hálózat nevű *myVNet* használatával [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) az alhálózati konfigurációjú. És végezetül hozza létre a nyilvános IP-cím nevű *myAGPublicIPAddress* használatával [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress). Ezeket az erőforrásokat segítségével adja meg a hálózati kapcsolat az Alkalmazásátjáró és a kapcsolódó erőforrások.

```powershell
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

Társítsa *myAGSubnet* átjáró használatával korábban létrehozott [New-AzureRmApplicationGatewayIPConfiguration](/powershell/module/azurerm.network/new-azurermapplicationgatewayipconfiguration). Rendelje hozzá *myAGPublicIPAddress* átjáró használatával [New-AzureRmApplicationGatewayFrontendIPConfig](/powershell/module/azurerm.network/new-azurermapplicationgatewayfrontendipconfig). Majd hozhat létre a HTTPS port és [New-AzureRmApplicationGatewayFrontendPort](/powershell/module/azurerm.network/new-azurermapplicationgatewayfrontendport).

```powershell
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
$frontendPort = New-AzureRmApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 443
```

### <a name="create-the-backend-pool-and-settings"></a>A háttérkészlet és a beállítások létrehozása

Hozzon létre nevű háttérkészlet *appGatewayBackendPool* átjáró használatára vonatkozó [New-AzureRmApplicationGatewayBackendAddressPool](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendaddresspool). Adja meg a beállításokat a háttér címkészletet használatára vonatkozó [New-AzureRmApplicationGatewayBackendHttpSettings](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendhttpsettings).

```powershell
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

Egy figyelő szükséges ahhoz, hogy a forgalom megfelelő irányításához a háttérkészletbe az Alkalmazásátjáró. Ebben a példában hozzon létre egy alapszintű figyelő, amely figyeli a HTTPS-forgalmat a gyökér URL-címen. 

Hozzon létre egy tanúsítvány objektum használatával [New-AzureRmApplicationGatewaySslCertificate](/powershell/module/azurerm.network/new-azurermapplicationgatewaysslcertificate) majd létre szeretne hozni egy nevű figyelő *appGatewayHttpListener* használatával [ Új AzureRmApplicationGatewayHttpListener](/powershell/module/azurerm.network/new-azurermapplicationgatewayhttplistener) a előtérbeli konfigurációja, a elülső rétegbeli portot és a korábban létrehozott tanúsítványt. A szabály a figyelőt, hogy tudja, melyik háttérkészlet, a bejövő forgalmat használandó szükség. Hozzon létre egy egyszerű szabályt nevű *Szabály1* használatával [New-AzureRmApplicationGatewayRequestRoutingRule](/powershell/module/azurerm.network/new-azurermapplicationgatewayrequestroutingrule).

```powershell
$pwd = ConvertTo-SecureString `
  -String "Azure123456!" `
  -Force `
  -AsPlainText
$cert = New-AzureRmApplicationGatewaySslCertificate `
  -Name "appgwcert" `
  -CertificateFile "c:\appgwcert.pfx" `
  -Password $pwd
$defaultListener = New-AzureRmApplicationGatewayHttpListener `
  -Name appGatewayHttpListener `
  -Protocol Https `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendPort `
  -SslCertificate $cert
$frontendRule = New-AzureRmApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultListener `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>Application Gateway létrehozása

Most, hogy létrehozta a szükséges támogató erőforrásokat, adja meg az Alkalmazásátjáró nevű paramétereinek *myAppGateway* használatával [New-AzureRmApplicationGatewaySku](/powershell/module/azurerm.network/new-azurermapplicationgatewaysku), majd létre szeretne hozni, használatával[ Új AzureRmApplicationGateway](/powershell/module/azurerm.network/new-azurermapplicationgateway) a tanúsítványhoz.

```powershell
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
  -FrontendPorts $frontendPort `
  -HttpListeners $defaultListener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku `
  -SslCertificates $cert
```

## <a name="add-a-listener-and-redirection-rule"></a>Figyelő és átirányítási szabály hozzáadása

### <a name="add-the-http-port"></a>Adja hozzá a HTTP-port

A HTTP-port hozzáadása az átjáró használó [Add-AzureRmApplicationGatewayFrontendPort](/powershell/module/azurerm.network/add-azurermapplicationgatewayfrontendport).

```powershell
$appgw = Get-AzureRmApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG
Add-AzureRmApplicationGatewayFrontendPort `
  -Name httpPort  `
  -Port 80 `
  -ApplicationGateway $appgw
```

### <a name="add-the-http-listener"></a>Adja hozzá a HTTP-figyelő

Adja hozzá a HTTP-figyelő nevű *myListener* átjáró használatával [Add-AzureRmApplicationGatewayHttpListener](/powershell/module/azurerm.network/add-azurermapplicationgatewayhttplistener).

```powershell
$fipconfig = Get-AzureRmApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -ApplicationGateway $appgw
$fp = Get-AzureRmApplicationGatewayFrontendPort `
  -Name httpPort `
  -ApplicationGateway $appgw
Add-AzureRmApplicationGatewayHttpListener `
  -Name myListener `
  -Protocol Http `
  -FrontendPort $fp `
  -FrontendIPConfiguration $fipconfig `
  -ApplicationGateway $appgw
```

### <a name="add-the-redirection-configuration"></a>Az átirányítás konfigurációjának hozzáadása

Adja hozzá a HTTP HTTPS átirányítás konfigurációját, és az átjárót használó [Add-AzureRmApplicationGatewayRedirectConfiguration](/powershell/module/azurerm.network/add-azurermapplicationgatewayredirectconfiguration).

```powershell
$defaultListener = Get-AzureRmApplicationGatewayHttpListener `
  -Name appGatewayHttpListener `
  -ApplicationGateway $appgw
Add-AzureRmApplicationGatewayRedirectConfiguration -Name httpToHttps `
  -RedirectType Permanent `
  -TargetListener $defaultListener `
  -IncludePath $true `
  -IncludeQueryString $true `
  -ApplicationGateway $appgw
```

### <a name="add-the-routing-rule"></a>Az útválasztási szabály hozzáadása

A átirányítása konfigurációjú-útválasztási szabály hozzáadása az átjáró használatával [Add-AzureRmApplicationGatewayRequestRoutingRule](/powershell/module/azurerm.network/add-azurermapplicationgatewayrequestroutingrule).

```powershell
$myListener = Get-AzureRmApplicationGatewayHttpListener `
  -Name myListener `
  -ApplicationGateway $appgw
$redirectConfig = Get-AzureRmApplicationGatewayRedirectConfiguration `
  -Name httpToHttps `
  -ApplicationGateway $appgw
Add-AzureRmApplicationGatewayRequestRoutingRule `
  -Name rule2 `
  -RuleType Basic `
  -HttpListener $myListener `
  -RedirectConfiguration $redirectConfig `
  -ApplicationGateway $appgw
Set-AzureRmApplicationGateway -ApplicationGateway $appgw
```

## <a name="create-a-virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport létrehozása

Ebben a példában hozzon létre egy virtuálisgép-méretezési kiszolgálók biztosít az Alkalmazásátjáró a háttérkészlet beállítása. A méretezési készletben a háttérkészletbe, amikor konfigurálja az IP-beállításokat rendel.

```powershell
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

```powershell
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/davidmu1/samplescripts/master/appgatewayurl.ps1"); 
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

## <a name="test-the-application-gateway"></a>Az Alkalmazásátjáró tesztelése

Használhat [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) lekérni az alkalmazás átjáró nyilvános IP-címét. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába. Például: http://52.170.203.149

```powershell
Get-AzureRmPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Biztonságos figyelmeztetés](./media/redirect-http-to-https-powershell/application-gateway-secure.png)

Válassza ki a biztonsági figyelmeztetést, ha egy önaláírt tanúsítványt használt elfogadásához **részletek** , majd **nyissa meg a képernyőn látható weblapon**. Ekkor a biztonságos IIS-webhely a következő példához hasonlóan jelenik meg:

![Az alkalmazás átjáró alap URL-cím tesztelése](./media/redirect-http-to-https-powershell/application-gateway-iistest.png)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Önaláírt tanúsítvány létrehozása
> * A hálózat beállítása
> * Hozzon létre egy alkalmazást a tanúsítvány
> * Figyelő és átirányítási szabály hozzáadása
> * Hozzon létre egy virtuálisgép-méretezési állítható be alapértelmezett háttérkészlet
