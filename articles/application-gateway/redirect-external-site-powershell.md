---
title: Külső átirányítás a PowerShell használatával
titleSuffix: Azure Application Gateway
description: Ismerje meg, hogyan hozhat létre egy alkalmazásátjárót, amely átirányítja a webes forgalmat egy külső webhelyre az Azure Powershell használatával.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 6596cdb2df0a916c49086f80466db60b02a81467
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74047760"
---
# <a name="create-an-application-gateway-with-external-redirection-using-azure-powershell"></a>Alkalmazásátjáró létrehozása külső átirányítással az Azure PowerShell használatával

Az Azure Powershell segítségével konfigurálhatja [a webes forgalom átirányítását,](multiple-site-overview.md) amikor létrehoz egy [alkalmazásátjárót.](overview.md) Ebben az oktatóanyagban konfigurálegy figyelőt, és szabály, amely átirányítja a webes forgalmat, amely megérkezik az alkalmazás átjáróegy külső webhelyre.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * A hálózat beállítása
> * Figyelő- és átirányítási szabály létrehozása
> * Application Gateway létrehozása

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha úgy dönt, hogy helyileg telepíti és használja a PowerShellt, ez az oktatóanyag az Azure PowerShell-modul 1.0.0-s vagy újabb verzióját igényli. A verzió megkereséséhez futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Hozzon létre egy Azure-erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)használatával.  

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása

Hozza létre a *myAGSubnet* alhálózati konfigurációt a [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig)használatával. Hozza létre a *myVNet* nevű virtuális hálózatot a [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) használatával az alhálózati konfigurációval. És végül hozza létre a nyilvános [IP-címet a New-AzPublicIpAddress használatával.](/powershell/module/az.network/new-azpublicipaddress) Ezek az erőforrások biztosítják az alkalmazásátjáró és a hozzá kapcsolódó erőforrások hálózati kapcsolatát.

```azurepowershell-interactive
$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.1.0/24
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $agSubnetConfig
$pip = New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Dynamic
```

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

### <a name="create-the-ip-configurations-and-frontend-port"></a>Az IP-konfigurációk és az előtérbeli port létrehozása

Társítsa a korábban létrehozott *myAGSubnet-et* az alkalmazásátjáróhoz a [New-AzApplicationGatewayIPConfiguration használatával.](/powershell/module/az.network/new-azapplicationgatewayipconfiguration) Rendelje hozzá a nyilvános IP-címet az alkalmazásátjáróhoz a [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig)használatával. Ezután létrehozhatja a HTTP-portot a [New-AzApplicationGatewayFrontendPort használatával.](/powershell/module/az.network/new-azapplicationgatewayfrontendport)

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet
$subnet=$vnet.Subnets[0]
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

### <a name="create-the-backend-pool-and-settings"></a>A háttérkészlet létrehozása és beállítása

Hozza létre az *alkalmazásátjáró alapértelmezett készletének* nevű háttérkészletét a [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool)használatával. Adja meg a készlet beállításait a [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting)használatával.

```azurepowershell-interactive
$defaultPool = New-AzApplicationGatewayBackendAddressPool `
  -Name defaultPool 
$poolSettings = New-AzApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-listener-and-rule"></a>A figyelő és a szabály létrehozása

A figyelő szükséges, hogy az alkalmazásátjáró megfelelően irányítsa a forgalmat. Hozza létre a figyelőt a [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) használatával a korábban létrehozott előtér-konfigurációval és előtér-porttal. A szabály szükséges a figyelő, hogy tudja, hová küldje a bejövő forgalmat. Hozzon létre egy *redirectRule* nevű alapszabályt a [New-AzApplicationGatewayRequestRoutingRule használatával.](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule)

```azurepowershell-interactive
$defaultListener = New-AzApplicationGatewayHttpListener `
  -Name defaultListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport
$redirectConfig = New-AzApplicationGatewayRedirectConfiguration `
  -Name myredirect `
  -RedirectType Temporary `
  -TargetUrl "https://bing.com"
$redirectRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name redirectRule `
  -RuleType Basic `
  -HttpListener $defaultListener `
  -RedirectConfiguration $redirectConfig
```

### <a name="create-the-application-gateway"></a>Application Gateway létrehozása

Most, hogy létrehozta a szükséges támogató erőforrásokat, adja meg a *myAppGateway* nevű alkalmazásátjáró paramétereit a [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku)használatával, majd hozza létre a [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway)használatával.

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
  -Capacity 2
$appgw = New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $defaultPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultListener `
  -RequestRoutingRules $redirectRule `
  -RedirectConfigurations $redirectConfig `
  -Sku $sku
```

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

A [Get-AzPublicIPAddress használatával](/powershell/module/az.network/get-azpublicipaddress) lejuthat az alkalmazásátjáró nyilvános IP-címével. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

Látnia kell *bing.com* jelenik meg a böngészőben.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan:

> [!div class="checklist"]
> * A hálózat beállítása
> * Figyelő- és átirányítási szabály létrehozása
> * Application Gateway létrehozása
