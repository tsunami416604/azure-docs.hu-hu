---
title: Az Azure Application Gateway HTTP-fejlécek újraírása
description: Ez a cikk információt nyújt az Azure Application Gateway létrehozása, és írja újra az Azure PowerShell-lel HTTP-fejlécek
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 12/20/2018
ms.author: absha
ms.openlocfilehash: 4784ac8ac619a1b9a00f2e869d796d05dd9658df
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54434410"
---
# <a name="tutorial-create-an-application-gateway-and-rewrite-http-headers"></a>Oktatóanyag: Application gateway létrehozása, és a HTTP-fejlécek újraírása

Azure PowerShell-lel való konfigurálásához használható [HTTP-kérelmek és válaszfejlécek újraírási szabályok](rewrite-http-headers.md) létrehozásakor, az új [automatikus skálázást és zónaredundáns application gateway Termékváltozat](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)

> [!IMPORTANT] 
> Az Application Gateway automatikus skálázású és zónaredundáns termékváltozata jelenleg nyilvános előzetes verzióban érhető el. Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Az automatikus skálázási virtuális hálózat létrehozása
> * Fenntartott nyilvános IP-cím létrehozása
> * Az application gateway infrastruktúra beállítása
> * Adja meg a http-fejléc újraírási szabálykonfiguráció
> * Automatikus méretezés megadása
> * Application Gateway létrehozása
> * Az alkalmazásátjáró tesztelése

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Ehhez az oktatóanyaghoz az Azure PowerShellt helyileg kell futtatnia. Rendelkeznie kell Az 1.0.0-s verziójának modul, vagy újabb verziója van telepítve. Futtatás `Import-Module Az` , majd`Get-Module Az` a verzió megkereséséhez. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) ismertető cikket. A PowerShell-verzió ellenőrzése után futtassa az `Login-AzAccount` parancsot az Azure-hoz való kapcsolódáshoz.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
Hozzon létre egy erőforráscsoportot az elérhető helyek egyikén.

```azurepowershell
$location = "East US 2"
$rg = "<rg name>"

#Create a new Resource Group
New-AzResourceGroup -Name $rg -Location $location
```

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Az automatikus skálázás application Gateway egy kijelölt alhálózatot a virtuális hálózat létrehozása. Jelenleg az egyes dedikált alhálózatokon csak egy automatikus skálázású Application Gateway helyezhető üzembe.

```azurepowershell
#Create VNet with two subnets
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -AddressPrefix "10.0.0.0/24"
$sub2 = New-AzVirtualNetworkSubnetConfig -Name "BackendSubnet" -AddressPrefix "10.0.1.0/24"
$vnet = New-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg `
       -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $sub1, $sub2
```

## <a name="create-a-reserved-public-ip"></a>Fenntartott nyilvános IP-cím létrehozása

Adja meg a nyilvános IP-címre, a kiosztási módszert **statikus**. Az automatikus skálázású Application Gateway virtuális IP-címe csak statikus lehet. A dinamikus IP-címek nem használhatók. Csak a standard PublicIPAddress termékváltozat támogatott.

```azurepowershell
#Create static public IP
$pip = New-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP" `
       -location $location -AllocationMethod Static -Sku Standard
```

## <a name="retrieve-details"></a>Részletek beolvasása

Kérje le az IP-konfiguráció részleteit az application Gateway létrehozása egy helyi objektumban, az erőforráscsoport, alhálózatot és IP-adatait.

```azurepowershell
$resourceGroup = Get-AzResourceGroup -Name $rg
$publicip = Get-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP"
$vnet = Get-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -VirtualNetwork $vnet
```

## <a name="configure-the-infrastructure"></a>Az infrastruktúra konfigurálása

Konfigurálja az IP config, előtér-IP-config, háttérkészlet-, HTTP beállítások, tanúsítvány, port és figyelő meglévő Standard application Gateway egy azonos formátumban. Az új termékváltozat a standard termékváltozattal megegyező objektummodellt követi.

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

## <a name="specify-your-http-header-rewrite-rule-configuration"></a>Adja meg a HTTP-fejléc újraírási szabálykonfiguráció

Adja meg a szükséges a http-fejlécek újraírási új objektumok:

- **RequestHeaderConfiguration**: Ez az objektum az eredeti fejlécek kell írni az új érték pedig a kérés üzenetfejlécének mezői újraírási kívánt meghatározására szolgál.
- **ResponseHeaderConfiguration**: Ez az objektum az eredeti fejlécek kell írni az új érték pedig a válasz üzenetfejlécének mezői újraírási kívánt meghatározására szolgál.
- **ActionSet**: Ez az objektum tartalmaz a kérelmek és válaszfejlécek, a fentiekben ismertetett konfigurációit. 
- **RewriteRule**: Ez az objektum tartalmazza az összes a *actionSets* fent megadott. 
- **RewriteRuleSet**– Ez az objektum tartalmazza az összes a *rewriteRules* és a egy kérés útválasztási szabály – alap- vagy -alapú csatlakoztatni kell.

   ```azurepowershell
   $requestHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-isThroughProxy" -HeaderValue "True"
   $responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Strict-Transport-Security" -HeaderValue "max-age=31536000"
   $actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration    
   $rewriteRule = New-AzApplicationGatewayRewriteRule -Name rewriteRule1 -ActionSet $actionSet    
   $rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name rewriteRuleSet1 -RewriteRule $rewriteRule
   ```

## <a name="specify-the-routing-rule"></a>Adja meg az útválasztási szabályt

Hozzon létre egy kérelem-útválasztási szabály. Létrehozása után ezt írja újra a konfigurációt a forrás figyelőt az útválasztási szabály van csatolva. Egy alapszintű útválasztási szabályt használatakor a fejléc újraírási konfigurációs társítva a forrás-figyelő és egy globális fejléce módosítsa úgy. -Alapú útválasztási szabály használata esetén a fejléc újraírási konfiguráció az URL-Címtérkép van definiálva. Ezért csak vonatkozik egy helyet a megadott elérési út területéhez. Az alábbiakban jön létre egy alapszintű útválasztási szabályt, és a újraírási szabálykészlet van csatolva.

```azurepowershell
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "Rule1" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener01 -BackendAddressPool $pool -RewriteRuleSet $rewriteRuleSet
```

## <a name="specify-autoscale"></a>Automatikus méretezés megadása

Most már adhatja meg az application gateway az automatikus skálázási konfigurációját. Az Application Gateway két automatikus skálázási típust támogat:

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

Az application gateway létrehozása és a redundancia zónák és az automatikus skálázási konfigurációját.

```azurepowershell
$appgw = New-AzApplicationGateway -Name "AutoscalingAppGw" -Zone 1,2,3 -ResourceGroupName $rg -Location $location -BackendAddressPools $pool -BackendHttpSettingsCollection $setting -GatewayIpConfigurations $ipconfig -FrontendIpConfigurations $fip -FrontendPorts $fp01 -HttpListeners $listener01 -RequestRoutingRules $rule01 -Sku $sku -AutoscaleConfiguration $autoscaleConfig -RewriteRuleSet $rewriteRuleSet
```

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

Az application Gateway nyilvános IP-címének lekéréséhez használja a Get-AzureRmPublicIPAddress. Másolja a nyilvános IP-címet vagy a DNS nevét, majd illessze be a böngésző címsorába.

```azurepowershell
Get-AzPublicIPAddress -ResourceGroupName $rg -Name AppGwVIP
```



## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Először Fedezze fel az erőforrásokat, az application gateway-ekkel hozta létre. Ezután, amikor szükség van rájuk már nem, használhatja a `Remove-AzResourceGroup` paranccsal törölheti az erőforráscsoportot, az application gateway és az összes kapcsolódó erőforrás.

`Remove-AzResourceGroup -Name $rg`

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Alkalmazásátjáró létrehozása URL-alapú útválasztási szabályokkal](./tutorial-url-route-powershell.md)
