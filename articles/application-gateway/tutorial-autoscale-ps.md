---
title: 'Oktatóanyag: Fenntartott IP-címmel rendelkező, automatikus skálázású, zónaredundáns Application Gateway létrehozása – Azure PowerShell'
description: Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre egy automatikus skálázás, a zónaredundáns az application gateway az Azure PowerShell-lel fenntartott IP-cím.
services: application-gateway
author: amitsriva
ms.service: application-gateway
ms.topic: tutorial
ms.date: 11/26/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 99fa5d6f0ba74b56a53f2d1af1b99c7e5c2896a7
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53323200"
---
# <a name="tutorial-create-an-application-gateway-that-improves-web-application-access"></a>Oktatóanyag: Hozzon létre egy application gateway, amely javítja a webes alkalmazás-hozzáférés

Ha Ön rendszergazda a webes alkalmazás-hozzáférés javítása az érintett, optimalizálhatja az application gateway az alapján ügyfél méretezése igény és a span több rendelkezésre állási zónában. Ez az oktatóanyag segítséget nyújt, amely ehhez az Azure Application Gateway-szolgáltatások konfigurálása: az automatikus skálázás, a redundancia zónát, és a fenntartott virtuális IP-címek (statikus IP-cím). Azure PowerShell-parancsmagok és az Azure Resource Manager üzemi modell fogja használni, ha a probléma megoldásához.

> [!IMPORTANT] 
> Az Application Gateway automatikus skálázású és zónaredundáns termékváltozata jelenleg nyilvános előzetes verzióban érhető el. Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az automatikus skálázási virtuális hálózat létrehozása
> * Fenntartott nyilvános IP-cím létrehozása
> * Az application gateway infrastruktúra beállítása
> * Automatikus méretezés megadása
> * Application Gateway létrehozása
> * Az alkalmazásátjáró tesztelése

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Ehhez az oktatóanyaghoz az Azure PowerShellt helyileg kell futtatnia. Az Azure PowerShell-modul 6.9.0-s vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) ismertető cikket. A PowerShell-verzió ellenőrzése után futtassa az `Login-AzureRmAccount` parancsot az Azure-hoz való kapcsolódáshoz.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

```azurepowershell
Connect-AzureRmAccount
Select-AzureRmSubscription -Subscription "<sub name>"
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
Hozzon létre egy erőforráscsoportot az elérhető helyek egyikén.

```azurepowershell
$location = "East US 2"
$rg = "<rg name>"

#Create a new Resource Group
New-AzureRmResourceGroup -Name $rg -Location $location
```

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Az automatikus skálázás application Gateway egy kijelölt alhálózatot a virtuális hálózat létrehozása. Jelenleg az egyes dedikált alhálózatokon csak egy automatikus skálázású Application Gateway helyezhető üzembe.

```azurepowershell
#Create VNet with two subnets
$sub1 = New-AzureRmVirtualNetworkSubnetConfig -Name "AppGwSubnet" -AddressPrefix "10.0.0.0/24"
$sub2 = New-AzureRmVirtualNetworkSubnetConfig -Name "BackendSubnet" -AddressPrefix "10.0.1.0/24"
$vnet = New-AzureRmvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg `
       -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $sub1, $sub2
```

## <a name="create-a-reserved-public-ip"></a>Fenntartott nyilvános IP-cím létrehozása

Adja meg a nyilvános IP-címre, a kiosztási módszert **statikus**. Az automatikus skálázású Application Gateway virtuális IP-címe csak statikus lehet. A dinamikus IP-címek nem használhatók. Csak a standard PublicIPAddress termékváltozat támogatott.

```azurepowershell
#Create static public IP
$pip = New-AzureRmPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP" `
       -location $location -AllocationMethod Static -Sku Standard
```

## <a name="retrieve-details"></a>Részletek beolvasása

Kérje le az IP-konfiguráció részleteit az application Gateway létrehozása egy helyi objektumban, az erőforráscsoport, alhálózatot és IP-adatait.

```azurepowershell
$resourceGroup = Get-AzureRmResourceGroup -Name $rg
$publicip = Get-AzureRmPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP"
$vnet = Get-AzureRmvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg
$gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "AppGwSubnet" -VirtualNetwork $vnet
```

## <a name="configure-the-infrastructure"></a>Az infrastruktúra konfigurálása

Konfigurálja az IP config, előtér-IP-config, háttérkészlet-, HTTP beállítások, tanúsítvány, port, figyelő és szabály meglévő Standard application Gateway egy azonos formátumban. Az új termékváltozat a standard termékváltozattal megegyező objektummodellt követi.

```azurepowershell
$ipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name "IPConfig" -Subnet $gwSubnet
$fip = New-AzureRmApplicationGatewayFrontendIPConfig -Name "FrontendIPCOnfig" -PublicIPAddress $publicip
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name "Pool1" `
       -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "SSLPort" -Port 443
$fp02 = New-AzureRmApplicationGatewayFrontendPort -Name "HTTPPort" -Port 80

$securepfxpwd = ConvertTo-SecureString -String "scrap" -AsPlainText -Force
$sslCert01 = New-AzureRmApplicationGatewaySslCertificate -Name "SSLCert" -Password $securepfxpwd `
            -CertificateFile "D:\Networking\ApplicationGateway\scrap.pfx"
$listener01 = New-AzureRmApplicationGatewayHttpListener -Name "SSLListener" `
             -Protocol Https -FrontendIPConfiguration $fip -FrontendPort $fp01 -SslCertificate $sslCert01
$listener02 = New-AzureRmApplicationGatewayHttpListener -Name "HTTPListener" `
             -Protocol Http -FrontendIPConfiguration $fip -FrontendPort $fp02

$setting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "BackendHttpSetting1" `
          -Port 80 -Protocol Http -CookieBasedAffinity Disabled
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "Rule1" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener01 -BackendAddressPool $pool
$rule02 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "Rule2" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener02 -BackendAddressPool $pool
```

## <a name="specify-autoscale"></a>Automatikus méretezés megadása

Most már adhatja meg az application gateway az automatikus skálázási konfigurációját. Az Application Gateway két automatikus skálázási típust támogat:

* **Rögzített kapacitású mód**. Ebben a módban az Application Gateway nem automatikus skálázású, és rögzített skálázásiegység-kapacitással működik.

   ```azurepowershell
   $sku = New-AzureRmApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2 -Capacity 2
   ```

* **Automatikus skálázási mód**. Ebben a módban az Application Gateway az alkalmazás forgalmi mintázata alapján automatikus skálázású.

   ```azurepowershell
   $autoscaleConfig = New-AzureRmApplicationGatewayAutoscaleConfiguration -MinCapacity 2
   $sku = New-AzureRmApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
   ```

## <a name="create-the-application-gateway"></a>Application Gateway létrehozása

Az application gateway létrehozása és a redundancia zónák és az automatikus skálázási konfigurációját.

```azurepowershell
$appgw = New-AzureRmApplicationGateway -Name "AutoscalingAppGw" -Zone 1,2,3 `
  -ResourceGroupName $rg -Location $location -BackendAddressPools $pool `
  -BackendHttpSettingsCollection $setting -GatewayIpConfigurations $ipconfig `
  -FrontendIpConfigurations $fip -FrontendPorts $fp01, $fp02 `
  -HttpListeners $listener01, $listener02 -RequestRoutingRules $rule01, $rule02 `
  -Sku $sku -sslCertificates $sslCert01 -AutoscaleConfiguration $autoscaleConfig
```

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

Az application Gateway nyilvános IP-címének lekéréséhez használja a Get-AzureRmPublicIPAddress. Másolja a nyilvános IP-címet vagy a DNS nevét, majd illessze be a böngésző címsorába.

`Get-AzureRmPublicIPAddress -ResourceGroupName $rg -Name AppGwVIP`

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Először Fedezze fel az erőforrásokat, az application gateway-ekkel hozta létre. Ezután, amikor szükség van rájuk már nem, használhatja a `Remove-AzureRmResourceGroup` paranccsal törölheti az erőforráscsoportot, az application gateway és az összes kapcsolódó erőforrás.

`Remove-AzureRmResourceGroup -Name $rg`

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Alkalmazásátjáró létrehozása URL-alapú útválasztási szabályokkal](./tutorial-url-route-powershell.md)
