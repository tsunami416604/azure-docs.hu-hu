---
title: Azure Application Gateway létrehozása & HTTP-fejlécek újraírása
description: Ez a cikk az Azure Application Gateway létrehozásáról és a HTTP-fejlécek Azure PowerShell használatával történő újraírásáról nyújt tájékoztatást.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/19/2019
ms.author: absha
ms.openlocfilehash: 2663c049245a7025b5948a64fc5008bb9e7dee90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74173715"
---
# <a name="create-an-application-gateway-and-rewrite-http-headers"></a>Alkalmazásátjáró létrehozása és HTTP-fejlécek újraírása

Az Azure PowerShell segítségével szabályokat állíthat be [a HTTP-kérelmek és válaszok fejléceinek újraírására](rewrite-http-headers.md) az új [automatikus skálázás és zónaredundáns alkalmazásátjáró termékváltozatának](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant) létrehozásakor

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
>
> * Automatikus skálázású virtuális hálózat létrehozása
> * Fenntartott nyilvános IP-cím létrehozása
> * Az alkalmazásátjáró-infrastruktúra beállítása
> * A http fejléc újraírási szabálykonfigurációjának megadása
> * Automatikus méretezés megadása
> * Application Gateway létrehozása
> * Az alkalmazásátjáró tesztelése

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk megköveteli, hogy az Azure PowerShell helyileg futtassa. Az Az modul 1.0.0-s vagy újabb verziójának telepítve kell lennie. Futtassa, `Import-Module Az` majd`Get-Module Az` keresse meg a verziót. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](https://docs.microsoft.com/powershell/azure/install-az-ps) ismertető cikket. A PowerShell-verzió ellenőrzése után futtassa az `Login-AzAccount` parancsot az Azure-hoz való kapcsolódáshoz.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása
Hozzon létre egy erőforráscsoportot az elérhető helyek egyikén.

```azurepowershell
$location = "East US 2"
$rg = "<rg name>"

#Create a new Resource Group
New-AzResourceGroup -Name $rg -Location $location
```

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy virtuális hálózatot egy dedikált alhálózattal egy automatikus skálázási alkalmazásátjáróhoz. Jelenleg az egyes dedikált alhálózatokon csak egy automatikus skálázású Application Gateway helyezhető üzembe.

```azurepowershell
#Create VNet with two subnets
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -AddressPrefix "10.0.0.0/24"
$sub2 = New-AzVirtualNetworkSubnetConfig -Name "BackendSubnet" -AddressPrefix "10.0.1.0/24"
$vnet = New-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg `
       -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $sub1, $sub2
```

## <a name="create-a-reserved-public-ip"></a>Fenntartott nyilvános IP-cím létrehozása

Adja meg a PublicIPAddress felosztási módját **statikusként.** Az automatikus skálázású Application Gateway virtuális IP-címe csak statikus lehet. A dinamikus IP-címek nem használhatók. Csak a standard PublicIPAddress termékváltozat támogatott.

```azurepowershell
#Create static public IP
$pip = New-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP" `
       -location $location -AllocationMethod Static -Sku Standard
```

## <a name="retrieve-details"></a>Részletek beolvasása

Az erőforráscsoport, az alhálózat és az IP egy helyi objektumban található adatok beolvasása az alkalmazásátjáró IP-konfigurációs részleteinek létrehozásához.

```azurepowershell
$resourceGroup = Get-AzResourceGroup -Name $rg
$publicip = Get-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP"
$vnet = Get-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -VirtualNetwork $vnet
```

## <a name="configure-the-infrastructure"></a>Az infrastruktúra konfigurálása

Konfigurálja az IP-konfigurációt, az előtér-IP-konfigurációt, a háttérkészletet, a HTTP-beállításokat, a tanúsítványt, a portot és a figyelőt a meglévő Standard alkalmazásátjáróval azonos formátumban. Az új termékváltozat a standard termékváltozattal megegyező objektummodellt követi.

```azurepowershell
$ipconfig = New-AzApplicationGatewayIPConfiguration -Name "IPConfig" -Subnet $gwSubnet
$fip = New-AzApplicationGatewayFrontendIPConfig -Name "FrontendIPCOnfig" -PublicIPAddress $publicip
$pool = New-AzApplicationGatewayBackendAddressPool -Name "Pool1" `
       -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com
$fp01 = New-AzApplicationGatewayFrontendPort -Name "HTTPPort" -Port 80

$listener01 = New-AzApplicationGatewayHttpListener -Name "HTTPListener" `
             -Protocol Http -FrontendIPConfiguration $fip -FrontendPort $fp01

$setting = New-AzApplicationGatewayBackendHttpSettings -Name "BackendHttpSetting1" `
          -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

## <a name="specify-your-http-header-rewrite-rule-configuration"></a>A HTTP-fejléc újraírási szabálykonfigurációjának megadása

Konfigurálja a http fejlécek újraírásához szükséges új objektumokat:

- **RequestHeaderConfiguration**: ez az objektum az újraírni kívánt kérelemfejléc-mezők és az új érték megadására szolgál, amelybe az eredeti fejléceket újra kell írni.
- **ResponseHeaderConfiguration**: ez az objektum az újraírni kívánt válaszfejléc-mezők és az új érték megadására szolgál, amelybe az eredeti fejléceket újra kell írni.
- **ActionSet**: ez az objektum a fent megadott kérés- és válaszfejlécek konfigurációit tartalmazza. 
- **RewriteRule**: ez az objektum tartalmazza a fent megadott összes *műveletkészletet.* 
- **RewriteRuleSet**- ez az objektum tartalmazza az összes *újrawriteRules* és csatolni kell egy kérelem útválasztási szabály - alap- vagy elérési út alapú.

   ```azurepowershell
   $requestHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-isThroughProxy" -HeaderValue "True"
   $responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Strict-Transport-Security" -HeaderValue "max-age=31536000"
   $actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration    
   $rewriteRule = New-AzApplicationGatewayRewriteRule -Name rewriteRule1 -ActionSet $actionSet    
   $rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name rewriteRuleSet1 -RewriteRule $rewriteRule
   ```

## <a name="specify-the-routing-rule"></a>Az útválasztási szabály megadása

Kérelem-útválasztási szabály létrehozása. Létrehozása után ez az újraírási konfiguráció az útválasztási szabályon keresztül csatlakozik a forrásfigyelőhöz. Alapvető útválasztási szabály használataesetén a fejléc újraírási konfigurációja egy forrásfigyelőhöz van társítva, és globális fejléc-újraírás. Görbealapú útválasztási szabály használata esetén a fejléc újraírási konfigurációja az URL-elérési út térképén van definiálva. Tehát csak a webhely adott elérési útterületén érvényes. Az alábbiakban létrejön egy alapvető útválasztási szabály, és az újraírási szabálykészlet csatolva van.

```azurepowershell
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "Rule1" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener01 -BackendAddressPool $pool -RewriteRuleSet $rewriteRuleSet
```

## <a name="specify-autoscale"></a>Automatikus méretezés megadása

Most megadhatja az alkalmazásátjáró automatikus skálázási konfigurációját. Az Application Gateway két automatikus skálázási típust támogat:

* **Rögzített kapacitású mód**. Ebben a módban az Application Gateway nem automatikus skálázású, és rögzített skálázásiegység-kapacitással működik.

   ```azurepowershell
   $sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2 -Capacity 2
   ```

* **Automatikus skálázási mód**. Ebben a módban az Application Gateway az alkalmazás forgalmi mintázata alapján automatikus skálázású.

   ```azurepowershell
   $autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 2
   $sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
   ```

## <a name="create-the-application-gateway"></a>Application Gateway létrehozása

Hozza létre az alkalmazásátjárót, és tartalmazza a redundanciazónákat és az automatikus skálázási konfigurációt.

```azurepowershell
$appgw = New-AzApplicationGateway -Name "AutoscalingAppGw" -Zone 1,2,3 -ResourceGroupName $rg -Location $location -BackendAddressPools $pool -BackendHttpSettingsCollection $setting -GatewayIpConfigurations $ipconfig -FrontendIpConfigurations $fip -FrontendPorts $fp01 -HttpListeners $listener01 -RequestRoutingRules $rule01 -Sku $sku -AutoscaleConfiguration $autoscaleConfig -RewriteRuleSet $rewriteRuleSet
```

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

A Get-AzPublicIPAddress használatával leszeretné késni az alkalmazásátjáró nyilvános IP-címét. Másolja a nyilvános IP-címet vagy a DNS nevét, majd illessze be a böngésző címsorába.

```azurepowershell
Get-AzPublicIPAddress -ResourceGroupName $rg -Name AppGwVIP
```



## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Először vizsgálja meg az alkalmazásátjáróval létrehozott erőforrásokat. Ezt követően, ha már nincs rájuk `Remove-AzResourceGroup` szükség, a paranccsal eltávolíthatja az erőforráscsoportot, az alkalmazásátjárót és az összes kapcsolódó erőforrást.

`Remove-AzResourceGroup -Name $rg`

## <a name="next-steps"></a>További lépések

- [Alkalmazásátjáró létrehozása URL-alapú útválasztási szabályokkal](./tutorial-url-route-powershell.md)
