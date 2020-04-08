---
title: Az API Management használata a virtuális hálózatban az Application Gateway segítségével
titleSuffix: Azure API Management
description: Ismerje meg, hogyan állíthatja be és konfigurálhatja az Azure API Managementet a belső virtuális hálózaton az Application Gateway (WAF) segítségével FrontEnd néven
services: api-management
documentationcenter: ''
author: solankisamir
manager: kjoshi
editor: vlvinogr
ms.assetid: a8c982b2-bca5-4312-9367-4a0bbc1082b1
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: sasolank
ms.openlocfilehash: 733f4b74ca7643476586189b36f4e1d3e446968b
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811173"
---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>Az API Management integrálása belső virtuális hálózatba az Application Gateway alkalmazásátjáróval

## <a name="overview"></a><a name="overview"> </a> Áttekintés

Az API Management szolgáltatás konfigurálható egy virtuális hálózat belső módban, amely lehetővé teszi, hogy csak a virtuális hálózaton belül. Az Azure Application Gateway egy PAAS-szolgáltatás, amely egy Layer-7 terheléselosztót biztosít. Úgy működik, mint egy fordított proxy szolgáltatást, és többek között kínál a Web Application Firewall (WAF).

A belső virtuális hálózatban kiépített API Management és az Application Gateway előtér kombinálása a következő forgatókönyveket teszi lehetővé:

* Használja ugyanazt az API Management erőforrást a belső és a külső fogyasztók általi felhasználáshoz.
* Használjon egyetlen API Management-erőforrást, és az API-k egy részhalmazát az API Management ben definiálva a külső fogyasztók számára elérhetővé tegye.
* Kulcsrakész módon válthat az API Management szolgáltatáshoz való hozzáférésre a nyilvános internetről be- és kikapcsolva.

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A cikkben ismertetett lépések végrehajtásához a következőkre van szüksége:

* Aktív Azure-előfizetés.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

* Tanúsítványok - pfx és cer az API hostname és pfx a fejlesztői portál hostname.

## <a name="scenario"></a><a name="scenario"> </a> Forgatókönyv

Ez a cikk bemutatja, hogyan használhatja az egyetlen API-felügyeleti szolgáltatást mind a belső, mind a külső felhasználók számára, és egyetlen előtérként működhet mind a helyszíni, mind a felhőbeli API-k számára. Azt is láthatja, hogyan teheti elérhetővé az API-k csak egy részét (a példában a zöld színnel kiemelve) a külső felhasználás hoz elérhető útválasztási funkció val az Application Gateway.You will also see how to expose only a subset of your APIs (in the example they are highlighted in green) for External Consumption using routing functionality available in Application Gateway.

Az első beállítási példában az api-k kezelése csak a virtuális hálózaton belül. A belső fogyasztók (narancssárga színnel kiemelve) hozzáférhetnek az összes belső és külső API-hoz. A forgalom soha nem megy ki az internetre. A nagy teljesítményű kapcsolatot expressz útvonal-áramkörökön keresztül biztosítjuk.

![url útvonal](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"></a><a name="before-you-begin"> </a> Mielőtt elkezdené

* Ügyeljen arra, hogy az Azure PowerShell legújabb verzióját használja. Tekintse meg az [Azure PowerShell telepítése](/powershell/azure/install-az-ps)című telepítési útmutatót. 

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>Mire van szükség az API Management és az Application Gateway közötti integráció létrehozásához?

* **Háttérkiszolgáló-készlet:** Ez az API Management szolgáltatás belső virtuális IP-címe.
* **Háttérkiszolgáló-készlet beállításai:** Minden készlet rendelkezik olyan beállításokkal, mint a port, a protokoll és a cookie-alapú affinitás. Ezek a beállítások a készlet en belüli összes kiszolgálóra vonatkoznak.
* **Előtér-port:** Ez az alkalmazásátjárón megnyitott nyilvános port. Az azt ütő forgalmat átirányítja az egyik háttérkiszolgálóra.
* **Hallgató:** A figyelő rendelkezik egy előtér-porttal, egy protokollal (Http vagy Https, ezek az értékek a kis- és nagybetűket, és a TLS/SSL tanúsítvány neve (a TLS kiszervezés konfigurálása esetén).
* **Szabály:** A szabály egy figyelőt egy háttérkiszolgálókészlethez köt.
* **Egyéni állapotvizsgálat:** Az Application Gateway alapértelmezés szerint IP-címalapú mintavételeket használ annak kiderítésére, hogy a BackendAddressPool mely kiszolgálói aktívak. Az API Management szolgáltatás csak a megfelelő állomásfejléccel rendelkező kérésekre válaszol, ezért az alapértelmezett mintavételek sikertelenek. Egy egyéni állapotminta kell definiálni, hogy az alkalmazásátjáró határozza meg, hogy a szolgáltatás él, és a kérelmek továbbítása.
* **Egyéni tartományi tanúsítványok:** Az API Management internetről való eléréséhez létre kell hoznia az állomásnév CNAME-hozzárendelését az Application Gateway előtér-DNS-nevéhez. Ez biztosítja, hogy az API Managementnek továbbított Application Gateway-nek küldött állomásnév-fejléc és tanúsítvány egy APIM-et érvényesnek ismerhet. Ebben a példában két tanúsítványt fogunk használni - a háttér-és a fejlesztői portálhoz.  

## <a name="steps-required-for-integrating-api-management-and-application-gateway"></a><a name="overview-steps"> </a> Az API-kezelés és az alkalmazásátjáró integrálásához szükséges lépések

1. Egy erőforráscsoport létrehozása a Resource Manager számára.
2. Hozzon létre egy virtuális hálózatot, alhálózatot és nyilvános IP-címet az application gateway számára. Hozzon létre egy másik alhálózatot az API Management számára.
3. Hozzon létre egy API Management szolgáltatást a fent létrehozott virtuálishálózat-alhálózaton belül, és győződjön meg arról, hogy a belső módot használja.
4. Egyéni tartománynév beállítása az API Management szolgáltatásban.
5. Hozzon létre egy Application Gateway konfigurációs objektumot.
6. Hozzon létre egy Application Gateway erőforrást.
7. Hozzon létre egy CNAME nevet az Application Gateway nyilvános DNS-nevéből az API Management proxyállomásnevére.

## <a name="exposing-the-developer-portal-externally-through-application-gateway"></a>A fejlesztői portál külső felfedése az Application Gateway-en keresztül

Ebben az útmutatóban a **fejlesztői portált** az Application Gateway-en keresztül is elérhetővé tesszük a külső közönségek számára. További lépéseket igényel a fejlesztői portál figyelőjének, mintavételének, beállításainak és szabályainak létrehozásához. Minden részletet a megfelelő lépésekben ismertetett.

> [!WARNING]
> Ha Azure AD vagy harmadik fél általi hitelesítést használ, engedélyezze [a cookie-alapú munkamenet-affinitási](../application-gateway/features.md#session-affinity) funkciót az Application Gateway alkalmazásban.

> [!WARNING]
> Ha meg szeretné akadályozni, hogy az Application Gateway WAF megszakítsa az `942200 - "Detects MySQL comment-/space-obfuscated injections and backtick termination"`OpenAPI specifikáció letöltését a fejlesztői portálon, le kell tiltania a tűzfalszabályt.

## <a name="create-a-resource-group-for-resource-manager"></a>Erőforráscsoport létrehozása a Resource Managerhez

### <a name="step-1"></a>1. lépés

Jelentkezzen be az Azure-ba

```powershell
Connect-AzAccount
```

Hitelesítse magát a hitelesítő adataival.

### <a name="step-2"></a>2. lépés

Válassza ki a kívánt előfizetést.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000" # GUID of your Azure subscription
Get-AzSubscription -Subscriptionid $subscriptionId | Select-AzSubscription
```

### <a name="step-3"></a>3. lépés

Hozzon létre egy erőforráscsoportot (hagyja ki ezt a lépést, ha egy meglévő erőforráscsoportot használ).

```powershell
$resGroupName = "apim-appGw-RG" # resource group name
$location = "West US"           # Azure region
New-AzResourceGroup -Name $resGroupName -Location $location
```

Az Azure Resource Manager megköveteli, hogy minden erőforráscsoport adjon meg egy helyet. Ez szolgál az erőforráscsoport erőforrásainak alapértelmezett helyeként. Győződjön meg arról, hogy az alkalmazásátjáró létrehozásához szükséges összes parancs ugyanazt az erőforráscsoportot használja.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Virtuális hálózat és alhálózat létrehozása az alkalmazásátjáróhoz

A következő példa bemutatja, hogyan hozhat létre virtuális hálózatot az Erőforrás-kezelő használatával.

### <a name="step-1"></a>1. lépés

Rendelje hozzá a 10.0.0.0/24 címtartományt az alkalmazásátjáróhoz a virtuális hálózat létrehozása kor használandó alhálózati változóhoz.

```powershell
$appgatewaysubnet = New-AzVirtualNetworkSubnetConfig -Name "apim01" -AddressPrefix "10.0.0.0/24"
```

### <a name="step-2"></a>2. lépés

Rendelje hozzá a 10.0.1.0/24 címtartományt az API-kezeléshez a virtuális hálózat létrehozása kor használandó alhálózati változóhoz.

```powershell
$apimsubnet = New-AzVirtualNetworkSubnetConfig -Name "apim02" -AddressPrefix "10.0.1.0/24"
```

### <a name="step-3"></a>3. lépés

Hozzon létre egy **appgwvnet** nevű virtuális hálózatot **az APIM-appGw-RG** erőforráscsoportban az USA nyugati régiójában. Használja a 10.0.0.0/16 előtagot a 10.0.0.0/24 és a 10.0.1.0/24 alhálózatokkal.

```powershell
$vnet = New-AzVirtualNetwork -Name "appgwvnet" -ResourceGroupName $resGroupName -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>4. lépés

Alhálózati változó hozzárendelése a következő lépésekhez

```powershell
$appgatewaysubnetdata = $vnet.Subnets[0]
$apimsubnetdata = $vnet.Subnets[1]
```

## <a name="create-an-api-management-service-inside-a-vnet-configured-in-internal-mode"></a>API-kezelő szolgáltatás létrehozása belső módban konfigurált virtuális hálózaton belül

A következő példa bemutatja, hogyan hozhat létre egy API Management szolgáltatást egy csak belső hozzáférésre konfigurált virtuális hálózatban.

### <a name="step-1"></a>1. lépés

Hozzon létre egy API-felügyeleti virtuális hálózati objektumot a fent létrehozott $apimsubnetdata alhálózat használatával.

```powershell
$apimVirtualNetwork = New-AzApiManagementVirtualNetwork -SubnetResourceId $apimsubnetdata.Id
```

### <a name="step-2"></a>2. lépés

Hozzon létre egy API Management szolgáltatást a virtuális hálózaton belül.

```powershell
$apimServiceName = "ContosoApi"       # API Management service instance name
$apimOrganization = "Contoso"         # organization name
$apimAdminEmail = "admin@contoso.com" # administrator's email address
$apimService = New-AzApiManagement -ResourceGroupName $resGroupName -Location $location -Name $apimServiceName -Organization $apimOrganization -AdminEmail $apimAdminEmail -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Developer"
```

Miután a fenti parancs sikeres, hivatkozzon a [belső VNET API Management szolgáltatás eléréséhez szükséges DNS-konfigurációra.](api-management-using-with-internal-vnet.md#apim-dns-configuration) Ez a lépés több mint fél órát vehet igénybe.

## <a name="set-up-a-custom-domain-name-in-api-management"></a>Egyéni tartománynév beállítása az API Management ben

> [!IMPORTANT]
> Az [új fejlesztői portálon](api-management-howto-developer-portal.md) az alábbi lépések mellett engedélyezni kell az API Management felügyeleti végponthoz való kapcsolódást is.

### <a name="step-1"></a>1. lépés

Inicializálja a következő változókat a tanúsítványok részleteivel a tartományok személyes kulcsaival. Ebben a példában `api.contoso.net` fogjuk használni, és `portal.contoso.net`.  

```powershell
$gatewayHostname = "api.contoso.net"                 # API gateway host
$portalHostname = "portal.contoso.net"               # API developer portal host
$gatewayCertCerPath = "C:\Users\Contoso\gateway.cer" # full path to api.contoso.net .cer file
$gatewayCertPfxPath = "C:\Users\Contoso\gateway.pfx" # full path to api.contoso.net .pfx file
$portalCertPfxPath = "C:\Users\Contoso\portal.pfx"   # full path to portal.contoso.net .pfx file
$gatewayCertPfxPassword = "certificatePassword123"   # password for api.contoso.net pfx certificate
$portalCertPfxPassword = "certificatePassword123"    # password for portal.contoso.net pfx certificate

$certPwd = ConvertTo-SecureString -String $gatewayCertPfxPassword -AsPlainText -Force
$certPortalPwd = ConvertTo-SecureString -String $portalCertPfxPassword -AsPlainText -Force
```

### <a name="step-2"></a>2. lépés

Hozza létre és állítsa be a proxy és a portál állomásnév konfigurációs objektumait.  

```powershell
$proxyHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $gatewayHostname -HostnameType Proxy -PfxPath $gatewayCertPfxPath -PfxPassword $certPwd
$portalHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $portalHostname -HostnameType DeveloperPortal -PfxPath $portalCertPfxPath -PfxPassword $certPortalPwd

$apimService.ProxyCustomHostnameConfiguration = $proxyHostnameConfig
$apimService.PortalCustomHostnameConfiguration = $portalHostnameConfig
Set-AzApiManagement -InputObject $apimService
```

> [!NOTE]
> Az örökölt fejlesztői portál kapcsolatának `-HostnameType DeveloperPortal` `-HostnameType Portal`konfigurálásához le kell cserélnie a rendszerre.

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Nyilvános IP-cím létrehozása az előtérbeli konfigurációhoz

Nyilvános IP-erőforrás **nyilvánosIP01** létrehozása az erőforráscsoportban.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $resGroupName -name "publicIP01" -location $location -AllocationMethod Dynamic
```

Amikor a szolgáltatás elindul, egy IP-cím lesz kiosztva az Application Gatewaynek.

## <a name="create-application-gateway-configuration"></a>Alkalmazásátjáró-konfiguráció létrehozása

Az Application Gateway létrehozása előtt minden konfigurációs elemet be kell állítani. Az alábbi lépések létrehozzák az Application Gateway erőforráshoz szükséges konfigurációs elemeket.

### <a name="step-1"></a>1. lépés

Hozzon létre egy **gatewayIP01** nevű Application Gateway IP-konfigurációt. Amikor az Application Gateway elindul, a konfigurált alhálózatból felvesz egy IP-címet, és a hálózati forgalmat a háttérbeli IP-készlet IP-címeihez irányítja. Ne feledje, hogy minden példány egy IP-címet vesz fel.

```powershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "gatewayIP01" -Subnet $appgatewaysubnetdata
```

### <a name="step-2"></a>2. lépés

Konfigurálja a nyilvános IP-végpont előtér-IP-portját. Ez a port az a port, amelyhez a végfelhasználók csatlakoznak.

```powershell
$fp01 = New-AzApplicationGatewayFrontendPort -Name "port01"  -Port 443
```

### <a name="step-3"></a>3. lépés

Konfigurálja az előtérbeli IP-portot egy nyilvános IP-címvégponttal.

```powershell
$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>4. lépés

Konfigurálja az alkalmazásátjáró tanúsítványait, amelyek az áthaladó forgalom visszafejtésére és újratitkosítására szolgálnak.

```powershell
$cert = New-AzApplicationGatewaySslCertificate -Name "cert01" -CertificateFile $gatewayCertPfxPath -Password $certPwd
$certPortal = New-AzApplicationGatewaySslCertificate -Name "cert02" -CertificateFile $portalCertPfxPath -Password $certPortalPwd
```

### <a name="step-5"></a>5. lépés

Hozza létre a HTTP-figyelők az application gateway. Rendelje hozzá az előtér-IP-konfigurációt, portot és TLS/SSL-tanúsítványokat.

```powershell
$listener = New-AzApplicationGatewayHttpListener -Name "listener01" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert -HostName $gatewayHostname -RequireServerNameIndication true
$portalListener = New-AzApplicationGatewayHttpListener -Name "listener02" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $certPortal -HostName $portalHostname -RequireServerNameIndication true
```

### <a name="step-6"></a>6. lépés

Hozzon létre egyéni mintavételeket `ContosoApi` az API Management szolgáltatás proxytartomány-végpontjára. Az `/status-0123456789abcdef` elérési út egy alapértelmezett állapotvégpont az összes API Management-szolgáltatásban. Egyéni `api.contoso.net` mintavételi állomásnévként állítsa be a TLS/SSL-tanúsítvánnyal való biztonságossá tétele érdekében.

> [!NOTE]
> A állomásnév `contosoapi.azure-api.net` az alapértelmezett proxy állomásnév `contosoapi` konfigurálva, ha egy névvel ellátott szolgáltatás jön létre a nyilvános Azure-ban.
>

```powershell
$apimprobe = New-AzApplicationGatewayProbeConfig -Name "apimproxyprobe" -Protocol "Https" -HostName $gatewayHostname -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
$apimPortalProbe = New-AzApplicationGatewayProbeConfig -Name "apimportalprobe" -Protocol "Https" -HostName $portalHostname -Path "/signin" -Interval 60 -Timeout 300 -UnhealthyThreshold 8
```

### <a name="step-7"></a>7. lépés

Töltse fel a TLS-kompatibilis háttérkészlet-erőforrásokon használandó tanúsítványt. Ez ugyanaz a tanúsítvány, amelyet a fenti 4.

```powershell
$authcert = New-AzApplicationGatewayAuthenticationCertificate -Name "whitelistcert1" -CertificateFile $gatewayCertCerPath
```

### <a name="step-8"></a>8. lépés

Konfigurálja az Alkalmazásátjáró HTTP-háttérbeállításait. Ez magában foglalja a háttér-kérelem időtúllépési korlátjának beállítását, amely után azok megszakadnak. Ez az érték eltér a mintavételidő-túltöltéstől.

```powershell
$apimPoolSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
$apimPoolPortalSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolPortalSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimPortalProbe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>9. lépés

Konfiguráljon egy **apimbackend** nevű háttér-IP-címkészletet a fent létrehozott API Management szolgáltatás belső virtuális IP-címével.

```powershell
$apimProxyBackendPool = New-AzApplicationGatewayBackendAddressPool -Name "apimbackend" -BackendIPAddresses $apimService.PrivateIPAddresses[0]
```

### <a name="step-10"></a>10. lépés

Hozzon létre szabályokat az Application Gateway számára az alapvető útválasztás használatához.

```powershell
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType Basic -HttpListener $listener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting
$rule02 = New-AzApplicationGatewayRequestRoutingRule -Name "rule2" -RuleType Basic -HttpListener $portalListener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolPortalSetting
```

> [!TIP]
> Módosítsa a -RuleType és az útválasztás, hogy korlátozza a hozzáférést bizonyos oldalain a fejlesztői portál.

### <a name="step-11"></a>11. lépés

Adja meg az Alkalmazásátjáró példányainak számát és méretét. Ebben a példában a [WAF termékváltozatot](../application-gateway/application-gateway-webapplicationfirewall-overview.md) használjuk az API Management erőforrás fokozott biztonsága érdekében.

```powershell
$sku = New-AzApplicationGatewaySku -Name "WAF_Medium" -Tier "WAF" -Capacity 2
```

### <a name="step-12"></a>12. lépés

Állítsa be a WAF-ot "Megelőzés" módban.

```powershell
$config = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>Alkalmazásátjáró létrehozása

Hozzon létre egy alkalmazásátjárót az előző lépések összes konfigurációs objektumával.

```powershell
$appgwName = "apim-app-gw"
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $resGroupName -Location $location -BackendAddressPools $apimProxyBackendPool -BackendHttpSettingsCollection $apimPoolSetting, $apimPoolPortalSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener, $portalListener -RequestRoutingRules $rule01, $rule02 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert, $certPortal -AuthenticationCertificates $authcert -Probes $apimprobe, $apimPortalProbe
```

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>CNAME az API Management proxyállomásneve az Application Gateway erőforrás nyilvános DNS-nevéhez

Az átjáró létrehozása után a következő lépés a kommunikációra szolgáló előtér konfigurálása. Nyilvános IP-cím használata esetén az Application Gateway dinamikusan hozzárendelt DNS-nevet igényel, amely nem feltétlenül használható.

Az Application Gateway DNS-nevét kell használni egy CNAME rekord létrehozásához, amely az `api.contoso.net` APIM-proxyállomás nevét (például a fenti példákban) erre a DNS-névre. Az előtér-IP CNAME rekord konfigurálásához olvassa be az Application Gateway és a hozzá tartozó IP/DNS-név részleteit a PublicIPAddress elem használatával. Az A-rekordok használata nem ajánlott, mivel a VIP az átjáró újraindításakor változhat.

```powershell
Get-AzPublicIpAddress -ResourceGroupName $resGroupName -Name "publicIP01"
```

## <a name="summary"></a><a name="summary"> </a> Összegzés
A virtuális hálózatban konfigurált Azure API Management egyetlen átjárókapcsolatot biztosít az összes konfigurált API-hoz, függetlenül attól, hogy azok a helyszínen vagy a felhőben vannak-e üzemeltetve. Az Application Gateway integrálása az API Management szolgáltatással rugalmasságot biztosít az egyes API-k interneten való elérhetővé tétele szelektív lehetővé tétele érdekében, valamint az API-felügyeleti példány előtérként való webalkalmazás-tűzfal biztosítását.

## <a name="next-steps"></a><a name="next-steps"> </a> Következő lépések
* További információ az Azure Application Gateway alkalmazásról
  * [Alkalmazásátjáró – áttekintés](../application-gateway/application-gateway-introduction.md)
  * [Alkalmazásátjáró webalkalmazás-tűzfala](../application-gateway/application-gateway-webapplicationfirewall-overview.md)
  * [Alkalmazásátjáró útválasztással](../application-gateway/application-gateway-create-url-route-arm-ps.md)
* További információ az API Management ről és a VNET-kről
  * [Az API Management használata csak a virtuális hálózaton belül érhető el](api-management-using-with-internal-vnet.md)
  * [Az API-kezelés használata a virtuális hálózatban](api-management-using-with-vnet.md)
