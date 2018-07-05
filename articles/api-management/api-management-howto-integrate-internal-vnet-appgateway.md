---
title: Az Azure API Management használata virtuális hálózatban, az Application Gatewayen |} A Microsoft Docs
description: Ismerje meg, hogyan telepítse és konfigurálja a belső virtuális hálózathoz az Application Gateway (WAF) előtérbeli, az Azure API Management
services: api-management
documentationcenter: ''
author: solankisamir
manager: kjoshi
editor: vlvinogr
ms.assetid: a8c982b2-bca5-4312-9367-4a0bbc1082b1
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: sasolank
ms.openlocfilehash: c7d4351a9691c9787c42107306220e075f8648a0
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37435123"
---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>Integrálása belső vnet-en az API Management az Application Gateway segítségével

##<a name="overview"> </a> – Áttekintés

Az API Management szolgáltatás konfigurálható a belső üzemmódban, ami lehetővé teszi a virtuális hálózaton belül csak az elérhető virtuális hálózatban. Az Azure Application Gateway-példány egy PAAS szolgáltatás, amely biztosít egy 7. rétegbeli terheléselosztó. Azt proxykiszolgálói szolgáltatásként funkcionál, és biztosít az ajánlat egy webalkalmazási tűzfal (WAF) között.

Belső vnet-en az Application Gateway frontend-üzembe helyezett API Management kombinálásával lehetővé teszi, hogy a következő esetekben:

* Használhatja ugyanazt az API Management erőforrást belső fogyasztók és a külső felhasználók által történő használatra.
* Az API Management egy erőforrást használnak, és a külső fogyasztók számára elérhető API Management szolgáltatásban meghatározott API-t.
* Átállás a hozzáférés és az API Management a nyilvános interneten kapcsolja ki kulcsrakész lehetőséget biztosíthat.

## <a name="prerequisites"></a>Előfeltételek

Ebben a cikkben leírt lépések végrehajtásához rendelkeznie:

+ Aktív Azure-előfizetés.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ APIM-példány. További információkért lásd: [Azure API Management szolgáltatáspéldány létrehozása](get-started-create-service-instance.md).

##<a name="scenario"> </a> A forgatókönyv
Ez a cikk bemutatja, hogyan használhatja a külső és belső fogyasztók számára egy egy API Management-szolgáltatás, és lehetővé teszi szerepét egy egységes előtérrendszer mind a helyszíni és felhőalapú API-k. Hogyan teszi közzé az API-k (a példában kiemelve jelennek meg a zöld) csak egy része külső felhasználásra a PathBasedRouting érhető el az Application Gateway szolgáltatással is megjelenik.

Az első telepítő minden API-felügyelt csak a virtuális hálózaton belül. Belső fogyasztók számára (kiemelve a narancssárga) férhetnek hozzá az összes belső és külső API-k. Forgalom soha nem kerül ki internetes kézbesíti a rendszer a nagy teljesítményű Expressroute-Kapcsolatcsoportok keresztül.

![URL-útvonal](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"> </a> A telepítés megkezdése előtt

1. Telepítse az Azure PowerShell-parancsmagok legújabb verzióját a Webplatform-telepítővel. A [Letöltések lap](https://azure.microsoft.com/downloads/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) **Windows PowerShell** szakaszából letöltheti és telepítheti a legújabb verziót.
2. Hozzon létre egy virtuális hálózatot, és hozzon létre különálló alhálózatokat az API Management és az Application Gateway számára.
3. Ha szeretne létrehozni egy egyéni DNS-kiszolgáló a virtuális hálózat, ehhez az üzembe helyezés megkezdése előtt. Úgy, hogy létrehozott egy új alhálózatot a virtuális hálózatban lévő virtuális gép működik, ellenőrizze megoldhatja és az összes Azure-szolgáltatás végpontjainak eléréséhez.

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>Mi szükséges egy API Management és az Application Gateway közötti integrációt létrehozni?

* **Háttér-kiszolgálókészlet:** Ez az API Management szolgáltatás belső virtuális IP-címe.
* **Háttér-kiszolgálókészlet beállításai:** Minden készletnek vannak beállításai, például port, protokoll vagy cookie-alapú affinitás. Ezek a beállítások a készlet minden kiszolgálójára érvényesek.
* **Előtérbeli port:** Ez az, hogy az application gateway-en megnyitott nyilvános port. Szerezze meg a, forgalom átirányítja a háttér-kiszolgálók valamelyikével.
* **Figyelő:** A figyelő egy előtérbeli porttal, egy protokollal (Http vagy Https, a kis- és a nagybetűk megkülönböztetésével) és SSL tanúsítványnévvel rendelkezik (SSL-kiszervezés konfigurálásakor).
* **Szabály:** a szabály köti a figyelőt egy háttér-kiszolgálókészlethez.
* **Egyéni állapotadat-mintavétel:** Application Gateway alapértelmezés szerint segítségével IP-cím alapú mintavételek döntse el, hogy mely kiszolgálók a értékre aktívak. Az API Management szolgáltatás csak válaszol a kérelmekre, amelyek rendelkeznek a megfelelő állomásfejlécet, ezért az alapértelmezett mintavételek sikertelen. Egyéni állapotmintát kell definiálni, határozza meg, hogy a szolgáltatás működik-e, és továbbítsa a kérelmek az application gateway segítségével.
* **Egyéni tartományi tanúsítvány:** eléréséhez az API Management az internetről szeretne létrehozni egy CNAME-leképezés, az állomásnév az Application Gateway előtér DNS-nevével. Ez biztosítja, hogy a hostname fejléc és az Application Gateway és az API Management továbbított küldött tanúsítvány egy APIM felismerje, érvényes.

## <a name="overview-steps"> </a> Az API Management és az Application Gateway való integrálásához szükséges lépéseket

1. Egy erőforráscsoport létrehozása a Resource Manager számára.
2. Hozzon létre egy virtuális hálózat, alhálózat és nyilvános IP-Címek az Application Gateway számára. Hozzon létre egy másik alhálózatot az API Management szolgáltatáshoz.
3. A fent létrehozott virtuális hálózat alhálózat belül az API Management szolgáltatás létrehozása, és győződjön meg arról, a belső módot használja.
4. Az API Management szolgáltatásban az egyéni tartománynév beállítása.
5. Hozzon létre egy Application Gateway konfigurációs objektumhoz.
6. Hozzon létre egy Application Gateway-erőforrás.
7. Hozzon létre egy CNAME rekord, az API Management proxyeszköznév az Application Gateway nyilvános DNS-nevét.

## <a name="create-a-resource-group-for-resource-manager"></a>Erőforráscsoport létrehozása a Resource Managerhez

Ügyeljen arra, hogy az Azure PowerShell legújabb verzióját használja. További információ: [A Windows PowerShell használata a Resource Managerrel](https://docs.microsoft.com/azure/azure-resource-manager/powershell-azure-resource-manager).

### <a name="step-1"></a>1. lépés

Jelentkezzen be az Azure-ba

```powershell
Connect-AzureRmAccount
```

Hitelesíti a hitelesítő adataival.<BR>

### <a name="step-2"></a>2. lépés

Ellenőrizze a fiók előfizetéseit, és válassza ki azt.

```powershell
Get-AzureRmSubscription -Subscriptionid "GUID of subscription" | Select-AzureRmSubscription
```

### <a name="step-3"></a>3. lépés

Hozzon létre egy erőforráscsoportot (hagyja ki ezt a lépést, ha egy meglévő erőforráscsoportot használ).

```powershell
New-AzureRmResourceGroup -Name "apim-appGw-RG" -Location "West US"
```
Az Azure Resource Manager megköveteli, hogy minden erőforráscsoport megadjon egy helyet. Ez szolgál az erőforráscsoport erőforrásainak alapértelmezett helyeként. Győződjön meg arról, hogy az application gateway létrehozására irányuló összes parancs ugyanazt az erőforráscsoportot.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Hozzon létre egy virtuális hálózatot és egy alhálózatot az application gateway számára

Az alábbi példa bemutatja, hogyan hozhat létre virtuális hálózatot használatával a resource manager.

### <a name="step-1"></a>1. lépés

A 10.0.0.0/24 címtartományt rendelni az Application Gateway egy virtuális hálózat létrehozásakor használni kívánt alhálózati változóhoz.

```powershell
$appgatewaysubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "apim01" -AddressPrefix "10.0.0.0/24"
```

### <a name="step-2"></a>2. lépés

A cím-tartományt 10.0.1.0/24 hozzárendelése a virtuális hálózat létrehozásakor az API Management szolgáltatáshoz használni kívánt alhálózati változóhoz.

```powershell
$apimsubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "apim02" -AddressPrefix "10.0.1.0/24"
```

### <a name="step-3"></a>3. lépés

Nevű virtuális hálózat létrehozása **appgwvnet** erőforráscsoportban **apim-appGw-RG** esetében az USA nyugati régiója, az előtag 10.0.0.0/16 használja a 10.0.0.0/24 alhálózat és 10.0.1.0/24.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name "appgwvnet" -ResourceGroupName "apim-appGw-RG" -Location "West US" -AddressPrefix "10.0.0.0/16" -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>4. lépés

Rendelje hozzá egy alhálózati változót a következő lépések

```powershell
$appgatewaysubnetdata=$vnet.Subnets[0]
$apimsubnetdata=$vnet.Subnets[1]
```
## <a name="create-an-api-management-service-inside-a-vnet-configured-in-internal-mode"></a>Egy belső módban konfigurált virtuális hálózaton belül az API Management szolgáltatás létrehozása

Az alábbi példa bemutatja, hogyan hozhat létre API Management szolgáltatás csak a belső hozzáférés konfigurált virtuális hálózaton.

### <a name="step-1"></a>1. lépés
Hozzon létre egy API Management virtuális hálózat objektumot, a fent létrehozott $apimsubnetdata alhálózat használatával.

```powershell
$apimVirtualNetwork = New-AzureRmApiManagementVirtualNetwork -Location "West US" -SubnetResourceId $apimsubnetdata.Id
```
### <a name="step-2"></a>2. lépés
Hozzon létre egy API Management szolgáltatás a virtuális hálózaton belül.

```powershell
$apimService = New-AzureRmApiManagement -ResourceGroupName "apim-appGw-RG" -Location "West US" -Name "ContosoApi" -Organization "Contoso" -AdminEmail "admin@contoso.com" -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Developer"
```
Lásd a fenti parancs sikeres követően [belső virtuális hálózathoz az API Management szolgáltatáshoz való hozzáféréshez szükséges DNS-konfiguráció](api-management-using-with-internal-vnet.md#apim-dns-configuration) az eléréséhez.

## <a name="set-up-a-custom-domain-name-in-api-management"></a>Az API Management egy egyéni tartománynév beállítása

### <a name="step-1"></a>1. lépés
Töltse fel a tanúsítványt a tartományhoz tartozó titkos kulccsal. Ebben a példában lesz `*.contoso.net`.

```powershell
$certUploadResult = Import-AzureRmApiManagementHostnameCertificate -ResourceGroupName "apim-appGw-RG" -Name "ContosoApi" -HostnameType "Proxy" -PfxPath <full path to .pfx file> -PfxPassword <password for certificate file> -PassThru
```

### <a name="step-2"></a>2. lépés
Miután feltöltötte a tanúsítványt, hozzon létre egy állomásnév konfigurációs objektumot a proxy az állomásnévvel rendelkező `api.contoso.net`, ahogy a példában tanúsítvány lehetővé teszi a szolgáltató a `*.contoso.net` tartományhoz.

```powershell
$proxyHostnameConfig = New-AzureRmApiManagementHostnameConfiguration -CertificateThumbprint $certUploadResult.Thumbprint -Hostname "api.contoso.net"
$result = Set-AzureRmApiManagementHostnames -Name "ContosoApi" -ResourceGroupName "apim-appGw-RG" -ProxyHostnameConfiguration $proxyHostnameConfig
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Nyilvános IP-cím létrehozása az előtérbeli konfigurációhoz

Hozzon létre egy nyilvános IP-erőforrást **publicIP01** erőforráscsoportban **apim-appGw-RG** az USA nyugati RÉGIÓJA számára.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName "apim-appGw-RG" -name "publicIP01" -location "West US" -AllocationMethod Dynamic
```

Amikor a szolgáltatás elindul, egy IP-cím lesz kiosztva az Application Gatewaynek.

## <a name="create-application-gateway-configuration"></a>Hozzon létre az application gateway-konfigurációt

Az Application Gateway létrehozása előtt minden konfigurációs elemet be kell állítani. Az alábbi lépések létrehozzák az Application Gateway erőforráshoz szükséges konfigurációs elemeket.

### <a name="step-1"></a>1. lépés

Hozzon létre egy **gatewayIP01** nevű Application Gateway IP-konfigurációt. Amikor az Application Gateway elindul, a konfigurált alhálózatból felvesz egy IP-címet, és a hálózati forgalmat a háttérbeli IP-készlet IP-címeihez irányítja. Ne feledje, hogy minden példány egy IP-címet vesz fel.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name "gatewayIP01" -Subnet $appgatewaysubnetdata
```

### <a name="step-2"></a>2. lépés

Konfigurálja az előtérbeli IP-portot a nyilvános IP-cím végponthoz. A port a port, amelyet a végfelhasználók csatlakozni.

```powershell
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "port01"  -Port 443
```
### <a name="step-3"></a>3. lépés

Konfigurálja az előtérbeli IP-portot egy nyilvános IP-címvégponttal.

```powershell
$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>4. lépés

A tanúsítvány konfigurálása az Application Gateway, áthaladó forgalom újbóli titkosítására és visszafejtésére használt.

```powershell
$cert = New-AzureRmApplicationGatewaySslCertificate -Name "cert01" -CertificateFile <full path to .pfx file> -Password <password for certificate file>
```

### <a name="step-5"></a>5. lépés

Hozzon létre a HTTP-figyelő az Application Gateway számára. Előtérbeli IP konfigurációja, port és az ssl tanúsítvány rendelje hozzá.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert
```

### <a name="step-6"></a>6. lépés

Az API Management szolgáltatásban való egyéni mintavétel létrehozása `ContosoApi` proxy tartomány végpont. Az elérési út `/status-0123456789abcdef` egy alapértelmezett egészségügyi végpont az API Management-szolgáltatásokban üzemeltetett. Állítsa be `api.contoso.net` , egy egyéni mintát állomásnév biztonságossá tételéhez, SSL-tanúsítvánnyal.

> [!NOTE]
> A hostname `contosoapi.azure-api.net` konfigurálva, amikor egy elnevezett szolgáltatás az alapértelmezett proxyeszköznév `contosoapi` jön létre a nyilvános Azure-ban.
>

```powershell
$apimprobe = New-AzureRmApplicationGatewayProbeConfig -Name "apimproxyprobe" -Protocol "Https" -HostName "api.contoso.net" -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
```

### <a name="step-7"></a>7. lépés

Töltse fel a tanúsítványt az SSL-kompatibilis háttérerőforrásokhoz készletben kell használni. Ez az a 4. lépésben megadott ugyanazt a tanúsítványt.

```powershell
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name "whitelistcert1" -CertificateFile <full path to .cer file>
```

### <a name="step-8"></a>8. lépés

Konfigurálja a HTTP-háttérbeállítások az Application Gateway számára. Ide tartoznak a háttérrendszer kérelem megszakadt utána időtúllépési korlát. Ez az érték eltér a mintavétel időkorlátja.

```powershell
$apimPoolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "apimPoolSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>9. lépés

A háttérbeli IP-címkészletet konfigurálja **apimbackend** belső virtuális IP-címe az API Management szolgáltatás a fent létrehozott.

```powershell
$apimProxyBackendPool = New-AzureRmApplicationGatewayBackendAddressPool -Name "apimbackend" -BackendIPAddresses $apimService.StaticIPs[0]
```

### <a name="step-10"></a>10. lépés

Hozzon létre egy üres (nem létező) háttérrendszer beállításait. Kérelmek, hogy nem szeretne közzétenni az API-felügyelet az Application Gateway API elérési nyomja le a háttér, és a 404-es adja vissza.

A helyőrző háttérrendszer HTTP beállításainak konfigurálása.

```powershell
$dummyBackendSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "dummySetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

Konfigurálja egy helyőrző háttérrendszer **dummyBackendPool**, amely egy FQDN cím mutat **dummybackend.com**. Az FQDN-cím nem létezik a virtuális hálózatban.

```powershell
$dummyBackendPool = New-AzureRmApplicationGatewayBackendAddressPool -Name "dummyBackendPool" -BackendFqdns "dummybackend.com"
```

Hozzon létre egy szabályt a beállítás, amelyet az Application Gateway fog használni, amely a nem létező háttérrendszer alapértelmezés szerint **dummybackend.com** a virtuális hálózatban.

```powershell
$dummyPathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "nonexistentapis" -Paths "/*" -BackendAddressPool $dummyBackendPool -BackendHttpSettings $dummyBackendSetting
```

### <a name="step-11"></a>11. lépés

Konfigurálja a háttérkészlet URL-cím szabály útvonalai. Ez lehetővé teszi, hogy válassza ki az API-k közül csak az API Management nyilvánosan elérhetővé váljon. Például, ha nincsenek `Echo API` (/ echo /) `Calculator API` (/calc/ stb.) ügyeljen csak `Echo API` interneten).

A következő példában létrehozunk egy egyszerű szabályt a "/ echo /" elérési út útválasztási forgalmat a háttérbeli "apimProxyBackendPool".

```powershell
$echoapiRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "externalapis" -Paths "/echo/*" -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting
```

Az elérési út nem egyezik az elérésiút-szabályok az API Management szeretnénk, ha az elérési út térkép szabálykonfiguráció is konfigurálja egy alapértelmezett háttér-címkészletet **dummyBackendPool**. Ha például http://api.contoso.net/calc/sum kerül **dummyBackendPool** , az alapértelmezett címkészlet nem egyező forgalom meghatározott.

```powershell
$urlPathMap = New-AzureRmApplicationGatewayUrlPathMapConfig -Name "urlpathmap" -PathRules $echoapiRule, $dummyPathRule -DefaultBackendAddressPool $dummyBackendPool -DefaultBackendHttpSettings $dummyBackendSetting
```

A fenti lépés biztosítja, hogy csak az elérési út vonatkozó kérések "/ echo" használata engedélyezett az Application Gatewayen keresztül. Az Application Gateway, ha elérhető az internetről érkező kérelmek más API-khoz, az API Management szolgáltatásban konfigurált 404-es hibát kivételt fogja kijelezni.

### <a name="step-12"></a>12. lépés

Hozzon létre egy szabály beállítása az Application Gateway URL-cím-alapú útválasztás használatához.

```powershell
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType PathBasedRouting -HttpListener $listener -UrlPathMap $urlPathMap
```

### <a name="step-13"></a>13. lépés

Az Application Gateway konfigurálása a példányok számát és méretét. Itt a [WAF Termékváltozatban](../application-gateway/application-gateway-webapplicationfirewall-overview.md) az API Management-erőforrás a biztonság fokozása érdekében.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name "WAF_Medium" -Tier "WAF" -Capacity 2
```

### <a name="step-14"></a>14. lépés

WAF "Megelőzési" módban kell konfigurálni.
```powershell
$config = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>Az Application Gateway létrehozása

Hozzon létre egy Application Gateway az előző lépésekből származó összes konfigurációs objektumot.

```powershell
$appgw = New-AzureRmApplicationGateway -Name $applicationGatewayName -ResourceGroupName $resourceGroupName  -Location $location -BackendAddressPools $apimProxyBackendPool, $dummyBackendPool -BackendHttpSettingsCollection $apimPoolSetting, $dummyBackendSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener -UrlPathMaps $urlPathMap -RequestRoutingRules $rule01 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert -Probes $apimprobe
```

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>Az Application Gateway erőforrás nyilvános DNS-nevet, az API Management proxyeszköznév CNAME

Az átjáró létrehozása után a következő lépés a kommunikációra szolgáló előtér konfigurálása. Nyilvános IP-cím használata esetén az Application Gateway egy dinamikusan hozzárendelt DNS-név, amely nem lehet könnyen használható van szükség.

Az Application Gateway DNS-nevet használjon, hozzon létre egy CNAME rekordot, amely az APIM-proxy állomás neve (Példa: `api.contoso.net` a fenti példákban) a DNS-névhez. Konfigurálja az előtérbeli IP-CNAME-rekordot, hogy az Application Gateway és a kapcsolódó IP/DNS-név publicipaddress használatával adatai olvashatók be. A-bejegyzések használata nem ajánlott, mivel a virtuális IP-cím változhat az átjáró újraindítása.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName "apim-appGw-RG" -Name "publicIP01"
```

##<a name="summary"> </a> Összefoglalás
Egy virtuális hálózatban konfigurálva az Azure API Management egyetlen átjáró felületet biztosít az összes konfigurált API-k, attól üzemeltetett helyszíni vagy a felhőben. Az Application Gateway integrálása az API Management biztosít szelektív engedélyezésével az adott API-kat az interneten érhető el, valamint a webalkalmazási tűzfal biztosít az API Management-példány egy előtérkiszolgáló, rugalmasságát.

##<a name="next-steps"> </a> Következő lépések
* További információ az Azure Application Gateway
  * [Application Gateway áttekintése](../application-gateway/application-gateway-introduction.md)
  * [Application Gateway webalkalmazási tűzfal](../application-gateway/application-gateway-webapplicationfirewall-overview.md)
  * [Az Application Gateway-alapú útválasztás használatával](../application-gateway/application-gateway-create-url-route-arm-ps.md)
* További információ az API Management és a virtuális hálózatok
  * [Csak a virtuális hálózaton belül elérhető API Management használatával](api-management-using-with-internal-vnet.md)
  * [Az API Management használatával a virtuális hálózaton](api-management-using-with-vnet.md)
