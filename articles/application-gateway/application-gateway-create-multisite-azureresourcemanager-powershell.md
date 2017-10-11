---
title: "Több hely üzemeltetéséhez Alkalmazásátjáró létrehozása |} Microsoft Docs"
description: "Ez a lap létrehozásához, konfiguráljon egy Azure-alkalmazásokban átjárót ugyanahhoz az átjáróhoz a több webalkalmazás üzemeltetéséhez utasításokat tartalmaz."
documentationcenter: na
services: application-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: b107d647-c9be-499f-8b55-809c4310c783
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/12/2016
ms.author: amsriva
ms.openlocfilehash: d42efa7d359f5c87c14afbfd138328b37c8ae6c2
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="create-an-application-gateway-for-hosting-multiple-web-applications"></a>Hozzon létre egy alkalmazás több webalkalmazás üzemeltetéséhez

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-multisite-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-multisite-azureresourcemanager-powershell.md)

Több helyet üzemeltető lehetővé teszi az ugyanazon Alkalmazásátjáró egynél több webalkalmazás telepítését. Az állomásfejlécnek meghatározni, mely figyelő kapja forgalom a bejövő HTTP-kérelmek jelenlétére támaszkodik. A figyelő majd arra utasítja a megfelelő háttérkészlet-forgalom, be az átjáró szabályok meghatározását. Az SSL engedélyezve van a webes alkalmazásokhoz Alkalmazásátjáró a kiszolgálónév jelzése (SNI) bővítménye válassza ki a megfelelő figyelő a webes forgalom támaszkodik. A közös több hely üzemeltetéséhez rendeltetése különböző webtartományok különböző háttér-kiszolgálófiók tárolókészletekben az érkező kérések elosztása. Az azonos gyökértartomány több altartományt hasonló módon is tárolt alkalmazás ugyanahhoz az átjáróhoz.

## <a name="scenario"></a>Forgatókönyv

A következő példában Alkalmazásátjáró van kiszolgáló a contoso.com és fabrikam.com forgalom a két háttér-kiszolgálófiók rendelkezik: contoso kiszolgálókészlet és a fabrikam kiszolgálókészlethez. Hasonló telepítő állomás altartományok például app.contoso.com és blog.contoso.com használható.

![imageURLroute](./media/application-gateway-create-multisite-azureresourcemanager-powershell/multisite.png)

## <a name="before-you-begin"></a>Előkészületek

1. Telepítse az Azure PowerShell-parancsmagok legújabb verzióját a Webplatform-telepítővel. A [Letöltések lap](https://azure.microsoft.com/downloads/) **Windows PowerShell** szakaszából letöltheti és telepítheti a legújabb verziót.
2. Az alkalmazás átjáró használatához a háttér-készlethez hozzáadott kiszolgálók léteznie kell, különben a végpontokat hozott létre vagy egy külön alhálózatot vagy egy nyilvános IP-cím/VIP hozzárendelése a virtuális hálózatban.

## <a name="requirements"></a>Követelmények

* **Háttér-kiszolgálókészlet:** A háttérkiszolgálók IP-címeinek listája. A listán szereplő IP-címeknek a virtuális hálózat alhálózatához kell tartozniuk, vagy nyilvános/virtuális IP-címnek kell lenniük. Teljes Tartománynevét is használható.
* **Háttér-kiszolgálókészlet beállításai:** Minden készletnek vannak beállításai, például port, protokoll vagy cookie-alapú affinitás. Ezek a beállítások egy adott készlethez kapcsolódnak, és a készlet minden kiszolgálójára érvényesek.
* **Előtérbeli port:** Az Application Gateway-en megnyitott nyilvános port. Amikor a forgalom eléri ezt a portot, a port átirányítja az egyik háttérkiszolgálóra.
* **Figyelő:** A figyelő egy előtérbeli porttal, egy protokollal (Http vagy Https, a kis- és a nagybetűk megkülönböztetésével) és SSL tanúsítványnévvel rendelkezik (SSL-kiszervezés konfigurálásakor). A többhelyes engedélyezett alkalmazásátjárót, állomásnév és SNI mutatók is bekerülnek.
* **Szabály:** a szabály van kötve a figyelő, a háttér-kiszolgálófiók-vermet, és határozza meg, mely a forgalom legyenek irányítva, amikor az adott figyelő találatok háttér-kiszolgálófiók készlet. Szabályok feldolgozása a sorrendben, és a forgalmat a rendszer kéri az első szabály, amely megfelel a sajátlagossága figyelembe vétele függetlenül keresztül. Például ha egy szabályt egy alapszintű figyelő és egy többhelyes figyelő mindkét ugyanazt a portot használó szabály, a szabály a többhelyes figyelőjével szerepelnie kell a szabály a vártnak megfelelően működik az alapvető figyelő ahhoz, hogy a többhelyes szabály előtt.

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

Az Alkalmazásátjáró létrehozásához szükséges lépéseket a következők:

1. Egy erőforráscsoport létrehozása a Resource Manager számára.
2. Hozzon létre egy virtuális hálózatot, alhálózatok és az alkalmazás átjáró nyilvános IP-cím.
3. Egy Application Gateway konfigurációs objektum létrehozása.
4. Egy Application Gateway erőforrás létrehozása.

## <a name="create-a-resource-group-for-resource-manager"></a>Erőforráscsoport létrehozása a Resource Managerhez

Ügyeljen arra, hogy az Azure PowerShell legújabb verzióját használja. További információt [Windows PowerShell használata a Resource Manager](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>1. lépés

Jelentkezzen be az Azure-ba

```powershell
Login-AzureRmAccount
```
A rendszer kérni fogja a hitelesítő adatokkal történő hitelesítést.

### <a name="step-2"></a>2. lépés

Keresse meg a fiókot az előfizetésekben.

```powershell
Get-AzureRmSubscription
```

### <a name="step-3"></a>3. lépés

Válassza ki, hogy melyik Azure előfizetést fogja használni.

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>4. lépés

Hozzon létre egy erőforráscsoportot (hagyja ki ezt a lépést, ha egy meglévő erőforráscsoportot használ).

```powershell
New-AzureRmResourceGroup -Name appgw-RG -location "West US"
```

Másik lehetőségként az Alkalmazásátjáró erőforráscsoport címkéket is létrehozhat:

```powershell
$resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US" -Tags @{Name = "testtag"; Value = "Application Gateway multiple site"}
```

Az Azure Resource Manager megköveteli, hogy minden erőforráscsoport megadjon egy helyet. Ez a hely lesz az erőforráscsoport erőforrásainak alapértelmezett helye. Győződjön meg arról, hogy minden parancs Alkalmazásátjáró létrehozása ugyanabban az erőforráscsoportban.

A fenti példában létrehozott nevű erőforráscsoport **appgw-RG** a hellyel rendelkező **USA nyugati régiója**.

> [!NOTE]
> Ha egy egyéni mintát kell konfigurálnia az Application Gateway számára: [Application Gateway létrehozása egyéni mintákkal a PowerShell használatával](application-gateway-create-probe-ps.md). Látogasson el [egyéni mintavételt és az állapotfigyelés](application-gateway-probe-overview.md) további információt.

## <a name="create-a-virtual-network-and-subnets"></a>Hozzon létre egy virtuális hálózatot és alhálózatok

Az alábbi példa bemutatja, hogyan hozhat létre virtuális hálózatot a Resource Manager használatával. Két alhálózat ebben a lépésben jönnek létre. Az első alhálózat van az Alkalmazásátjáró magát. Alkalmazásátjáró saját alhálózatba a példányok tárolásához szükséges. Csak más alkalmazásátjárót alhálózat is telepíthető. A második alhálózat alkalmazás háttérkiszolgálók tárolására szolgál.

### <a name="step-1"></a>1. lépés

Rendelje hozzá a cím tartomány 10.0.0.0/24 alhálózat változó az Alkalmazásátjáró tárolására használandó.

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name appgatewaysubnet -AddressPrefix 10.0.0.0/24
```
### <a name="step-2"></a>2. lépés

Rendelje hozzá a cím tartomány 10.0.1.0/24 a háttérkészlet használandó Alhalozat_2 változó.

```powershell
$subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name backendsubnet -AddressPrefix 10.0.1.0/24
```

### <a name="step-3"></a>3. lépés

Hozzon létre egy virtuális hálózatot nevű **appgwvnet** erőforráscsoportban **appgw-rg** az USA nyugati régiója régió a előtag 10.0.0.0/16 használata a 10.0.0.0/24 alhálózat, és 10.0.1.0/24.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet,$subnet2
```

### <a name="step-4"></a>4. lépés

Rendelje hozzá egy olyan átjárót hoz létre, amely alhálózati változó a következő lépéseket.

```powershell
$appgatewaysubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name appgatewaysubnet -VirtualNetwork $vnet
$backendsubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name backendsubnet -VirtualNetwork $vnet
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Nyilvános IP-cím létrehozása az előtérbeli konfigurációhoz

Hozzon létre egy **publicIP01** nevű, nyilvános IP-címhez tartozó erőforrást az **appgw-rg** nevű erőforráscsoportban, az USA nyugati régiójában.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

Amikor a szolgáltatás elindul, egy IP-cím lesz kiosztva az Application Gatewaynek.

## <a name="create-application-gateway-configuration"></a>Alkalmazás átjáró-konfiguráció létrehozása

Az Application Gateway létrehozása előtt minden konfigurációs elemet be kell állítania. Az alábbi lépések létrehozzák az Application Gateway erőforráshoz szükséges konfigurációs elemeket.

### <a name="step-1"></a>1. lépés

Hozzon létre egy **gatewayIP01** nevű Application Gateway IP-konfigurációt. Alkalmazásátjáró indításakor szerzi be a konfigurált alhálózat IP-címeit és hálózati forgalom átirányítása a háttér IP-címkészlet IP-címek. Ne feledje, hogy minden példány egy IP-címet vesz fel.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $appgatewaysubnet
```

### <a name="step-2"></a>2. lépés

Konfigurálja a háttér IP-címkészletet nevű **pool01** és **pool2** IP-címekkel rendelkező **134.170.185.46**, **134.170.188.221**, **134.170.185.50** a **pool1** és **134.170.186.46**, **134.170.189.221**, **134.170.186.50** a **pool2**.

```powershell
$pool1 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 10.0.1.100, 10.0.1.101, 10.0.1.102
$pool2 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool02 -BackendIPAddresses 10.0.1.103, 10.0.1.104, 10.0.1.105
```

Ebben a példában azonban két háttér-készletek a hálózati forgalmat a kért hely alapján. Egy készlet a "contoso.com" hely származó forgalmat megkapja és egyéb készlet hely "fabrikam.com" származó forgalmat megkapja. Cserélje le a fenti IP-címek hozzáadása a saját alkalmazás IP-cím végpontokat kell. Belső IP-címek, helyett is használhatja nyilvános IP-címek, FQDN vagy a virtuális gép hálózati háttér-példányok. A PowerShell használata IP-cím helyett teljes tartományneveket adja meg "-BackendFQDNs" paraméter.

### <a name="step-3"></a>3. lépés

Alkalmazásbeállítás átjáró konfigurálása **poolsetting01** és **poolsetting02** az elosztott terhelésű hálózati forgalmat a háttér-készletben. Ebben a példában a háttér-készletek különböző háttér-készlet beállításait konfigurálja. Mindegyik háttérkészlet saját háttérkészlet-beállításokkal rendelkezhet.

```powershell
$poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120
$poolSetting02 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting02" -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 240
```

### <a name="step-4"></a>4. lépés

Konfigurálja az előtérbeli IP-portot egy nyilvános IP-címvégponttal.

```powershell
$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-5"></a>5. lépés

Konfigurálja az előtérbeli portot egy Application Gatewayhez.

```powershell
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "fep01" -Port 443
```

### <a name="step-6"></a>6. lépés

Konfigurálja a két SSL-tanúsítványok esetében a két webhely támogatja az ebben a példában fogjuk. Egy tanúsítvány a contoso.com forgalmat, a másik fabrikam.com forgalom. Ezeket a tanúsítványokat a webhelyeknek a tanúsítványokat kiadó hitelesítésszolgáltató kell lennie. Önaláírt tanúsítványok támogatottak, de nem javasolt éles forgalom.

```powershell
$cert01 = New-AzureRmApplicationGatewaySslCertificate -Name contosocert -CertificateFile <file path> -Password <password>
$cert02 = New-AzureRmApplicationGatewaySslCertificate -Name fabrikamcert -CertificateFile <file path> -Password <password>
```

### <a name="step-7"></a>7. lépés

Ebben a példában két figyelők a két webhelyek konfigurálása Ez a lépés konfigurál a nyilvános IP-cím, port és a bejövő forgalom fogadására szolgáló gazdagép figyelők. Állomásnév paraméter több webhely támogatásához szükséges, és jelölje meg a megfelelő webhelyen, amelynek a forgalom érkezik. RequireServerNameIndication paraméter meg igaz a webhelyeket, amelyeket a támogatásra van szüksége az SSL több állomás esetén. Ha SSL támogatására szükség, akkor is meg kell adnia az SSL-tanúsítvány, amely védi a webalkalmazáshoz tartozó forgalmat a. A FrontendIPConfiguration FrontendPort és állomásnév kombinációja egy figyelő egyedinek kell lennie. Minden egyes figyelő több tanúsítvány is támogatja.

```powershell
$listener01 = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol Https -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -HostName "contoso11.com" -RequireServerNameIndication true  -SslCertificate $cert01
$listener02 = New-AzureRmApplicationGatewayHttpListener -Name "listener02" -Protocol Https -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -HostName "fabrikam11.com" -RequireServerNameIndication true -SslCertificate $cert02
```

### <a name="step-8"></a>8. lépés

Hozzon létre két szabály beállítást a két webes alkalmazásokhoz, ebben a példában. Egy szabály kötelékek együtt, figyelők, a háttérkészlet és a HTTP-beállításait. Ez a lépés konfigurál az Alkalmazásátjáró alapvető útválasztási szabály, egy minden webhelyre vonatkozóan. Minden webhelyre irányuló forgalom érkezik a beállított figyelő, és a beállított háttérkészlet, a backendhttpsettings beállítások megadott tulajdonságok a rendszer ekkor átirányítja.

```powershell
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule01" -RuleType Basic -HttpListener $listener01 -BackendHttpSettings $poolSetting01 -BackendAddressPool $pool1
$rule02 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule02" -RuleType Basic -HttpListener $listener02 -BackendHttpSettings $poolSetting02 -BackendAddressPool $pool2
```

### <a name="step-9"></a>9. lépés

Konfigurálja az Application Gatewayben a példányok számát és a méretet.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name "Standard_Medium" -Tier Standard -Capacity 2
```

## <a name="create-application-gateway"></a>Alkalmazásátjáró létrehozása

Hozzon létre egy alkalmazást az előző lépéseket az összes konfigurációs objektumok.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-RG -Location "West US" -BackendAddressPools $pool1,$pool2 -BackendHttpSettingsCollection $poolSetting01, $poolSetting02 -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener01, $listener02 -RequestRoutingRules $rule01, $rule02 -Sku $sku -SslCertificates $cert01, $cert02
```

> [!IMPORTANT]
> Alkalmazás átjáró kiépítése hosszú ideig futó művelet, és eltarthat egy ideig.
> 
> 

## <a name="get-application-gateway-dns-name"></a>Az Application Gateway DNS-nevének beszerzése

Az átjáró létrehozása után a következő lépés a kommunikációra szolgáló előtér konfigurálása. Nyilvános IP-cím esetén az Application Gateway használatához dinamikusan hozzárendelt DNS-névre van szükség, amely nem valódi név. Ha szeretné, hogy a végfelhasználók elérjék az Application Gatewayt, használjon egy Application Gateway nyilvános végpontjára mutató CNAME-rekordot. [Egyéni tartománynév konfigurálása az Azure-ban](../cloud-services/cloud-services-custom-domain-name-portal.md). A művelet végrehajtásához az Application Gateway részleteinek beszerzésére és a kapcsolódó IP/DNS-név lekérésére van szükség az Application Gatewayhez csatolt PublicIPAddress használatával. Az Application Gateway DNS-nevének használatával létrehozhat egy CNAME rekordot, amely a két webalkalmazást erre a DNS-névre irányítja. Az A-bejegyzések használata nem javasolt, mivel a virtuális IP-cím változhat az Application Gateway újraindításakor.

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

Ismerje meg, hogyan védi meg a webhelyek [Application Gateway - webalkalmazási tűzfal](application-gateway-webapplicationfirewall-overview.md)

