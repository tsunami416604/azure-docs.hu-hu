---
title: "Alkalmazásátjáró létrehozása URL-útválasztási szabályok használatával |} Microsoft Docs"
description: "Ezen a lapon létrehozása és konfigurálása az Azure Alkalmazásátjáró URL-útválasztási szabályok használatával utasításokat tartalmaz."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: d141cfbb-320a-4fc9-9125-10001c6fa4cf
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/03/2017
ms.author: davidmu
ms.openlocfilehash: f0b085ebf922cd5b14acd91bf86b9262a6921e9e
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="create-an-application-gateway-by-using-path-based-routing"></a>Alkalmazásátjáró létrehozása elérési-alapú útválasztási használatával

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-url-route-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-url-route-arm-ps.md)
> * [Azure CLI 2.0](application-gateway-create-url-route-cli.md)

A HTTP-kérések URL-címe alapján útvonalak elérési-alapú útválasztási társítja. Ellenőrzi, hogy nincs konfigurálva az URL-cím szerepel az Alkalmazásátjáró a háttér-készlet egy útvonalat, és ezután elküldi a hálózati forgalom a meghatározott háttér-készlethez. URL-alapú útválasztási gyakori felhasználási-hoz való különböző háttér-kiszolgálófiók tartalom különböző érkező kérések elosztása.

Az Azure Application Gateway két szabály tartozik: alapvető és útvonal-alapú útválasztást. Basic a háttér-készletek ciklikus multiplexelés szolgáltatást biztosít. A háttér-készlet kiválasztása az elérési út mintája a kérelem URL-címének elérési út-alapú útválasztási ciklikus multiplexelés mellett is használ.

## <a name="scenario"></a>Forgatókönyv

A következő példában Application Gateway szolgálja ki a contoso.com forgalom két háttér-kiszolgálófiók rendelkezik: egy videó kiszolgálókészletet és egy kép kiszolgálókészlethez.

Kérések a http://contoso.com/image * legyenek átirányítva a kép kiszolgálókészlet (**pool1**), és a rendszer kérést a http://contoso.com/video * átirányítja a videó kiszolgálókészlet (**pool2**). Ha az elérési út minták egyike sem felel meg, egy alapértelmezett kiszolgálókészletet (**pool1**) van kiválasztva.

![URL-cím útvonal](./media/application-gateway-create-url-route-arm-ps/figure1.png)

## <a name="before-you-begin"></a>Előkészületek

1. Telepítse az Azure PowerShell-parancsmagok legújabb verzióját a Webplatform-telepítővel. A [Letöltések lap](https://azure.microsoft.com/downloads/) **Windows PowerShell** szakaszából letöltheti és telepítheti a legújabb verziót.
2. Hozzon létre egy virtuális hálózat és az Alkalmazásátjáró alhálózatot. Győződjön meg arról, hogy egyetlen virtuális gépek vagy a felhőben történő alkalmazáshoz alhálózaton. Az Application Gateway-nek egyedül kell lennie a virtuális hálózat alhálózatán.
3. Győződjön meg arról, hogy a háttér-címkészletet az Alkalmazásátjáró hozzáadott kiszolgálók létezik, vagy arról, hogy a végpontok a virtuális hálózat vagy a nyilvános IP-cím/VIP rendelt.

## <a name="requirements-to-create-an-application-gateway"></a>Alkalmazásátjáró létrehozásához szükséges követelményeknek

* **Háttér-kiszolgálófiók készlet**: a háttér-kiszolgálók IP-címek listáját. Az IP-címek felsorolt kell vagy a virtuális hálózati alhálózathoz tartozik, vagy egy nyilvános IP-cím/VIP kell.
* **Háttér-kiszolgálófiók Készletbeállítások**: például az affinitási cookie-alapú, port és protokoll. Ezek készlet kötődik, és alkalmazza a készletben lévő összes kiszolgálót.
* **Előtér-port**: A nyilvános portot, az alkalmazás-átjárón meg van nyitva. Forgalom találatok ezt a portot, és ezután átirányítja a felhasználókat a háttér-kiszolgálóhoz.
* **Figyelő**: A figyelő legalább egy előtér-port és a protokollt (Http vagy HTTPS-t, amely kis-és nagybetűket), az SSL-tanúsítvány neve (ha az SSL beállításának-kiszervezés).
* **A szabály**: A szabály van kötve, a figyelő és a háttér-kiszolgálófiók készletben, és határozza meg, melyik készletbe a forgalom legyenek irányítva, ha a találatok száma a figyelő.

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

A klasszikus üzembe helyezési modellel és az Azure Resource Manager használatával közötti különbség létrehozása az Alkalmazásátjáró és be kell állítani az elemek sorrendje.

A Resource Managerrel az Application Gateway összes alkotóelemét külön kell konfigurálni, és csak utána kell őket összeállítani az Application Gateway-erőforrás létrehozásához.

Kövesse az alábbi lépéseket Alkalmazásátjáró létrehozása:

1. Egy erőforráscsoport létrehozása a Resource Manager számára.
2. Egy virtuális hálózat, alhálózat és nyilvános IP-cím létrehozása az Application Gateway számára.
3. Egy Application Gateway konfigurációs objektum létrehozása.
4. Egy Application Gateway erőforrás létrehozása.

## <a name="create-a-resource-group-for-resource-manager"></a>Erőforráscsoport létrehozása a Resource Managerhez

Győződjön meg arról, hogy az Azure PowerShell legújabb verzióját használja. További információ: található [Windows PowerShell használata a Resource Manager](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>1. lépés

Jelentkezzen be az Azure-bA.

```powershell
Login-AzureRmAccount
```

Azokkal a hitelesítő adatait kéri.<BR>

### <a name="step-2"></a>2. lépés

Keresse meg a fiókot az előfizetésekben.

```powershell
Get-AzureRmSubscription
```

### <a name="step-3"></a>3. lépés

Válassza ki, hogy melyik Azure előfizetést fogja használni. <BR>

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>4. lépés

Hozzon létre egy erőforráscsoportot. (Hagyja ki ezt a lépést, ha egy meglévő erőforráscsoportot használ.)

```powershell
$resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US"
```

Azt is megteheti az Alkalmazásátjáró erőforráscsoport címkéket hozhat létre:

```powershell
$resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US" -Tags @{Name = "testtag"; Value = "Application Gateway URL routing"} 
```

Az Azure Resource Manager megköveteli, hogy erőforráscsoport megadjon egy alapértelmezett helyen, amely szolgál, hogy a csoportban található összes erőforrást. Győződjön meg arról, hogy minden parancs Alkalmazásátjáró létrehozása ugyanabban az erőforráscsoportban.

Az előző példában azt létrehozott egy "appgw-RG" nevű csoportot, és használja a következő helyen: "USA nyugati régiója."

> [!NOTE]
> Ha egy egyéni mintavétel az alkalmazás átjáró van szüksége, lépjen [PowerShell használatával hozzon létre olyan átjárót egyéni mintavételt](application-gateway-create-probe-ps.md). Lásd: [ Alkalmazásátjáró állapotfigyelési áttekintése](application-gateway-probe-overview.md) további információt.
> 
> 

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Virtuális hálózat és alhálózat létrehozása az Application Gateway számára

Az alábbi példa bemutatja, hogyan hozhat létre virtuális hálózatot a Resource Manager használatával. Ez a példa létrehoz egy virtuális hálózatot az Alkalmazásátjáró. Alkalmazásátjáró saját alhálózatba van szükség. Emiatt az Alkalmazásátjáró létrehozott alhálózati értéke kisebb, mint a virtuális hálózat címtere. Ez lehetővé teszi, hogy más erőforrások, például az, de nem kizárólagosan a webkiszolgálók, konfigurálni kell az azonos virtuális hálózatban.

### <a name="step-1"></a>1. lépés

Rendelje hozzá a 10.0.0.0/24 címtartományt a virtuális hálózat létrehozásához használni kívánt alhálózati változóhoz.  Az Alkalmazásátjáró, a következő példában használt alhálózat-konfigurációs objektum létrejön.

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

### <a name="step-2"></a>2. lépés

Hozzon létre egy virtuális hálózatot nevű **appgwvnet** erőforráscsoportban **appgw-rg** az USA nyugati régiója régió a előtag 10.0.0.0/16 alhálózati 10.0.0.0/24 való használatával. Ezzel befejezte az alkalmazás átjáró elhelyezése egyetlen alhálózattal a virtuális hálózat konfigurációját.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
```

### <a name="step-3"></a>3. lépés

Rendelje hozzá a alhálózati változó a következő lépéseket. Ez továbbítódik a `New-AzureRMApplicationGateway` parancsmag egy későbbi lépésben.

```powershell
$subnet=$vnet.Subnets[0]
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Nyilvános IP-cím létrehozása az előtérbeli konfigurációhoz

Hozzon létre egy **publicIP01** nevű, nyilvános IP-címhez tartozó erőforrást az **appgw-rg** nevű erőforráscsoportban, az USA nyugati régiójában. Alkalmazásátjáró használhatja egy nyilvános IP-címet, a belső IP-címet, vagy mindkettő terheléselosztás kérelmek fogadására.  A példa egy nyilvános IP-címet. A következő példában DNS-neve nem úgy van konfigurálva, a nyilvános IP-cím létrehozásához mert Alkalmazásátjáró nem támogatja az egyéni DNS-nevek nyilvános IP-címeket.  Ha egy egyéni nevet a nyilvános végpontot, hozzon létre egy CNAME rekordot a nyilvános IP-cím automatikusan létrehozott DNS-nevének mutasson.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

Amikor a szolgáltatás elindul, egy IP-cím lesz kiosztva az Application Gatewaynek.

## <a name="create-the-application-gateway-configuration"></a>Az alkalmazás átjáró-konfiguráció létrehozása

Az összes konfigurációs elemek kell be kell állítania az Alkalmazásátjáró létrehozása előtt. Az alábbi lépéseket a konfigurációs elemben alkalmazás átjáró erőforrás szükséges.

### <a name="step-1"></a>1. lépés

Hozzon létre egy **gatewayIP01** nevű Application Gateway IP-konfigurációt. Alkalmazásátjáró indításakor azt szerzi be a beállított alhálózatból származó IP-címnek, és a hálózati forgalmat a háttér IP-címkészlet IP-címek útválasztását. Ne feledje, hogy minden példány egy IP-címet vesz fel.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

### <a name="step-2"></a>2. lépés

Konfigurálja a háttér IP-címkészletet nevű **pool1** és **pool2** az IP-címekkel rendelkező **pool1** és **pool2**. Ezek azok az erőforrások, a webes alkalmazás fogja védeni az Alkalmazásátjáró üzemeltető IP-címét. A háttér-a készlet tagjainak az összes érvényesíti a lesz kifogástalan, alapszintű vagy egyéni mintavételt. Ezek után a rendszer hozzájuk irányítja a forgalmat, amikor kérések érkeznek az Application Gatewayre. Háttér-készletek az Alkalmazásátjáró belül több szabály által használható. Ez azt jelenti, hogy egy háttér címkészletet használható több olyan webes alkalmazásokhoz, amelyek ugyanazon a gazdagépen.

```powershell
$pool1 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221, 134.170.185.50

$pool2 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool02 -BackendIPAddresses 134.170.186.47, 134.170.189.222, 134.170.186.51
```

Ebben a példában a két háttér-készletek irányítható a hálózati forgalom az URL-címe alapján. Egy készlet URL-címet származó forgalmat megkapja "/ videó," és az egyéb készlet elérési származó forgalmat megkapja "/ image." Az előző IP-címeket lecseréli a saját alkalmazása IP-címvégpontjaira. 

### <a name="step-3"></a>3. lépés

Alkalmazásátjáró beállításainak konfigurálása **poolsetting01** és **poolsetting02** az elosztott terhelésű hálózati forgalmat a háttér-készletben. Ebben a példában a háttér-készletek különböző háttér-készlet beállításait konfigurálja. Mindegyik háttér-gyűjtő a saját beállításai lehetnek.  Szabályok beállításokkal háttér HTTP irányíthatja a forgalmat a megfelelő háttér-készlet tagjai számára. Ez határozza meg a protokoll és a forgalom küldése a háttér-készlettag használt port. Cookie-alapú munkamenetek is a háttér-HTTP-beállításait határozza meg. Ha engedélyezve van, munkamenet cookie-alapú kapcsolat küld forgalom az azonos háttér egyes csomagok az előző kérelmekben.

```powershell
$poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120

$poolSetting02 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting02" -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 240
```

### <a name="step-4"></a>4. lépés

Az előtér-IP-cím konfigurálása a nyilvános IP-végpontokon. Egy figyelő vonatkoznak a passzív felé néző IP-cím és a figyelő az előtér-IP-konfigurációs objektum használja.

```powershell
$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-5"></a>5. lépés

Konfigurálja az előtérbeli portot egy Application Gatewayhez. A figyelő az előtér-port konfigurációs objektum használja milyen az Alkalmazásátjáró sem figyeli a forgalmat a figyelő portjának megadása.

```powershell
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "fep01" -Port 80
```

### <a name="step-6"></a>6. lépés

A figyelő a nyilvános IP-címet és portot a bejövő hálózati forgalom fogadására szolgáló konfigurálása. Az alábbi példa átveszi a korábban konfigurált előtér-IP-konfiguráció, előtér-konfiguráció és a protokollt (Http vagy HTTPS-t, amely kis-és nagybetűket), és konfigurálja a figyelőt. A példában a figyelő a 80-as porton figyeli a HTTP-forgalmat a korábban létrehozott nyilvános IP-címen.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol Http -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01
```

### <a name="step-7"></a>7. lépés

A háttér-készletek az URL-cím szabály elérési útvonalainak konfigurálása. Ebben a lépésben konfigurálja az Application Gateway relatív elérési, és az URL-cím és a háttér-címkészlet, amely hozzá van rendelve a bejövő forgalom kezelésére közötti megfeleltetés.

> [!IMPORTANT]
> Mindegyik elérési út egy "/" kell kezdődnie, és csillag csak engedélyezett végén. Érvényes többek között az /xyz, /xyz*, vagy /xyz/*. Az az elérési út matcher táplált a karakterlánc nem tartalmaz sem szöveges az első után "?" vagy "#", és ezek a karakterek nem engedélyezettek. 

Az alábbi példa létrehoz két szabályt: egy olyan "/ kép /" elérési út útválasztási háttér- **pool1**, egy pedig a "/ videó /" elérési út útválasztási forgalmat háttér- **pool2**. Ezek a szabályok győződjön meg arról, hogy az egyes URL-címeket a háttérben történő továbbítódik. Például http://contoso.com/image/figure1.jpg ugrik **pool1** és http://contoso.com/video/example.mp4 ugrik **pool2**.

```powershell
$imagePathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule1" -Paths "/image/*" -BackendAddressPool $pool1 -BackendHttpSettings $poolSetting01

$videoPathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule2" -Paths "/video/*" -BackendAddressPool $pool2 -BackendHttpSettings $poolSetting02
```

Ha az elérési út nem felel meg az előre definiált elérésiút-szabály, a szabály térkép konfiguráció is konfigurálja egy háttér címkészletet. Például http://contoso.com/shoppingcart/test.html ugrik **pool1** , mert az alapértelmezett alkalmazáskészlet páratlan forgalom nevezünk.

```powershell
$urlPathMap = New-AzureRmApplicationGatewayUrlPathMapConfig -Name "urlpathmap" -PathRules $videoPathRule, $imagePathRule -DefaultBackendAddressPool $pool1 -DefaultBackendHttpSettings $poolSetting02
```

### <a name="step-8"></a>8. lépés

Hozzon létre egy szabályt beállítást. Ez a lépés az Alkalmazásátjáró URL-cím elérési út-alapú útválasztási használatára konfigurálja. A `$urlPathMap` az előző lépésben megadott változó most az elérési út alapuló szabály létrehozására szolgál. Ebben a lépésben a szabály azt társítani egy figyelő, és az URL-cím elérési útjának leképezése a korábban létrehozott.

```powershell
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType PathBasedRouting -HttpListener $listener -UrlPathMap $urlPathMap
```

### <a name="step-9"></a>9. lépés

Konfigurálja az Application Gatewayben a példányok számát és a méretet.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name "Standard_Small" -Tier Standard -Capacity 2
```

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

Hozzon létre egy alkalmazást az előző lépéseket az összes konfigurációs objektumok.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-RG -Location "West US" -BackendAddressPools $pool1,$pool2 -BackendHttpSettingsCollection $poolSetting01, $poolSetting02 -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener -UrlPathMaps $urlPathMap -RequestRoutingRules $rule01 -Sku $sku
```

## <a name="get-an-application-gateway-dns-name"></a>Egy alkalmazás átjáró DNS-név beolvasása

Az átjáró létrehozása után az előtér-kommunikációra kell konfigurálnia. Egy nyilvános IP-cím használata esetén az Application Gateway egy dinamikusan hozzárendelt DNS-nevet, amely nincs rövid van szükség. Annak érdekében, hogy az ügyfelek is elérte az Alkalmazásátjáró, egy CNAME rekordot a segítségével a nyilvános végpontot az Alkalmazásátjáró mutasson. További információkért lásd: [egy egyéni tartománynevet, az Azure-felhőszolgáltatás konfigurálása](../cloud-services/cloud-services-custom-domain-name-portal.md).

Az előtér-IP CNAME rekord konfigurálásához beolvasása részleteit az Alkalmazásátjáró és a hozzá tartozó IP-/ DNS-nevet az Alkalmazásátjáró csatolva PublicIPAddress elem használatával. Az Alkalmazásátjáró DNS-név használatával hozzon létre egy CNAME rekordot. Nem ajánlott, mert előfordulhat, hogy módosítsa a VIP Alkalmazásátjáró újraindítása A rekordok használatát.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
```

```
Name                     : publicIP01
ResourceGroupName        : appgw-RG
Location                 : westus
Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
ResourceGuid             : 00000000-0000-0000-0000-000000000000
ProvisioningState        : Succeeded
Tags                     : 
PublicIpAllocationMethod : Dynamic
IpAddress                : xx.xx.xxx.xx
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                                "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
                            Configurations/frontend1"
                            }
DnsSettings              : {
                                "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                            }
```

## <a name="next-steps"></a>Következő lépések

Ha azt szeretné, további információt a Secure Sockets Layer (SSL) kiszervezési, lásd: [SSL kiszervezési Alkalmazásátjáró konfigurálása Azure Resource Manager használatával](application-gateway-ssl-arm.md).

