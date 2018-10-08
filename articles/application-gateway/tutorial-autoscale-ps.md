---
title: Fenntartott IP-címmel rendelkező, automatikus skálázású, zónaredundáns Application Gateway létrehozása – Azure PowerShell
description: Megtudhatja, hogy hozhat létre fenntartott IP-címmel rendelkező, automatikus skálázású, zónaredundáns Application Gatewayt az Azure PowerShell használatával.
services: application-gateway
author: amitsriva
ms.service: application-gateway
ms.topic: tutorial
ms.date: 9/26/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 6c54706f45653c43e6b41d0adb3132583079e6b6
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47167530"
---
# <a name="tutorial-create-an-autoscaling-zone-redundant-application-gateway-with-a-reserved-virtual-ip-address-using-azure-powershell"></a>Oktatóanyag: Fenntartott IP-címmel rendelkező, automatikus skálázású, zónaredundáns Application Gateway létrehozása az Azure PowerShell használatával

Ez az oktatóanyag bemutatja, hogyan hozhat létre Azure Application Gateway az Azure PowerShell-parancsmagok és az Azure Resource Manager-alapú üzemi modell segítségével. Ez az oktatóanyag az új automatikus skálázású termékváltozat és a meglévő standard termékváltozat közötti különbségekre összpontosít. Pontosabban az automatikus skálázást, a zónaredundanciát és a fenntartott virtuális IP-címeket (statikus IP-címeket) támogató funkciókról lesz szó.

Az Application Gateway automatikus skálázásával és zónaredundanciájával kapcsolatban további információt az [automatikus skálázású és zónaredundáns Application Gatewayt (nyilvános előzetes verzió)](application-gateway-autoscaling-zone-redundant.md) bemutató cikk tartalmaz.

> [!IMPORTANT]
> Az Application Gateway automatikus skálázású és zónaredundáns termékváltozata jelenleg nyilvános előzetes verzióban érhető el. Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az automatikus skálázás konfigurációs paraméterének beállítása
> * A zóna paraméter használata
> * Statikus virtuális IP-cím használata
> * Application Gateway létrehozása


Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

Ehhez az oktatóanyaghoz az Azure PowerShellt helyileg kell futtatnia. Az Azure PowerShell-modul 6.9.0-s vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) ismertető cikket. A PowerShell-verzió ellenőrzése után futtassa az `Login-AzureRmAccount` parancsot az Azure-hoz való kapcsolódáshoz.

## <a name="sign-in-to-your-azure-account"></a>Jelentkezzen be az Azure-fiókjába

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

## <a name="create-a-vnet"></a>Virtuális hálózat létrehozása
Hozzon létre virtuális hálózatot egy automatikus skálázású Application Gatewayhez tartozó dedikált alhálózattal. Jelenleg az egyes dedikált alhálózatokon csak egy automatikus skálázású Application Gateway helyezhető üzembe.

```azurepowershell
#Create VNet with two subnets
$sub1 = New-AzureRmVirtualNetworkSubnetConfig -Name "AppGwSubnet" -AddressPrefix "10.0.0.0/24"
$sub2 = New-AzureRmVirtualNetworkSubnetConfig -Name "BackendSubnet" -AddressPrefix "10.0.1.0/24"
$vnet = New-AzureRmvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg `
       -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $sub1, $sub2
```

## <a name="create-a-reserved-public-ip"></a>Fenntartott nyilvános IP-cím létrehozása

A PublicIPAddress kiosztási módszere legyen **statikus**. Az automatikus skálázású Application Gateway virtuális IP-címe csak statikus lehet. A dinamikus IP-címek nem használhatók. Csak a standard PublicIPAddress termékváltozat támogatott.

```azurepowershell
#Create static public IP
$pip = New-AzureRmPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP" `
       -location $location -AllocationMethod Static -Sku Standard
```

## <a name="retrieve-details"></a>Részletek beolvasása

Az erőforráscsoport, az alhálózat és az IP-cím beolvasása helyi objektumban az Application Gateway IP-konfiguráció részleteinek létrehozásához.

```azurepowershell
$resourceGroup = Get-AzureRmResourceGroup -Name $rg
$publicip = Get-AzureRmPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP"
$vnet = Get-AzureRmvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg
$gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "AppGwSubnet" -VirtualNetwork $vnet
```
## <a name="configure-application-gateway-infrastructure"></a>Az Application Gateway infrastruktúrájának konfigurálása
Az IP-konfigurációt, az előtérbeli IP-konfigurációt, a háttérkészletet, a HTTP-beállításokat, a tanúsítványt, a portot, a figyelőt és a szabályokat a meglévő standard Application Gatewaynek megfelelő formátumban kell konfigurálnia. Az új termékváltozat a standard termékváltozattal megegyező objektummodellt követi.

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

Most megadhatja az Application Gateway automatikus skálázási konfigurációját. Az Application Gateway két automatikus skálázási típust támogat:

- **Rögzített kapacitású mód**. Ebben a módban az Application Gateway nem automatikus skálázású, és rögzített skálázásiegység-kapacitással működik.

   ```azurepowershell
   $sku = New-AzureRmApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
   ```
- **Automatikus skálázási mód**. Ebben a módban az Application Gateway az alkalmazás forgalmi mintázata alapján automatikus skálázású.

   ```azurepowershell
   $autoscaleConfig = New-AzureRmApplicationGatewayAutoscaleConfiguration -MinCapacity 2
   $sku = New-AzureRmApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
   ```

## <a name="create-the-application-gateway"></a>Application Gateway létrehozása

Hozzon létre egy Application Gatewayt redundanciazónákkal. 

A zóna konfigurációja csak azokban a régiókban támogatott, ahol az Azure-zónák elérhetők. Ne használja a zóna paramétert azokban a régiókban, ahol az Azure-zónák nem érhetők el. Application Gateway egyetlen zónában, két zónában vagy akár mindhárom zónában is üzembe helyezhető. A PublicIPAddressnek az egyzónás Application Gateway esetén ugyanahhoz a zónához kell kötődnie. Két vagy három zónás redundáns Application Gateway esetén a PublicIPAddressnek is zónaredundánsnak kell lennie, ezért nem lehet megadva zóna.

```azurepowershell
$appgw = New-AzureRmApplicationGateway -Name "AutoscalingAppGw" -Zone 1,2,3 `
  -ResourceGroupName $rg -Location $location -BackendAddressPools $pool `
  -BackendHttpSettingsCollection $setting -GatewayIpConfigurations $ipconfig `
  -FrontendIpConfigurations $fip -FrontendPorts $fp01, $fp02 `
  -HttpListeners $listener01, $listener02 -RequestRoutingRules $rule01, $rule02 `
  -Sku $sku -sslCertificates $sslCert01 -AutoscaleConfiguration $autoscaleConfig
```

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

A [Get-AzureRmPublicIPAddress](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermpublicipaddress) parancsmaggal kérje le az alkalmazásátjáró nyilvános IP-címét. Másolja a nyilvános IP-címet vagy a DNS nevét, majd illessze be a böngésző címsorába.

`Get-AzureRmPublicIPAddress -ResourceGroupName $rg -Name AppGwVIP`

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Először tekintse át az Application Gatewayjel együtt létrehozott erőforrásokat, majd ha már nincs rájuk szüksége, az `Remove-AzureRmResourceGroup` paranccsal távolítsa el az eszközcsoportot, az Application Gatewayt és a kapcsolódó erőforrásokat.

`Remove-AzureRmResourceGroup -Name $rg`

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Statikus virtuális IP-cím használata
> * Az automatikus skálázás konfigurációs paraméterének beállítása
> * A zóna paraméter használata
> * Application Gateway létrehozása

> [!div class="nextstepaction"]
> [Alkalmazásátjáró létrehozása URL-alapú útválasztási szabályokkal](./tutorial-url-route-powershell.md)
