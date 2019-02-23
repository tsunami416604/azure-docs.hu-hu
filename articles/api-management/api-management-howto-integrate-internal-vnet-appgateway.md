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
ms.date: 06/26/2018
ms.author: sasolank
ms.openlocfilehash: a88ea248f1db90916c5dbcaefc24131eb976e2f6
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56728329"
---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>Integrálása belső vnet-en az API Management az Application Gateway segítségével

## <a name="overview"> </a> – Áttekintés

Az API Management szolgáltatás konfigurálható a belső üzemmódban, ami lehetővé teszi a virtuális hálózaton belül csak az elérhető virtuális hálózatban. Az Azure Application Gateway-példány egy PAAS szolgáltatás, amely biztosít egy 7. rétegbeli terheléselosztó. Azt proxykiszolgálói szolgáltatásként funkcionál, és biztosít az ajánlat egy webalkalmazási tűzfal (WAF) között.

Belső vnet-en az Application Gateway frontend-üzembe helyezett API Management kombinálásával lehetővé teszi, hogy a következő esetekben:

* Használhatja ugyanazt az API Management erőforrást belső fogyasztók és a külső felhasználók által történő használatra.
* Az API Management egy erőforrást használnak, és a külső fogyasztók számára elérhető API Management szolgáltatásban meghatározott API-t.
* Átállás a hozzáférés és az API Management a nyilvános interneten kapcsolja ki kulcsrakész lehetőséget biztosíthat.

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ebben a cikkben ismertetett lépéseket követve, kell rendelkeznie:

* Aktív Azure-előfizetés.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

* Tanúsítványok – pfx és az API állomásnév cer és a fejlesztői portál gazdanév pfx.

## <a name="scenario"> </a> A forgatókönyv

Ez a cikk bemutatja, hogyan használhatja a külső és belső fogyasztók számára egy egy API Management-szolgáltatás, és lehetővé teszi szerepét egy egységes előtérrendszer mind a helyszíni és felhőalapú API-k. Hogyan teszi közzé az API-k (a példában kiemelve jelennek meg a zöld) csak egy részhalmazát útválasztási szolgáltatással elérhető az Application Gatewayben külső felhasználásra is megjelenik.

Az első telepítő minden API-felügyelt csak a virtuális hálózaton belül. Belső fogyasztók számára (kiemelve a narancssárga) férhetnek hozzá az összes belső és külső API-k. Forgalom soha nem kerül ki internetes kézbesíti a rendszer a nagy teljesítményű Expressroute-Kapcsolatcsoportok keresztül.

![URL-útvonal](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"> </a> A telepítés megkezdése előtt

* Ügyeljen arra, hogy az Azure PowerShell legújabb verzióját használja. Tekintse meg a telepítési utasításait [Azure PowerShell telepítése](/powershell/azure/install-az-ps). 

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>Mi szükséges egy API Management és az Application Gateway közötti integrációt létrehozni?

* **Háttér-kiszolgálókészlet:** Ez az API Management szolgáltatás belső virtuális IP-címe.
* **Háttér-kiszolgálókészlet beállításai:** Minden készletnek vannak beállításai, például port, protokoll vagy cookie-alapú affinitás. Ezek a beállítások a készlet minden kiszolgálójára érvényesek.
* **Előtérbeli port:** Ez az application gateway-en megnyitott nyilvános port. Szerezze meg a, forgalom átirányítja a háttér-kiszolgálók valamelyikével.
* **Figyelő:** A figyelő rendelkezik egy előtérbeli porttal, egy protokollal (Http vagy Https, ezek az értékek olyan kis-és nagybetűket), és az SSL-tanúsítvány neve (-kiszervezés konfigurálásakor SSL).
* **Szabály:** A szabály köti a figyelőt egy háttér-kiszolgálókészlethez.
* **Egyéni állapotadat-mintavétel:** Application Gateway alapértelmezés szerint használatával IP-cím alapú mintavételek döntse el, hogy mely kiszolgálók a értékre aktívak. Az API Management szolgáltatás csak válaszol a megfelelő állomásfejléc-kérelmek, ezért az alapértelmezett mintavételek sikertelen. Egyéni állapotmintát kell definiálni, határozza meg, hogy a szolgáltatás működik-e, és továbbítsa a kérelmek az application gateway segítségével.
* **Egyéni tartomány tanúsítványok:** Szeretne hozzáférni az API Management az internetről, szeretne létrehozni egy CNAME-leképezés, az állomásnév az Application Gateway előtér DNS-nevével. Ez biztosítja, hogy a hostname fejléc és az Application Gateway és az API Management továbbított küldött tanúsítvány egy APIM felismerje, érvényes. Ebben a példában két tanúsítványok – használjuk a háttérrendszer és a fejlesztői portál.  

## <a name="overview-steps"> </a> Az API Management és az Application Gateway való integrálásához szükséges lépéseket

1. Egy erőforráscsoport létrehozása a Resource Manager számára.
2. Hozzon létre egy virtuális hálózat, alhálózat és nyilvános IP-Címek az Application Gateway számára. Hozzon létre egy másik alhálózatot az API Management szolgáltatáshoz.
3. A fent létrehozott virtuális hálózat alhálózat belül az API Management szolgáltatás létrehozása, és győződjön meg arról, a belső módot használja.
4. Az API Management szolgáltatást az egyéni tartománynév beállítása.
5. Hozzon létre egy Application Gateway konfigurációs objektumhoz.
6. Hozzon létre egy Application Gateway-erőforrás.
7. Hozzon létre egy CNAME rekord, az API Management proxyeszköznév az Application Gateway nyilvános DNS-nevét.

## <a name="exposing-the-developer-portal-externally-through-application-gateway"></a>A fejlesztői portálon kívülről keresztül az Application Gateway által

Ebben az útmutatóban fog is elérhetővé tesszük a **fejlesztői portál** a külső célközönség az Application Gatewayen keresztül. Fejlesztői portál figyelőt, mintavételi, beállítások és szabályok létrehozásához további lépések szükségesek. Az összes részletei is szerepelnek a megfelelő lépéseket.

> [!WARNING]
> Ha az Azure AD használata, illetve harmadik fél hitelesítési, engedélyezze [cookie-alapú munkamenet-affinitás](https://docs.microsoft.com/azure/application-gateway/overview#session-affinity) az Application Gateway szolgáltatást.

## <a name="create-a-resource-group-for-resource-manager"></a>Erőforráscsoport létrehozása a Resource Managerhez

### <a name="step-1"></a>1. lépés

Jelentkezzen be az Azure-ba

```powershell
Login-AzAccount
```

Hitelesíti a hitelesítő adataival.

### <a name="step-2"></a>2. lépés

Válassza ki a kívánt előfizetés azonosítóértékét.

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

Az Azure Resource Manager megköveteli, hogy minden erőforráscsoport megadjon egy helyet. Ez szolgál az erőforráscsoport erőforrásainak alapértelmezett helyeként. Győződjön meg arról, hogy az application gateway létrehozására irányuló összes parancs ugyanazt az erőforráscsoportot.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Hozzon létre egy virtuális hálózatot és egy alhálózatot az application gateway számára

Az alábbi példa bemutatja, hogyan hozhat létre virtuális hálózatot használatával a resource manager.

### <a name="step-1"></a>1. lépés

A 10.0.0.0/24 címtartományt rendelni az Application Gateway egy virtuális hálózat létrehozásakor használni kívánt alhálózati változóhoz.

```powershell
$appgatewaysubnet = New-AzVirtualNetworkSubnetConfig -Name "apim01" -AddressPrefix "10.0.0.0/24"
```

### <a name="step-2"></a>2. lépés

A cím-tartományt 10.0.1.0/24 hozzárendelése a virtuális hálózat létrehozásakor az API Management szolgáltatáshoz használni kívánt alhálózati változóhoz.

```powershell
$apimsubnet = New-AzVirtualNetworkSubnetConfig -Name "apim02" -AddressPrefix "10.0.1.0/24"
```

### <a name="step-3"></a>3. lépés

Hozzon létre egy virtuális hálózatot nevű **appgwvnet** erőforráscsoportban **apim-appGw-RG** az USA nyugati RÉGIÓJA számára. Az előtag 10.0.0.0/16 használja a 10.0.0.0/24 alhálózat és 10.0.1.0/24.

```powershell
$vnet = New-AzVirtualNetwork -Name "appgwvnet" -ResourceGroupName $resGroupName -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>4. lépés

Rendelje hozzá egy alhálózati változót a következő lépések

```powershell
$appgatewaysubnetdata = $vnet.Subnets[0]
$apimsubnetdata = $vnet.Subnets[1]
```

## <a name="create-an-api-management-service-inside-a-vnet-configured-in-internal-mode"></a>Egy belső módban konfigurált virtuális hálózaton belül az API Management szolgáltatás létrehozása

Az alábbi példa bemutatja, hogyan hozhat létre API Management szolgáltatás csak a belső hozzáférés konfigurált virtuális hálózaton.

### <a name="step-1"></a>1. lépés

Hozzon létre egy API Management virtuális hálózat objektumot, a fent létrehozott $apimsubnetdata alhálózat használatával.

```powershell
$apimVirtualNetwork = New-AzApiManagementVirtualNetwork -Location $location -SubnetResourceId $apimsubnetdata.Id
```

### <a name="step-2"></a>2. lépés

Hozzon létre egy API Management szolgáltatás a virtuális hálózaton belül.

```powershell
$apimServiceName = "ContosoApi"       # API Management service instance name
$apimOrganization = "Contoso"         # organization name
$apimAdminEmail = "admin@contoso.com" # administrator's email address
$apimService = New-AzApiManagement -ResourceGroupName $resGroupName -Location $location -Name $apimServiceName -Organization $apimOrganization -AdminEmail $apimAdminEmail -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Developer"
```

Lásd a fenti parancs sikeres követően [belső virtuális hálózathoz az API Management szolgáltatáshoz való hozzáféréshez szükséges DNS-konfiguráció](api-management-using-with-internal-vnet.md#apim-dns-configuration) az eléréséhez. Ebben a lépésben egy több mint fél óráig is eltarthat.

## <a name="set-up-a-custom-domain-name-in-api-management"></a>Az API Management egy egyéni tartománynév beállítása

### <a name="step-1"></a>1. lépés

Töltse fel a tanúsítványokat a titkos kulcsok a tartományra. Ebben a példában használjuk `api.contoso.net` és `portal.contoso.net`.  

```powershell
$gatewayHostname = "api.contoso.net"                 # API gateway host
$portalHostname = "portal.contoso.net"               # API developer portal host
$gatewayCertCerPath = "C:\Users\Contoso\gateway.cer" # full path to api.contoso.net .cer file
$gatewayCertPfxPath = "C:\Users\Contoso\gateway.pfx" # full path to api.contoso.net .pfx file
$portalCertPfxPath = "C:\Users\Contoso\portal.pfx"   # full path to portal.contoso.net .pfx file
$gatewayCertPfxPassword = "certificatePassword123"   # password for api.contoso.net pfx certificate
$portalCertPfxPassword = "certificatePassword123"    # password for portal.contoso.net pfx certificate

$certUploadResult = Import-AzApiManagementHostnameCertificate -ResourceGroupName $resGroupName -Name $apimServiceName -HostnameType "Proxy" -PfxPath $gatewayCertPfxPath -PfxPassword $gatewayCertPfxPassword -PassThru
$certPortalUploadResult = Import-AzApiManagementHostnameCertificate -ResourceGroupName $resGroupName -Name $apimServiceName -HostnameType "Proxy" -PfxPath $portalCertPfxPath -PfxPassword $portalCertPfxPassword -PassThru
```

### <a name="step-2"></a>2. lépés

A tanúsítványok feltöltése után hozzon létre gazdanév konfigurációs objektumok a proxy és a portál.  

```powershell
$proxyHostnameConfig = New-AzApiManagementHostnameConfiguration -CertificateThumbprint $certUploadResult.Thumbprint -Hostname $gatewayHostname
$portalHostnameConfig = New-AzApiManagementHostnameConfiguration -CertificateThumbprint $certPortalUploadResult.Thumbprint -Hostname $portalHostname
$result = Set-AzApiManagementHostnames -Name $apimServiceName -ResourceGroupName $resGroupName –PortalHostnameConfiguration $portalHostnameConfig -ProxyHostnameConfiguration $proxyHostnameConfig
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Nyilvános IP-cím létrehozása az előtérbeli konfigurációhoz

Hozzon létre egy nyilvános IP-erőforrást **publicIP01** az erőforráscsoportban.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $resGroupName -name "publicIP01" -location $location -AllocationMethod Dynamic
```

Amikor a szolgáltatás elindul, egy IP-cím lesz kiosztva az Application Gatewaynek.

## <a name="create-application-gateway-configuration"></a>Hozzon létre az application gateway-konfigurációt

Az Application Gateway létrehozása előtt minden konfigurációs elemet be kell állítani. Az alábbi lépések létrehozzák az Application Gateway erőforráshoz szükséges konfigurációs elemeket.

### <a name="step-1"></a>1. lépés

Hozzon létre egy **gatewayIP01** nevű Application Gateway IP-konfigurációt. Amikor az Application Gateway elindul, a konfigurált alhálózatból felvesz egy IP-címet, és a hálózati forgalmat a háttérbeli IP-készlet IP-címeihez irányítja. Ne feledje, hogy minden példány egy IP-címet vesz fel.

```powershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "gatewayIP01" -Subnet $appgatewaysubnetdata
```

### <a name="step-2"></a>2. lépés

Konfigurálja az előtérbeli IP-portot a nyilvános IP-cím végponthoz. A port a port, amelyet a végfelhasználók csatlakozni.

```powershell
$fp01 = New-AzApplicationGatewayFrontendPort -Name "port01"  -Port 443
```

### <a name="step-3"></a>3. lépés

Konfigurálja az előtérbeli IP-portot egy nyilvános IP-címvégponttal.

```powershell
$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>4. lépés

A tanúsítványok konfigurálása az Application gatewayhez, amelyet áthaladó forgalom újbóli titkosítására és visszafejtésére használhat.

```powershell
$certPwd = ConvertTo-SecureString $gatewayCertPfxPassword -AsPlainText -Force
$cert = New-AzApplicationGatewaySslCertificate -Name "cert01" -CertificateFile $gatewayCertPfxPath -Password $certPwd
$certPortalPwd = ConvertTo-SecureString $portalCertPfxPassword -AsPlainText -Force
$certPortal = New-AzApplicationGatewaySslCertificate -Name "cert02" -CertificateFile $portalCertPfxPath -Password $certPortalPwd
```

### <a name="step-5"></a>5. lépés

Hozza létre a HTTP-figyelők az Application Gateway számára. Előtérbeli IP konfigurációja, port és az ssl tanúsítványok rendelhet hozzájuk.

```powershell
$listener = New-AzApplicationGatewayHttpListener -Name "listener01" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert -HostName $gatewayHostname -RequireServerNameIndication true
$portalListener = New-AzApplicationGatewayHttpListener -Name "listener02" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $certPortal -HostName $portalHostname -RequireServerNameIndication true
```

### <a name="step-6"></a>6. lépés

Hozzon létre egyéni mintavételek az API Management szolgáltatás `ContosoApi` proxy tartomány végpont. Az elérési út `/status-0123456789abcdef` egy alapértelmezett egészségügyi végpont az API Management-szolgáltatásokban üzemeltetett. Állítsa be `api.contoso.net` , egy egyéni mintát állomásnév biztonságossá tételéhez, SSL-tanúsítvánnyal.

> [!NOTE]
> A hostname `contosoapi.azure-api.net` konfigurálva, amikor egy elnevezett szolgáltatás az alapértelmezett proxyeszköznév `contosoapi` jön létre a nyilvános Azure-ban.
>

```powershell
$apimprobe = New-AzApplicationGatewayProbeConfig -Name "apimproxyprobe" -Protocol "Https" -HostName $gatewayHostname -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
$apimPortalProbe = New-AzApplicationGatewayProbeConfig -Name "apimportalprobe" -Protocol "Https" -HostName $portalHostname -Path "/signin" -Interval 60 -Timeout 300 -UnhealthyThreshold 8
```

### <a name="step-7"></a>7. lépés

Töltse fel a tanúsítványt az SSL-kompatibilis háttérerőforrásokhoz készletben kell használni. Ez az a 4. lépésben megadott ugyanazt a tanúsítványt.

```powershell
$authcert = New-AzApplicationGatewayAuthenticationCertificate -Name "whitelistcert1" -CertificateFile $gatewayCertCerPath
```

### <a name="step-8"></a>8. lépés

Konfigurálja a HTTP-háttérbeállítások az Application Gateway számára. Ez magában foglalja a háttérrendszer kérelem, amely után törölve van időtúllépési korlát. Ez az érték eltér a mintavétel időkorlátja.

```powershell
$apimPoolSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
$apimPoolPortalSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolPortalSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimPortalProbe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>9. lépés

A háttérbeli IP-címkészletet konfigurálja **apimbackend** belső virtuális IP-címe az API Management szolgáltatás a fent létrehozott.

```powershell
$apimProxyBackendPool = New-AzApplicationGatewayBackendAddressPool -Name "apimbackend" -BackendIPAddresses $apimService.PrivateIPAddresses[0]
```

### <a name="step-10"></a>10. lépés

Az Application Gateway használatára az alapszintű útválasztási szabályokat létrehozni.

```powershell
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType Basic -HttpListener $listener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting
$rule02 = New-AzApplicationGatewayRequestRoutingRule -Name "rule2" -RuleType Basic -HttpListener $portalListener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolPortalSetting
```

> [!TIP]
> -RuleType módosítása és az útválasztást, bizonyos a fejlesztői portál oldalain való hozzáférés korlátozásához.

### <a name="step-11"></a>11. lépés

Az Application Gateway konfigurálása a példányok számát és méretét. Ebben a példában használjuk a [WAF Termékváltozatban](../application-gateway/application-gateway-webapplicationfirewall-overview.md) az API Management-erőforrás a biztonság fokozása érdekében.

```powershell
$sku = New-AzApplicationGatewaySku -Name "WAF_Medium" -Tier "WAF" -Capacity 2
```

### <a name="step-12"></a>12. lépés

WAF "Megelőzési" módban kell konfigurálni.

```powershell
$config = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>Az Application Gateway létrehozása

Hozzon létre egy Application Gateway az előző lépésekből származó összes konfigurációs objektumot.

```powershell
$appgwName = "apim-app-gw"
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $resGroupName -Location $location -BackendAddressPools $apimProxyBackendPool -BackendHttpSettingsCollection $apimPoolSetting, $apimPoolPortalSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener, $portalListener -RequestRoutingRules $rule01, $rule02 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert, $certPortal -AuthenticationCertificates $authcert -Probes $apimprobe, $apimPortalProbe
```

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>Az Application Gateway erőforrás nyilvános DNS-nevet, az API Management proxyeszköznév CNAME

Az átjáró létrehozása után a következő lépés a kommunikációra szolgáló előtér konfigurálása. Nyilvános IP-cím használata esetén az Application Gateway egy dinamikusan hozzárendelt DNS-név, amely nem lehet könnyen használható van szükség.

Az Application Gateway DNS-nevet használjon, hozzon létre egy CNAME rekordot, amely az APIM-proxy állomás neve (Példa: `api.contoso.net` a fenti példákban) a DNS-névhez. Konfigurálja az előtérbeli IP-CNAME-rekordot, hogy az Application Gateway és a kapcsolódó IP/DNS-név publicipaddress használatával adatai olvashatók be. A-bejegyzések használata nem ajánlott, mivel a virtuális IP-cím változhat az átjáró újraindítása.

```powershell
Get-AzPublicIpAddress -ResourceGroupName $resGroupName -Name "publicIP01"
```

## <a name="summary"> </a> Összefoglalás
Egy virtuális hálózatban konfigurálva az Azure API Management egyetlen átjáró felületet biztosít az összes konfigurált API-k, attól üzemeltetett helyszíni vagy a felhőben. Az Application Gateway integrálása az API Management biztosít szelektív engedélyezésével az adott API-kat az interneten érhető el, valamint a webalkalmazási tűzfal biztosít az API Management-példány egy előtérkiszolgáló, rugalmasságát.

##<a name="next-steps"> </a> Következő lépések
* További információ az Azure Application Gateway
  * [Application Gateway áttekintése](../application-gateway/application-gateway-introduction.md)
  * [Application Gateway webalkalmazási tűzfal](../application-gateway/application-gateway-webapplicationfirewall-overview.md)
  * [Az Application Gateway-alapú útválasztás használatával](../application-gateway/application-gateway-create-url-route-arm-ps.md)
* További információ az API Management és a virtuális hálózatok
  * [Csak a virtuális hálózaton belül elérhető API Management használatával](api-management-using-with-internal-vnet.md)
  * [Az API Management használatával a virtuális hálózaton](api-management-using-with-vnet.md)
