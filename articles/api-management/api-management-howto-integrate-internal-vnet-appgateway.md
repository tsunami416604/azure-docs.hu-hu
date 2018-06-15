---
title: Azure API Management használata a virtuális hálózatban az Alkalmazásátjáró |} Microsoft Docs
description: Megtudhatja, hogyan telepítse és konfigurálja a belső virtuális hálózat az alkalmazás átjáró (waf-ot), amelynek az Azure API Management
services: api-management
documentationcenter: ''
author: solankisamir
manager: kjoshi
editor: antonba
ms.assetid: a8c982b2-bca5-4312-9367-4a0bbc1082b1
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: sasolank
ms.openlocfilehash: 595abcaafdea5cde3f868567bac7fb9cf0ee424b
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33936105"
---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>Egy belső virtuális Hálózatot az API Management integrálása Alkalmazásátjáró 

##<a name="overview"> </a> – Áttekintés
 
Az API Management szolgáltatás konfigurálható egy virtuális hálózatot belső módban, így azokat csak a virtuális hálózaton belülről érhetők el. Az Azure Application Gateway egy olyan PAAS szolgáltatás, amely réteg-7 terheléselosztót biztosít. Fordított proxy szolgáltatásként működik, és biztosít ajánlat egy webes alkalmazás tűzfalat (WAF) között.

Egy belső virtuális Hálózatot és az Application Gateway előtér kiépítve API Management kombinálásával lehetővé teszi, hogy a következő esetekben:

* Használja az ugyanazon API-kezelés erőforráshoz a belső fogyasztók és a külső felhasználók által megjeleníthető.
* Egyetlen API Management erőforrást használ, és rendelkezik a külső felhasználók API Management érhető el a megadott API-t.
* Kulcsrakész hardvermódosításainak váltani hozzáférés az API Management a nyilvános internetről be- és kikapcsolható. 

## <a name="prerequisites"></a>Előfeltételek

Ebben a cikkben leírt lépések végrehajtásához rendelkeznie kell:

+ Aktív Azure-előfizetés.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ APIM példánya. További információkért lásd: [hozzon létre egy Azure API Management példányt](get-started-create-service-instance.md).

##<a name="scenario"> </a> A forgatókönyv
Ez a cikk bemutatja, hogyan használja a külső és belső fogyasztók egyetlen API-kezelés szolgáltatás, és lehetővé teszi működjön, és egyetlen időtúllépést a két helyszíni és felhőalapú API-k. Hogyan teszi közzé az API-k (a példa zöld kijelölt) csak egy részét a PathBasedRouting funkció érhető el az alkalmazás átjáró használatával külső felhasználásra is megjelenik.

Az első beállítás minden API felügyelt csak virtuális hálózaton belül. Belső fogyasztóknak (a kijelölt narancs) férhetnek hozzá a belső és külső API. Forgalom soha nem kerül ki egy nagy teljesítményű kerülnek az interneten keresztül Expressroute-Kapcsolatcsoportok.

![URL-cím útvonal](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"> </a> Előkészületek

1. Telepítse az Azure PowerShell-parancsmagok legújabb verzióját a Webplatform-telepítővel. A [Letöltések lap](https://azure.microsoft.com/downloads/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) **Windows PowerShell** szakaszából letöltheti és telepítheti a legújabb verziót.
2. Hozzon létre egy virtuális hálózatot, és hozzon létre különálló alhálózatokat az API Management és az Alkalmazásátjáró. 
3. Ha azt tervezi, hogy hozzon létre egy egyéni DNS-kiszolgáló a virtuális hálózati, ehhez a központi telepítés elindítása előtt. Ellenőrizze az új alhálózat virtuális hálózatban létrehozott virtuális gépek biztosításával működik megoldhatja és Azure szolgáltatásvégpontokra eléréséhez.

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>Mi az API Management és az Application Gateway közötti integrációs létrehozásához szükséges?

* **Háttér-kiszolgálófiók készlet:** Ez az a belső virtuális IP-címet a API-kezelés szolgáltatást.
* **Háttér-kiszolgálókészlet beállításai:** Minden készletnek vannak beállításai, például port, protokoll vagy cookie-alapú affinitás. Ezek a beállítások a készletben lévő összes kiszolgálót is vonatkozik.
* **Előtér-port:** nyilvános portja, amely meg van nyitva, az alkalmazás-átjárón. Elérte-e azt forgalom lekérdezi átirányítva a háttér-kiszolgálóhoz.
* **Figyelő:** A figyelő egy előtérbeli porttal, egy protokollal (Http vagy Https, a kis- és a nagybetűk megkülönböztetésével) és SSL tanúsítványnévvel rendelkezik (SSL-kiszervezés konfigurálásakor).
* **Szabály:** a szabály a figyelőt kötve van egy háttér-kiszolgálófiók készlet.
* **Egyéni Állapotmintáihoz:** Alkalmazásátjáró, alapértelmezés szerint a megadott IP-cím alapú mintavételt mérje fel, hogy mely kiszolgálók a BackendAddressPool aktívak. Az API Management szolgáltatást csak válaszol a kérelmekre, amelyek rendelkeznek a megfelelő állomásfejléc, ezért az alapértelmezett mintavételt sikertelen. Egy egyéni állapotmintáihoz kell segítségével határozza meg, hogy a szolgáltatás nem aktív, és továbbítsa a kérelmek Alkalmazásátjáró definiálni.
* **Az egyéni tartomány tanúsítvány:** API Management kell létrehoznia egy CNAME-leképezés az állomásnév az Alkalmazásátjáró előtér-DNS-nevével, az internet eléréséhez. Ez biztosítja, hogy az állomásnév fejléc és a tanúsítvány, amely az API Management továbbíthatja a rendszer az Alkalmazásátjáró küldött egy APIM fel tudja ismerni, mint érvényes.

## <a name="overview-steps"> </a> API Management és az Application Gateway integrálásához szükséges lépések 

1. Egy erőforráscsoport létrehozása a Resource Manager számára.
2. Hozzon létre egy virtuális hálózati alhálózat és nyilvános IP-cím az az Alkalmazásátjáró. Hozzon létre egy másik alhálózatot az API Management.
3. Az előbb létrehozott virtuális hálózat alhálózaton belüli API Management szolgáltatás létrehozása, és győződjön meg arról, a belső módot használja.
4. Az API Management szolgáltatásban az egyéni tartománynév beállítása.
5. Az Application Gateway konfigurációs objektum létrehozásához.
6. Alkalmazásátjáró erőforrás létrehozása.
7. Hozzon létre egy CNAME REKORDOT a nyilvános DNS-neve az alkalmazás-átjáró az API Management proxy állomásnevet.

## <a name="create-a-resource-group-for-resource-manager"></a>Erőforráscsoport létrehozása a Resource Managerhez

Ügyeljen arra, hogy az Azure PowerShell legújabb verzióját használja. További információ: [A Windows PowerShell használata a Resource Managerrel](https://docs.microsoft.com/azure/azure-resource-manager/powershell-azure-resource-manager).

### <a name="step-1"></a>1. lépés

Jelentkezzen be az Azure-ba

```powershell
Connect-AzureRmAccount
```

Azokkal a hitelesítő adatait.<BR>

### <a name="step-2"></a>2. lépés

Ellenőrizze a fiókhoz tartozó előfizetések, és válassza ki azt.

```powershell
Get-AzureRmSubscription -Subscriptionid "GUID of subscription" | Select-AzureRmSubscription
```

### <a name="step-3"></a>3. lépés

Hozzon létre egy erőforráscsoportot (hagyja ki ezt a lépést, ha egy meglévő erőforráscsoportot használ).

```powershell
New-AzureRmResourceGroup -Name "apim-appGw-RG" -Location "West US"
```
Az Azure Resource Manager megköveteli, hogy minden erőforráscsoport megadjon egy helyet. Ez szolgál az erőforráscsoport erőforrásainak alapértelmezett helyeként. Győződjön meg arról, hogy minden parancs Alkalmazásátjáró létrehozása ugyanabban az erőforráscsoportban.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Hozzon létre egy virtuális hálózatot és az Alkalmazásátjáró alhálózatot

A következő példa bemutatja, hogyan manager használata a virtuális hálózat létrehozásához.

### <a name="step-1"></a>1. lépés

A cím tartomány 10.0.0.0/24 hozzárendelése a virtuális hálózat létrehozása során az Alkalmazásátjáró használt alhálózati változó.

```powershell
$appgatewaysubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "apim01" -AddressPrefix "10.0.0.0/24"
```

### <a name="step-2"></a>2. lépés

A cím tartomány 10.0.1.0/24 hozzárendelése a virtuális hálózat létrehozása során használt az API Management alhálózati változó.

```powershell
$apimsubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "apim02" -AddressPrefix "10.0.1.0/24"
```

### <a name="step-3"></a>3. lépés

Hozzon létre egy virtuális hálózatot nevű **appgwvnet** erőforráscsoportban **apim-appGw-RG** az USA nyugati régiója régióba a előtag 10.0.0.0/16 tartozó alhálózatok 10.0.0.0/24 és 10.0.1.0/24.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name "appgwvnet" -ResourceGroupName "apim-appGw-RG" -Location "West US" -AddressPrefix "10.0.0.0/16" -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>4. lépés

Rendelje hozzá a következő lépéseket alhálózati változó

```powershell
$appgatewaysubnetdata=$vnet.Subnets[0]
$apimsubnetdata=$vnet.Subnets[1]
```
## <a name="create-an-api-management-service-inside-a-vnet-configured-in-internal-mode"></a>Egy belső módban konfigurált virtuális hálózaton belül az API Management szolgáltatás létrehozása

A következő példa bemutatja, hogyan API Management szolgáltatás létrehozása csak a belső hozzáférés beállítása a VNETEN belül.

### <a name="step-1"></a>1. lépés
A fentiekben létrehozott $apimsubnetdata alhálózati használata API felügyeleti virtuális hálózati objektumot létrehozni.

```powershell
$apimVirtualNetwork = New-AzureRmApiManagementVirtualNetwork -Location "West US" -SubnetResourceId $apimsubnetdata.Id
```
### <a name="step-2"></a>2. lépés
A virtuális hálózaton belül az API Management szolgáltatás létrehozása.

```powershell
$apimService = New-AzureRmApiManagement -ResourceGroupName "apim-appGw-RG" -Location "West US" -Name "ContosoApi" -Organization "Contoso" -AdminEmail "admin@contoso.com" -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Developer"
```
A fenti parancs sikeres után tekintse meg a [belső hálózatok API-kezelés szolgáltatás eléréséhez szükséges DNS-konfiguráció](api-management-using-with-internal-vnet.md#apim-dns-configuration) való hozzáférésre.

## <a name="set-up-a-custom-domain-name-in-api-management"></a>Telepítés egy egyéni tartománynevet, az API Management

### <a name="step-1"></a>1. lépés
A tanúsítvány feltöltése a tartományhoz tartozó titkos kulccsal. Ehhez a példához lesz `*.contoso.net`. 

```powershell
$certUploadResult = Import-AzureRmApiManagementHostnameCertificate -ResourceGroupName "apim-appGw-RG" -Name "ContosoApi" -HostnameType "Proxy" -PfxPath <full path to .pfx file> -PfxPassword <password for certificate file> -PassThru
```

### <a name="step-2"></a>2. lépés
A tanúsítvány a feltöltést követően állomásnév konfigurációs objektum létrehozása egy állomásnevét a proxy `api.contoso.net`, mert a példa tanúsítvány lehetővé teszi a szolgáltató a `*.contoso.net` tartomány. 

```powershell
$proxyHostnameConfig = New-AzureRmApiManagementHostnameConfiguration -CertificateThumbprint $certUploadResult.Thumbprint -Hostname "api.contoso.net"
$result = Set-AzureRmApiManagementHostnames -Name "ContosoApi" -ResourceGroupName "apim-appGw-RG" -ProxyHostnameConfiguration $proxyHostnameConfig
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Nyilvános IP-cím létrehozása az előtérbeli konfigurációhoz

Hozzon létre egy nyilvános IP-erőforrás **publicIP01** erőforráscsoportban **apim-appGw-RG** az USA nyugati régiójában.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName "apim-appGw-RG" -name "publicIP01" -location "West US" -AllocationMethod Dynamic
```

Amikor a szolgáltatás elindul, egy IP-cím lesz kiosztva az Application Gatewaynek.

## <a name="create-application-gateway-configuration"></a>Alkalmazás átjáró-konfiguráció létrehozása

Az Application Gateway létrehozása előtt minden konfigurációs elemet be kell állítani. Az alábbi lépések létrehozzák az Application Gateway erőforráshoz szükséges konfigurációs elemeket.

### <a name="step-1"></a>1. lépés

Hozzon létre egy **gatewayIP01** nevű Application Gateway IP-konfigurációt. Amikor az Application Gateway elindul, a konfigurált alhálózatból felvesz egy IP-címet, és a hálózati forgalmat a háttérbeli IP-készlet IP-címeihez irányítja. Ne feledje, hogy minden példány egy IP-címet vesz fel.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name "gatewayIP01" -Subnet $appgatewaysubnetdata
```

### <a name="step-2"></a>2. lépés

Konfigurálja az előtér-IP-portját a nyilvános IP-végponton. Ez a port nem a portot, amelyet a végfelhasználók csatlakozni.

```powershell
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "port01"  -Port 443
```
### <a name="step-3"></a>3. lépés

Konfigurálja az előtérbeli IP-portot egy nyilvános IP-címvégponttal.

```powershell
$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>4. lépés

A tanúsítvány konfigurálása az Alkalmazásátjáró, hogy át kellene haladnia-forgalom újratitkosítása és visszafejtésére szolgál.

```powershell
$cert = New-AzureRmApplicationGatewaySslCertificate -Name "cert01" -CertificateFile <full path to .pfx file> -Password <password for certificate file>
```

### <a name="step-5"></a>5. lépés

Az alkalmazás átjáró a HTTP-figyelő létrehozása. Az előtér-IP konfigurációjával, a port és az ssl-tanúsítványt hozzárendeli azt.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert
```

### <a name="step-6"></a>6. lépés

Hozzon létre egy egyéni mintavétel az API Management szolgáltatáshoz `ContosoApi` proxy tartomány végpont. Az elérési út `/status-0123456789abcdef` egy alapértelmezett állapotfigyelő végpont az API Management-szolgáltatások üzemeltet. Állítsa be `api.contoso.net` mint egy SSL-tanúsítvánnyal biztosításához egyéni mintavételi állomásnevet.

> [!NOTE]
> A hostname `contosoapi.azure-api.net` konfigurálását, ha egy nevű szolgáltatás alapértelmezett proxy állomásneve `contosoapi` jön létre a nyilvános Azure-ban. 
> 

```powershell
$apimprobe = New-AzureRmApplicationGatewayProbeConfig -Name "apimproxyprobe" -Protocol "Https" -HostName "api.contoso.net" -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
```

### <a name="step-7"></a>7. lépés

Töltse fel az SSL-kompatibilis háttér készlet erőforrások használni kívánt tanúsítványt. Ez az a fenti 4 megadott ugyanazt a tanúsítványt.

```powershell
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name "whitelistcert1" -CertificateFile <full path to .cer file>
```

### <a name="step-8"></a>8. lépés

Az Alkalmazásátjáró HTTP háttér beállításainak konfigurálása. Ez magában foglalja a háttérrendszer kérelem megszakítása elteltével időtúllépési korlát. Ez az érték eltér a mintavételi időtúllépés.

```powershell
$apimPoolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "apimPoolSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>9. lépés

Konfigurálja a háttér IP-címkészletet nevű **apimbackend** a belső virtuális IP-címet a API-kezelés szolgáltatás a fenti létrehozott.

```powershell
$apimProxyBackendPool = New-AzureRmApplicationGatewayBackendAddressPool -Name "apimbackend" -BackendIPAddresses $apimService.StaticIPs[0]
```

### <a name="step-10"></a>10. lépés

Hozzon létre egy üres (nem létező) háttér beállításait. Kérelmek és API-elérési utakat, azt nem teszi közzé az API Management alkalmazás átjárón keresztül kíván elérte a háttér, és a 404 adja vissza.

Az üres háttér HTTP beállításainak konfigurálása.

```powershell
$dummyBackendSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "dummySetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

Egy üres háttér konfigurálása **dummyBackendPool**, amely a megadott FQDN cím mutat **dummybackend.com**. Az FQDN cím nem létezik a virtuális hálózat.

```powershell
$dummyBackendPool = New-AzureRmApplicationGatewayBackendAddressPool -Name "dummyBackendPool" -BackendFqdns "dummybackend.com"
```

Létrehoz egy szabály beállítást, amely az Alkalmazásátjáró fogja használni a nem létező háttér mutat, amely alapértelmezés szerint **dummybackend.com** virtuális hálózatban.

```powershell
$dummyPathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "nonexistentapis" -Paths "/*" -BackendAddressPool $dummyBackendPool -BackendHttpSettings $dummyBackendSetting
```

### <a name="step-11"></a>11. lépés

A háttér-készletek az URL-cím szabály elérési útvonalainak konfigurálása. Ez lehetővé teszi, hogy kijelölése az API-k közül csak az API Management nyilvánosan elérhetővé váljon. Például akkor, ha nincsenek `Echo API` (/ echo /) `Calculator API` csak (/calc/) stb. Ellenőrizze `Echo API` internetről hozzáférhető). 

Az alábbi példa létrehoz egy egyszerű szabályt a "/ echo /" elérési út útválasztási adatforgalmát a háttér-"apimProxyBackendPool".

```powershell
$echoapiRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "externalapis" -Paths "/echo/*" -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting
```

Ha az elérési út nem egyezik meg szeretnénk az API-kezelés engedélyezése az elérésiút-szabály, a szabály térkép konfiguráció is konfigurálja egy alapértelmezett háttér címkészletet nevű **dummyBackendPool**. Például http://api.contoso.net/calc/sum ugrik **dummyBackendPool** , mint az alapértelmezett alkalmazáskészlet nem egyező forgalom van definiálva.

```powershell
$urlPathMap = New-AzureRmApplicationGatewayUrlPathMapConfig -Name "urlpathmap" -PathRules $echoapiRule, $dummyPathRule -DefaultBackendAddressPool $dummyBackendPool -DefaultBackendHttpSettings $dummyBackendSetting
```

A fenti lépést biztosítja, hogy csak az elérési út vonatkozó kérések "/ echo" az alkalmazás átjárón keresztül engedélyezettek. Más konfigurálva az API Management API-k kérelmek kivételhibát 404-es hibák az Alkalmazásátjáró, ha az internetről érik el. 

### <a name="step-12"></a>12. lépés

Az Alkalmazásátjáró használandó URL-cím elérési út-alapú útválasztási szabály beállítás létrehozása.

```powershell
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType PathBasedRouting -HttpListener $listener -UrlPathMap $urlPathMap
```

### <a name="step-13"></a>13. lépés

Adja meg a példányok és mérete a(z). Itt a [WAF SKU](../application-gateway/application-gateway-webapplicationfirewall-overview.md) az API-kezelés erőforráshoz a biztonság fokozása érdekében.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name "WAF_Medium" -Tier "WAF" -Capacity 2
```

### <a name="step-14"></a>14. lépés

WAF "Megakadályozása" módban kell konfigurálni.
```powershell
$config = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>Alkalmazásátjáró létrehozása

Hozzon létre egy alkalmazást az előző lépéseket az összes konfigurációs objektumok.

```powershell
$appgw = New-AzureRmApplicationGateway -Name $applicationGatewayName -ResourceGroupName $resourceGroupName  -Location $location -BackendAddressPools $apimProxyBackendPool, $dummyBackendPool -BackendHttpSettingsCollection $apimPoolSetting, $dummyBackendSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener -UrlPathMaps $urlPathMap -RequestRoutingRules $rule01 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert -Probes $apimprobe
```

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>Az API Management proxy állomásnevet a nyilvános DNS-nevével, az Application Gateway erőforrás CNAME

Az átjáró létrehozása után a következő lépés a kommunikációra szolgáló előtér konfigurálása. Egy nyilvános IP-cím használata esetén az Application Gateway szükséges egy dinamikusan hozzárendelt DNS-nevet, amely nem lehet könnyen használható. 

Az Alkalmazásátjáró DNS-nevét kell hozhatók létre egy CNAME rekordot, amely a APIM proxyállomásnév mutat (pl. `api.contoso.net` a fenti példákban) a DNS-névhez. Az előtérbeli IP-CNAME rekord konfigurálásához részleteinek lehívására szolgáló az Alkalmazásátjáró és a társított IP-/ DNS-neve, a PublicIPAddress elem használatával. A-rekordok használata nem ajánlott, óta, a VIP előfordulhat, hogy az átjáró újra kell indítani.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName "apim-appGw-RG" -Name "publicIP01"
```

##<a name="summary"> </a> Összefoglalás
Konfigurált egy VNETET az Azure API Management átjáró egyetlen felületet biztosít minden konfigurált API, legyenek azok helyszíni központi vagy a felhőben. Alkalmazásátjáró integrálása az API Management szelektív módon engedélyezi az adott API-k az interneten érhető el, valamint a webalkalmazási tűzfal biztosít egy háttértűzfal a API Management-példányra, a rugalmasságot biztosít.

##<a name="next-steps"> </a> További lépések
* További tudnivalók az Azure Application Gateway
  * [Átjáró – áttekintés](../application-gateway/application-gateway-introduction.md)
  * [Alkalmazás átjáró webalkalmazási tűzfal](../application-gateway/application-gateway-webapplicationfirewall-overview.md)
  * [Az Alkalmazásátjáró elérési alapú útválasztás használatával](../application-gateway/application-gateway-create-url-route-arm-ps.md)
* Ismerje meg a további információk az API Management és Vnetek
  * [Elérhető a VNETEN belül csak az API Management használata](api-management-using-with-internal-vnet.md)
  * [Az API Management használatát a virtuális hálózat](api-management-using-with-vnet.md)
