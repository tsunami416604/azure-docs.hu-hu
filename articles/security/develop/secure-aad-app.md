---
title: Biztonságos Azure AD-webalkalmazás fejlesztése | Microsoft Docs
description: Ez az egyszerű minta alkalmazás olyan ajánlott biztonsági eljárásokat valósít meg, amelyek javítják az alkalmazást és a szervezete biztonsági helyzetét az Azure-ban való fejlesztés során.
keywords: Na
services: security
documentationcenter: na
author: TerryLanfear
manager: alclabo
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2019
ms.author: terrylan
ms.openlocfilehash: 11bf7c0ae05c2e52d59efb32be47ce6bd96fac4f
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2020
ms.locfileid: "76937986"
---
# <a name="develop-secure-app-for-an-azure-ad-app"></a>Biztonságos alkalmazás fejlesztése Azure AD-alkalmazásokhoz
## <a name="overview"></a>Áttekintés

Ez a minta egy egyszerű Azure Active Directory a webalkalmazással, amely az Azure-beli alkalmazások fejlesztéséhez szükséges biztonsági erőforrásokra hivatkozik. Az alkalmazás olyan biztonsági eljárásokat valósít meg, amelyek segíthetnek az alkalmazás és a szervezet biztonsági helyzetének javításában az Azure-beli alkalmazások fejlesztésekor.

Az üzembe helyezési parancsfájlok beállítják az infrastruktúrát. Az üzembe helyezési parancsfájlok futtatása után az összetevők és szolgáltatások összekapcsolásához meg kell adnia néhány manuális konfigurációt a Azure Portalban. Ez a minta az Azure-ban tapasztalt fejlesztők számára készült, akik a kiskereskedelmi iparágban dolgoznak, és biztonságos Azure-infrastruktúrával rendelkező Azure Active Directory szeretne létrehozni. 


Az alkalmazás fejlesztése és üzembe helyezése során megismerheti, hogyan 
- Hozzon létre egy Azure Key Vault példányt, tárolja és kérje le a titkokat.
- Üzembe helyezheti az Azure-webalkalmazást, amely dedikáltan elkülönített az előtér-tűzfalhoz való hozzáféréssel. 
- Hozzon létre és konfiguráljon egy Azure Application Gateway példányt egy olyan tűzfallal, amely az OWASP Top 10 szabályrendszert használja. 
- Az Azure-szolgáltatások használatával engedélyezheti az átvitelben és a nyugalmában lévő adatok titkosítását. 
- Állítsa be az Azure Policy és a Security centert a compliancies kiértékeléséhez. 

Az alkalmazás fejlesztése és üzembe helyezése után be kell állítania az alábbi minta-webalkalmazást, valamint a konfigurációs és biztonsági mértékeket.

## <a name="architecture"></a>Architektúra
Az alkalmazás egy tipikus n szintű alkalmazás három réteggel. Itt látható az előtér-, a háttér-és az adatbázis-réteg az integrált figyelési és titkos felügyeleti összetevőkkel:

![Alkalmazásarchitektúra](./media/secure-aad-app/architecture.png)

Ez a megoldás az alábbi Azure-szolgáltatásokat használja. Az üzembe helyezési architektúra részletei az üzembe helyezési architektúra szakaszban találhatók. 

Az architektúra ezekből az összetevőkből áll

- [Azure Application Gateway](../../application-gateway/index.yml). Átjárót és tűzfalat biztosít az alkalmazás architektúrája számára.
- [Application Insights](../../azure-monitor/app/app-insights-overview.md). Egy bővíthető Application Performance Management-(APM-) szolgáltatást biztosít több platformon.
- [Azure Key Vault](../../key-vault/index.yml). Tárolja és titkosítja az alkalmazás titkait, és felügyeli a rájuk épülő hozzáférési szabályzatok létrehozását.
- [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md). Felhőalapú identitás-és hozzáférés-kezelési szolgáltatást, bejelentkezést és hozzáférést biztosít az erőforrásokhoz.
- [Azure Domain Name System](../../dns/dns-overview.md). Adja meg a szolgáltatást a tartomány üzemeltetéséhez.
- [Azure Load Balancer](../../load-balancer/load-balancer-overview.md). Lehetővé teszi az alkalmazások skálázását és magas rendelkezésre állású szolgáltatások létrehozását.
- [Azure-webalkalmazás](../../app-service/overview.md).  HTTP-alapú szolgáltatást biztosít a webalkalmazások üzemeltetéséhez.
- [Azure Security Center](../../security-center/index.yml). komplex veszélyforrások elleni védelmet biztosít a felhőben futó hibrid számítási feladatokhoz.
- [Azure Policy](../../governance/policy/overview.md). Biztosítja az erőforrások kiértékelését a hozzárendelt szabályzatok nem megfelelősége érdekében.

## <a name="threat-model"></a>Veszélyforrások modellje
A veszélyforrások modellezése a potenciális biztonsági fenyegetések azonosításának folyamata a vállalat és az alkalmazás számára, majd a megfelelő kockázatcsökkentő terv biztosítása.

Ez a példa a [Microsoft Threat Modeling Tool](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) használta a biztonságos minta alkalmazás veszélyforrások modellezésének megvalósítására. Az összetevők és az adatfolyamatok diagramon való kiépítésével a fejlesztési folyamat elején azonosíthatja a problémákat és a fenyegetéseket. Az idő és a pénz később a használatával lesz mentve.

Itt látható a minta alkalmazáshoz tartozó veszélyforrás modell

![Veszélyforrások modellje](./media/secure-aad-app/threat-model.png)

A veszélyforrások modellezése eszköz által generált veszélyforrások és potenciális sebezhetőségek a következő képernyőképen jelennek meg. A fenyegetés modell áttekintést nyújt a feltett támadási felületről, és felszólítja a fejlesztőket a problémák enyhítésére.

![A veszélyforrások modell kimenete](./media/secure-aad-app/threat-model-output.png)

### <a name="prerequisites"></a>Előfeltételek
Az alkalmazás működésének megkezdéséhez telepítenie kell az alábbi eszközöket:

- Az alkalmazás kódjának módosítására és megtekintésére szolgáló Kódszerkesztő. A [Visual Studio Code](https://code.visualstudio.com/) egy nyílt forráskódú megoldás.
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest,) a fejlesztői számítógépen.
- [Git](https://git-scm.com/) a rendszeren. A git a forráskód helyi klónozására szolgál.
- [jQ](https://stedolan.github.io/jq/), egy UNIX-eszköz, amely felhasználóbarát módon kérdezi le a JSON-t.

A minta alkalmazás erőforrásainak üzembe helyezéséhez Azure-előfizetés szükséges. Ha nem rendelkezik Azure-előfizetéssel, [létrehozhat egy ingyenes fiókot](https://azure.microsoft.com/free/) a minta alkalmazás teszteléséhez.

Az eszközök telepítése után már készen áll az alkalmazás üzembe helyezésére az Azure-ban.

### <a name="implementation-guidance"></a>Implementálási segédlet
Az üzembe helyezési parancsfájl egy olyan parancsfájl, amely négy fázisra osztható fel. Mindegyik fázis üzembe helyezi és konfigurálja az [architektúra-diagramban](#architecture)található Azure-erőforrásokat.

A négy fázis

- Azure Key Vault üzembe helyezése.
- Az Azure Web Apps üzembe helyezése.
- Application Gateway üzembe helyezése a webalkalmazási tűzfallal.
- Konfigurálja az Azure AD-t egy üzembe helyezett alkalmazással.

Az egyes fázisok a korábban üzembe helyezett erőforrások konfigurációjának használatával épülnek fel az előzőre.

A megvalósítás lépéseinek elvégzéséhez győződjön meg arról, hogy telepítette az [Előfeltételek](#prerequisites)szakaszban felsorolt eszközöket.

#### <a name="deploy-azure-key-vault"></a>Azure Key Vault üzembe helyezése
Ebben a szakaszban egy Azure Key Vault-példányt hoz létre és helyez üzembe, amely a titkok és a tanúsítványok tárolására szolgál.

Az üzembe helyezés befejezése után az Azure-ban üzembe helyezhető egy Azure Key Vault példány.

Azure Key Vault üzembe helyezése a PowerShell használatával
 
1. Deklarálja Azure Key Vault változóit.
2. Regisztrálja a Azure Key Vault szolgáltatót.
3. Hozza létre a példányhoz tartozó erőforráscsoportot.
4. Hozza létre a Azure Key Vault példányt a 3. lépésben létrehozott erőforráscsoporthoz.

#### <a name="the-below-azure-ad-user-will-have-admin-permissions-to-the-key-vault"></a>Az alábbi Azure AD-felhasználó rendszergazdai jogosultságokkal fog rendelkezni a Key Vault
    $keyVaultAdminUsers = @($user1,user2)

#### <a name="register-the-az-providers"></a>Az az Providers regisztrálása
    Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault

#### <a name="create-the-azure-key-vault-instance"></a>A Azure Key Vault példány létrehozása
    New-AzKeyVault -Name $kvName 
                -ResourceGroupName $ResourceGroup 
                -Location 'East US'
                -EnabledForDiskEncryption

#### <a name="add-the-administrator-policies-to-the-key-vault"></a>Rendszergazdai házirendek hozzáadása a Key Vault
    foreach ($keyVaultAdminUser in $keyVaultAdminUsers) {
    $UserObjectId = (Get-AzADUser -SearchString $keyVaultAdminUser).Id
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ResourceGroupName $resourceGroupName -ObjectId $UserObjectId 
    -PermissionsToKeys all -PermissionsToSecrets all -PermissionsToCertificates all
    }

#### <a name="to-create-an-access-policy-to-allow-a-user-to-get-and-list-cryptographic-keys-certificates-and-secrets-if-you-know-the-user-principal-name"></a>Hozzáférési szabályzat létrehozása, amely lehetővé teszi a felhasználó számára a titkosítási kulcsok, tanúsítványok és titkos kódok lekérését és listázását, ha ismeri az egyszerű felhasználónevet:
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName 
                           -ResourceGroupName $resourceGroupName 
                           -UserPrincipalName 'user1@contoso.com 
                           -PermissionsToCertificates list, get 
                           -PermissionsToKeys list, get 
                           -PermissionsToSecrets list, get 

Ajánlott a felügyelt identitások használata az Azure-erőforrásokhoz olyan alkalmazásokban, amelyek a Key Vault használatával férnek hozzá az erőforrásokhoz. A biztonsági helyzet akkor nő, ha a hozzáférési kulcsok Key Vault nem a kódban vagy a konfigurációban vannak tárolva.

A tároló főtanúsítványt tartalmaz. A tanúsítvány beszerzéséhez szükséges lépések

1. Töltse le [a tanúsítványt a hitelesítésszolgáltatótól.](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt)
2. A tanúsítványfájl dekódolása:

   ```powershell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```
Ez a szkript létrehoz egy hozzárendelt identitást az App Service-példányhoz, amelyet az MSI használatával használhat a kód vagy konfiguráció során felhasználható kódolási titkok nélküli Azure Key Vault.

Lépjen a portál Azure Key Vault példányára, hogy engedélyezze a hozzárendelt identitást a hozzáférési házirend lapon. Válassza az **új hozzáférési házirend hozzáadása**lehetőséget. A **rendszerbiztonsági tag kiválasztása**területen keresse meg az alkalmazás nevét, amely hasonló a létrehozott app Service példány nevéhez.
Az alkalmazáshoz csatolt egyszerű szolgáltatásnak láthatónak kell lennie. Válassza ki, és mentse a hozzáférési szabályzat lapot, ahogy az alábbi képernyőképen is látható.

Mivel az alkalmazásnak csak a kulcsokat kell lekérnie, a titkok lehetőségnél válassza ki a **Get** engedélyt, amely lehetővé teszi a hozzáférést a megadott jogosultságok csökkentése mellett.

![Hozzáférési szabályzat Key Vault](./media/secure-aad-app/kv-access-policy.png)

*Key Vault hozzáférési szabályzat létrehozása*

Mentse a hozzáférési házirendet, majd mentse az új módosítást a **hozzáférési házirendek** lapon a házirendek frissítéséhez.

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>Application Gateway üzembe helyezése a webalkalmazási tűzfallal engedélyezve
A Web Apps szolgáltatásban nem javasoljuk, hogy közvetlenül az interneten keresztül tegye elérhetővé a szolgáltatásokat.
A terheléselosztás és a tűzfalszabályok nagyobb biztonságot és szabályozást biztosítanak a bejövő forgalom számára, és segítenek a felügyeletben.

Application Gateway példány üzembe helyezése

1. Hozzon létre egy erőforráscsoportot az Application Gateway kibontásához.
2. Hozzon létre egy virtuális hálózatot az átjáróhoz való csatlakoztatáshoz.
3. Hozzon létre egy alhálózatot az átjáróhoz a virtuális hálózaton.
4. Nyilvános IP-cím kiépítése.
5. Az Application Gateway kiépítése.
6. Engedélyezze a webalkalmazási tűzfalat az átjárón.

```
Connect-AzAccount
Select-AzSubscription -SubscriptionId '$SubscriptionId'
New-AzResourceGroup -Name appgw-rg -Location "East US"

#Create a virtual network and a subnet for the application gateway

#Assign an address range for the subnet to be used for the application gateway.

$gwSubnet = New-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

#Assign an address range to be used for the back-end address pool.

$nicSubnet = New-AzVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

#Create a virtual network with the subnets defined in the preceding steps.

$vnet = New-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "East US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet

#Retrieve the virtual network resource and subnet resources to be used in the steps that follow.

$vnet = Get-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
$nicSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet


#Create a public IP address for the front-end configuration

$publicip = New-AzPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "East US" -AllocationMethod Dynamic

#Create an application gateway configuration object

$gipconfig = New-AzApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

#Create a front-end IP configuration

$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

#Configure the back-end IP address pool with the IP addresses of the back-end web servers

$pool = New-AzApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 10.0.3.11

#Configure the front-end IP port for the public IP endpoint

$fp = New-AzApplicationGatewayFrontendPort -Name 'port01'  -Port 443

#Configure the certificate for the application gateway. This certificate is used to decrypt and reencrypt the traffic on the application gateway

$passwd = ConvertTo-SecureString  "P@ssword!1" -AsPlainText -Force 
$cert = New-AzApplicationGatewaySSLCertificate -Name cert01 -CertificateFile "C:\AAD\Securities\Certificates\sslcert.com.cer" -Password $passwd 


#Create the HTTP listener for the application gateway

$listener = New-AzApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert

#Upload the certificate to be used on the SSL-enabled back-end pool resources

#$authcert = New-AzApplicationGatewayAuthenticationCertificate -Name 'allowlistcert1' -CertificateFile C:\cert.cer

$trustedRootCert01 = New-AzApplicationGatewayTrustedRootCertificate -Name "test1" -CertificateFile "C:\AAD\Securities\Certificates\sslcert.com.cer"

#Configure the HTTP settings for the application gateway back end

$poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name "setting01" -Port 443 -Protocol Https -CookieBasedAffinity Disabled -TrustedRootCertificate $trustedRootCert01 -HostName "test1"

#Create a load-balancer routing rule that configures the load balancer

$rule = New-AzApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

#Configure the instance size of the application gateway

$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

#Configure the SSL policy to be used on the application gateway

$SSLPolicy = New-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -PolicyType Custom

$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "East US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -TrustedRootCertificate $trustedRootCert01 -Verbose

```

#### <a name="deploy-azure-web-apps"></a>Az Azure Web Apps üzembe helyezése
A Azure App Service lehetővé teszi, hogy a webalkalmazásokat a Python, a Ruby, C#és a Java nyelveken keresztül hozza létre és működtesse. Az Azure támogatja az egyéni tárolókat is, amelyek lehetővé teszik, hogy gyakorlatilag minden programozási nyelv fusson a Azure App Service platformon.

#### <a name="create-an-app-service-plan-in-free-tier"></a>App Service csomag létrehozása ingyenes szinten
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName $webappname -Tier Free

#### <a name="create-a-web-app"></a>Webalkalmazás létrehozása
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName $webappname

    Write-Host "Configure a CNAME record that maps $fqdn to $webappname.azurewebsites.net"
    Read-Host "Press [Enter] key when ready ..."

#### <a name="before-continuing-go-to-your-azure-domain-name-system-configuration-ui-for-your-custom-domain-and-follow-the-instructions-at-httpsakamsappservicecustomdns-to-configure-a-cname-record-for-the-hostname-www-and-point-it-your-web-apps-default-domain-name"></a>A folytatás előtt lépjen az Azure-beli tartománynévrendszer-konfiguráció felhasználói felületére az egyéni tartományhoz, és kövesse a https://aka.ms/appservicecustomdns ban található utasításokat a "www" állomásnév CNAME rekordjának konfigurálásához, és mutasson a webalkalmazás alapértelmezett tartománynevére.

#### <a name="upgrade-app-service-plan-to-shared-tier-minimum-required-by-custom-domains"></a>App Service terv frissítése a megosztott szintre (az egyéni tartományok számára minimálisan szükséges)
    Set-AzAppServicePlan -Name $webappname -ResourceGroupName $webappname -Tier Shared

#### <a name="add-a-custom-domain-name-to-the-web-app"></a>Egyéni tartománynév hozzáadása a webalkalmazáshoz
    Set-AzWebApp -Name $webappname -ResourceGroupName $webappname `-HostNames @($fqdn,"$webappname.azurewebsites.net")

## <a name="guidance-and-recommendations"></a>Útmutatás és javaslatok

### <a name="network"></a>Network (Hálózat)
Az üzembe helyezés befejezése után egy webalkalmazási tűzfallal rendelkező Application Gateway van engedélyezve.

Az átjáró-példány a HTTPS 443-es portját teszi elérhetővé. Ez a konfiguráció biztosítja, hogy az alkalmazás csak a 443-es porton keresztül legyen elérhető a HTTPS protokollon keresztül.

A nem használt portok blokkolása és a támadási felületek expozíciójának korlátozása az ajánlott biztonsági eljárás.

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>Hálózati biztonsági csoportok hozzáadása az App Service-példányhoz

App Service példányok integrálható a virtuális hálózatokkal. Ez az integráció lehetővé teszi, hogy az alkalmazás bejövő és kimenő forgalmát kezelő hálózati biztonsági csoportházirend-házirendekkel konfigurálják őket.

1. A szolgáltatás engedélyezéséhez az Azure app Service-példány panel beállítások területén válassza a **hálózat** **lehetőséget**. A jobb oldali ablaktáblában konfigurálja a **VNet-integráció**csomópontot.

   ![Új virtuális hálózat integrációja](./media/secure-web-app/app-vnet-menu.png)

    *Új Virtual Network Integration for App Service*
1. A következő lapon válassza a **VNET hozzáadása (előzetes verzió)** lehetőséget.

1. A következő menüben válassza ki a központi telepítésben létrehozott virtuális hálózatot, amely a `aad-vnet`-vel kezdődik. Létrehozhat egy új alhálózatot, vagy kijelölhet egy meglévőt is.
   Ebben az esetben hozzon létre egy új alhálózatot. Állítsa a **címtartományt** a **10.0.3.0/24** értékre, és nevezze el az alhálózati **alkalmazás-alhálózatot**.

   ![App Service virtuális hálózati konfiguráció](./media/secure-web-app/app-vnet-config.png)

    *App Service virtuális hálózati konfigurációja*

Most, hogy engedélyezte a virtuális hálózat integrációját, hozzáadhat hálózati biztonsági csoportokat az alkalmazáshoz.

1. A keresőmező segítségével keressen **hálózati biztonsági csoportokat**. Az eredmények között válassza a **hálózati biztonsági csoportok** lehetőséget.

    ![Hálózati biztonsági csoportok keresése](./media/secure-web-app/nsg-search-menu.png)

    *Hálózati biztonsági csoportok keresése*

2. A következő menüben válassza a **Hozzáadás**lehetőséget. Adja meg a NSG **nevét** és azt az **erőforráscsoportot** , amelyben a helye található. Ez a NSG az Application Gateway alhálózatára lesz alkalmazva.

    ![NSG létrehozása](./media/secure-web-app/nsg-create-new.png)

    *NSG létrehozása*

3. A NSG létrehozása után válassza ki azt. A paneljén a **Beállítások**területen válassza a **bejövő biztonsági szabályok**elemet. Konfigurálja ezeket a beállításokat, hogy engedélyezze az Application Gateway felé irányuló kapcsolatokat az 443-as porton keresztül.

   ![A NSG konfigurálása](./media/secure-web-app/nsg-gateway-config.png)

   *A NSG konfigurálása*

4. Az átjáró NSG kimenő szabályaiban adjon hozzá egy olyan szabályt, amely engedélyezi a kimenő kapcsolatokat a App Service-példányhoz egy olyan szabály létrehozásával, amely a szolgáltatási címkét célozza meg `AppService`

   ![Kimenő szabályok hozzáadása a NSG](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   *Kimenő szabályok hozzáadása a NSG*

    Adjon hozzá egy másik kimenő szabályt, amely lehetővé teszi, hogy az átjáró kimenő szabályokat küldjön egy virtuális hálózatra.

   ![Másik kimenő szabály hozzáadása](./media/secure-web-app/nsg-outbound-vnet.png)

    *Másik kimenő szabály hozzáadása*

5. A NSG alhálózatok paneljén válassza a **hozzárendelés**lehetőséget, válassza ki a központi telepítésben létrehozott virtuális hálózatot, és válassza ki a **GW-alhálózat**nevű átjáró-alhálózatot. A NSG az alhálózatra alkalmazza a rendszer.

6. Hozzon létre egy másik NSG, mint az előző lépésben, ezúttal a App Service példányra vonatkozóan. Adja meg a nevet. Adja hozzá a bejövő szabályt az 443-as porthoz, ahogy az Application Gateway-NSG tette.

   Ha App Service példánya van telepítve egy App Service Environment példányon, amely nem ebben az alkalmazásban, akkor a App Service NSG bejövő biztonsági csoportjain a 454-455-es port megnyitásával engedélyezheti a Azure Service Health-mintavételek számára a bejövő szabályok megadását. A konfiguráció a következő:

   ![Azure Service Health-mintavétel szabályainak hozzáadása](./media/secure-web-app/nsg-create-healthprobes.png)

    *Azure Service Health-mintavétel szabályainak hozzáadása (csak App Service Environment)*

A támadási felület korlátozásához módosítsa a App Service hálózati beállításokat úgy, hogy csak az Application Gateway hozzáférjen az alkalmazáshoz.
A beállítások alkalmazásához lépjen a App Service hálózat lapra, válassza az **IP-korlátozások** lapot, és hozzon létre egy engedélyezési szabályt, amely lehetővé teszi, hogy csak az Application Gateway IP-címe legyen közvetlenül elérhető a szolgáltatáshoz. Az átjáró IP-címét az Áttekintés lapjáról kérheti le. Az **IP-CIDR** lapon adja meg az IP-címet a következő formátumban: `<GATEWAY_IP_ADDRESS>/32`.

![Csak az átjáró engedélyezése](./media/secure-web-app/app-allow-gw-only.png)

*A App Service elérésének engedélyezése csak az átjáró IP-címéhez*

### <a name="azure-domain-name-system"></a>Azure Domain Name System 
Az Azure tartománynévrendszer vagy az Azure tartománynévrendszer felelős a webhelyek vagy szolgáltatások nevének az IP-címére való fordításához (vagy feloldásához). Az Azure tartománynévrendszer (https://docs.microsoft.com/azure/dns/dns-overview) a tartománynévrendszer-tartományok olyan üzemeltetési szolgáltatása, amely az Azure-infrastruktúra használatával teszi lehetővé a névfeloldást. A tartományok az Azure-ban való üzemeltetésével a felhasználók a tartománynévrendszer rekordjait a többi Azure-szolgáltatással azonos hitelesítő adatokkal, API-kkal, eszközökkel és számlázással kezelhetik. Az Azure tartománynévrendszer támogatja a magánhálózati tartománynévrendszer-tartományokat is.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption
Azure Disk Encryption kihasználja a Windows BitLocker szolgáltatását, hogy mennyiségi titkosítást biztosítson az adatlemezek számára. A megoldás integrálva van Azure Key Vaultekkel a lemezes titkosítási kulcsok szabályozása és kezelése érdekében.

### <a name="identity-management"></a>Identitáskezelés
Az alábbi technológiák lehetővé teszik a kártyatulajdonos-információknak az Azure-környezetben való elérésének kezelését.
- Azure Active Directory a Microsoft több-bérlős felhőalapú címtár-és Identitáskezelés-kezelő szolgáltatása. A megoldás összes felhasználója Azure Active Directoryban jön létre, beleértve az Azure WebApphoz hozzáférő felhasználókat is.
- Az Azure szerepköralapú hozzáférés-vezérlés lehetővé teszi a rendszergazdáknak, hogy részletes hozzáférési engedélyeket határozzanak meg, amelyek csak a felhasználóknak a feladataik elvégzéséhez szükséges hozzáférést biztosítják. Ahelyett, hogy minden felhasználó számára korlátlan engedélyt adna az Azure-erőforrásokhoz, a rendszergazdák csak bizonyos műveleteket engedélyezhetik a kártyatulajdonos-adatok eléréséhez. Az előfizetés-hozzáférés az előfizetés rendszergazdájára korlátozódik.
- Azure Active Directory Privileged Identity Management lehetővé teszi, hogy az ügyfelek csökkentsék azon felhasználók számát, akik hozzáféréssel rendelkeznek bizonyos adatokhoz, például a kártyatulajdonos adataihoz. A rendszergazdák Azure Active Directory Privileged Identity Management használhatják az emelt szintű identitások felderítését, korlátozását és figyelését, valamint az erőforrásokhoz való hozzáférésüket. Ez a funkció az igény szerinti, igény szerinti rendszergazdai hozzáférés biztosítására is használható, ha szükséges.
- Azure Active Directory Identity Protection észleli a szervezet identitásait érintő lehetséges biztonsági réseket, automatikus válaszokat konfigurál a szervezet identitásával kapcsolatos gyanús műveletekre, és megvizsgálja a gyanús a megfelelő lépéseket a megoldásához.
### <a name="secrets-management"></a>Titkok kezelése
A megoldás a kulcsok és titkok kezeléséhez Azure Key Vault használ. Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. Az alábbi Azure Key Vault-funkciók segítenek az ügyfeleknek az ilyen jellegű adatvédelemben és hozzáférésben
   - A speciális hozzáférési szabályzatok a szükséges módon vannak konfigurálva.
   - Key Vault hozzáférési házirendek minimálisan szükséges engedélyekkel vannak definiálva a kulcsokhoz és a titkokhoz.
   - Key Vault összes kulcsának és titkának lejárati dátuma van.
   - A Key Vault összes kulcsát speciális hardveres biztonsági modulok védik. A kulcs típusa a hardveres biztonsági modul (HSM) által védett 2048 bites RSA-kulcs.
   - A Key Vault segítségével titkosíthatja a kulcsokat és a titkos kulcsokat (például a hitelesítési kulcsokat, a Storage-fiók kulcsait, az adattitkosítási kulcsokat). PFX-fájlok és jelszavak) a hardveres biztonsági modulok (HSM-EK) által védett kulcsok használatával. 
   - Szerepköralapú Access Control (RBAC) használatával rendeljen engedélyeket a felhasználókhoz, csoportokhoz és alkalmazásokhoz egy adott hatókörben.     
   - A TLS-tanúsítványok automatikus megújítással történő kezeléséhez használja a Key Vault. 
   - Key Vault diagnosztikai naplói legalább 365 napos megőrzési időtartammal engedélyezettek.
   - A kulcsok számára engedélyezett titkosítási műveletek csak a szükségesek.
### <a name="azure-security-center"></a>Azure Security Center
A Azure Security Center segítségével az ügyfelek központilag alkalmazhatják és kezelhetik a munkaterhelések biztonsági szabályzatait, korlátozhatja a fenyegetéseket, és észlelheti és reagálhat a támadásokra. Emellett 
   - Azure Security Center elérhetővé teszi az Azure-szolgáltatások meglévő konfigurációit, hogy konfigurációs és szolgáltatási javaslatokat nyújtson a biztonsági helyzetek és az adatvédelem javításához.
   - Azure Security Center különböző észlelési képességekkel figyelmezteti az ügyfeleket a környezetekhez kapcsolódó lehetséges támadásokra. Ezek a riasztások értékes információkat tartalmaznak arról, hogy mi váltotta ki a riasztást, valamint a támadás forrásáról és az általa célba vett erőforrásokról. A Azure Security Center előre meghatározott biztonsági riasztásokkal rendelkezik, amelyek egy fenyegetés vagy gyanús tevékenység bekövetkeztekor aktiválódnak. A Azure Security Center egyéni riasztási szabályai lehetővé teszik, hogy az ügyfelek új biztonsági riasztásokat határozzanak meg a környezetből már összegyűjtött adatok alapján.
   - A Azure Security Center rangsorolt biztonsági riasztásokat és incidenseket biztosít, így egyszerűbbé válik az ügyfelek számára a potenciális biztonsági problémák felderítése és kezelése. Az egyes észlelt fenyegetésekkel kapcsolatos fenyegetési intelligenciáról szóló jelentés jön létre, amely segít az incidensek megválaszolásában a fenyegetések kivizsgálásában és szervizelését.
### <a name="azure-application-gateway"></a>Azure Application Gateway 
   Az architektúra csökkenti a biztonsági rések kockázatát egy olyan Azure-Application Gateway használatával, amely konfigurálva van egy webalkalmazási tűzfallal, és a OWASP szabályrendszert engedélyezve van. A további funkciók közé tartoznak a következők:
   - Végpontok közötti SSL.
   - Tiltsa le a TLS 1.0-s és 1.1-es verzióit.
   - Engedélyezze a TLS 1.2-es verzióját.
   - Webalkalmazási tűzfal (megelőzési mód).
   - OWASP 3,0-es szabályrendszert biztosító megelőzési mód.
   - Diagnosztikai naplózás engedélyezése.
   - Egyéni állapot-mintavételek.
   - A Azure Security Center és az Azure Advisor további védelmet és értesítéseket biztosítanak. A Azure Security Center a hírnév rendszerét is biztosítja.
### <a name="logging-and-auditing"></a>Naplózás
Az Azure-szolgáltatások széles körben naplózzák a rendszer és a felhasználó tevékenységét, valamint a rendszer állapotát:
   - Tevékenységnaplók: a [Tevékenységnaplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) betekintést nyújtanak az előfizetésben lévő erőforrásokon végrehajtott műveletekre. A Tevékenységnaplók segítenek meghatározni a művelet kezdeményezőjét, az előfordulás időpontját és az állapotot.
   - Diagnosztikai naplók: a [diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) a minden erőforrás által kibocsátott összes naplót tartalmazzák. Ezek a naplók a Windows-eseménynaplókat, az Azure Storage-naplókat, a Key Vault naplókat, valamint Application Gateway hozzáférési és tűzfal-naplókat tartalmaznak. Az összes diagnosztikai napló egy központi és titkosított Azure Storage-fiókba írja az archiválást. A megőrzés a felhasználó által konfigurálható, akár 730 nap, hogy megfeleljen a szervezetre vonatkozó megőrzési követelményeknek.
### <a name="azure-monitor-logs"></a>Azure Monitor-naplók
   Ezeket a naplókat a rendszer a feldolgozás, tárolás és irányítópult-jelentéskészítés [Azure monitor naplófájljaiban](https://azure.microsoft.com/services/log-analytics/) összesíti. Az adatgyűjtés után a rendszer külön táblákba rendezi az adatokat Log Analytics munkaterületeken belül minden adattípushoz, ami lehetővé teszi, hogy az összes adatokat együtt elemezze az eredeti forrástól függetlenül. Emellett a Azure Security Center integrálható Azure Monitor naplókkal, így az ügyfelek Kusto-lekérdezéseket használhatnak a biztonsági események adatainak eléréséhez és más szolgáltatásokból származó adatokkal való összekapcsolásához.

   Az architektúra részeként az alábbi Azure- [figyelési megoldások](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) szerepelnek

   - [Active Directory Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): a Active Directory Health-ellenőrzési megoldás rendszeres időközönként kivizsgálja a kiszolgálói környezetek kockázatait és állapotát, és rangsorolja a telepített kiszolgálói infrastruktúrára jellemző ajánlásokat.
   - [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): a Agent Health-megoldás azt jelenti, hogy hány ügynök van üzembe helyezve, valamint azok földrajzi eloszlása, valamint hány ügynök, amely nem válaszol, valamint az ügynökök száma, amelyek operatív adatküldést küldenek.
   - [Activity log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): a Activity log Analytics megoldás segítséget nyújt az Azure-tevékenységek naplóinak elemzésében az ügyfelekhez tartozó összes Azure-előfizetésen belül.
### <a name="azure-monitor"></a>Azure Monitor
   [Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/)segíti a felhasználókat a teljesítmény nyomon követésében, a biztonság fenntartásában és a trendek azonosításában azáltal, hogy lehetővé teszi a szervezetek számára, hogy naplózzák, riasztásokat hozzon létre és archiválják az adatok archiválását, beleértve az Azure
### <a name="application-insights"></a>Application Insights 
   A [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) egy bővíthető Application Performance Management szolgáltatás a webes fejlesztők számára több platformon. Application Insights észleli a teljesítménnyel kapcsolatos rendellenességeket, és az ügyfelek használhatják az élő webalkalmazás figyelését. Hatékony elemzési eszközöket tartalmaz, amelyek segítségével az ügyfelek diagnosztizálják a problémákat, és megtudhatják, hogy a felhasználók hogyan használják ténylegesen az alkalmazást. A megoldás célja, hogy az ügyfelek folyamatosan javítsák a teljesítményt és a használhatóságot.

### <a name="azure-key-vault"></a>Azure Key Vault
   Hozzon létre egy tárolót ahhoz a szervezethez, amelyben a kulcsokat tárolni kívánja, és őrizze meg az alábbi működési feladatok elszámoltathatóságát

   - A Key Vaultban tárolt adatkészletek tartalmazzák a következőket:   
   - Alkalmazás Insight-kulcsa
   - Adattároló-hozzáférési kulcs
   - Kapcsolati sztring
   - Adattábla neve
   - Felhasználói hitelesítő adatok
   - A speciális hozzáférési szabályzatok igény szerint vannak konfigurálva
   - Key Vault hozzáférési szabályzatok minimálisan szükséges engedélyekkel rendelkeznek a kulcsok és a titkos kulcsokhoz
   - A Key Vault összes kulcsának és titkának lejárati dátuma
   - Key Vault összes kulcsát a hardveres biztonsági modul (HSM) védi [kulcs típusa = hardveres biztonsági modul (HSM) által védett       
     2048 – bites RSA-kulcs]
   - Minden felhasználó/identitás minimálisan szükséges engedélyeket kap a szerepköralapú Access Control (RBAC) használatával
   - Az alkalmazások nem osztják meg Key Vault, hacsak nem bíznak egymással, és a futtatáskor ugyanazokat a titkokat kell elérniük
   - Key Vault diagnosztikai naplói legalább 365 napos megőrzési időtartammal engedélyezettek.
   - A kulcsok számára engedélyezett titkosítási műveletek a szükséges értékekre vannak korlátozva

### <a name="vpn-and-expressroute"></a>VPN-és ExpressRoute
   Biztonságos VPN-alagutat vagy [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) kell konfigurálni a jelen Pásti-webalkalmazás-hivatkozási architektúra részeként üzembe helyezett erőforrásokkal létesített kapcsolat biztonságos létrehozásával. A VPN-vagy ExpressRoute megfelelő beállításával az ügyfelek hozzáadhatnak egy védelmi réteget az átvitelhez.

   A biztonságos VPN-alagút az Azure-nal való megvalósításával létrehozhat egy helyszíni hálózat és egy Azure-Virtual Network közötti virtuális magánhálózati kapcsolatot. Ez a kapcsolat az interneten keresztül történik, és lehetővé teszi az ügyfelek számára, hogy az ügyfél hálózata és az Azure közötti titkosított kapcsolaton belül biztonságosan "alagút" információkat használjanak. A helyek közötti VPN egy olyan biztonságos, érett technológia, amelyet évtizedek óta minden méretben üzembe helyezett a vállalatok. Ebben a beállításban az IPsec-alagút mód titkosítási mechanizmusként van használatban.

   Mivel a VPN-alagúton belüli forgalom egy helyek közötti VPN-kapcsolaton keresztül halad át az interneten, a Microsoft egy másik, még biztonságosabb kapcsolódási lehetőséget kínál. Az Azure ExpressRoute egy dedikált WAN-kapcsolat az Azure és egy helyszíni hely vagy egy Exchange-szolgáltató között. Mivel a ExpressRoute-kapcsolatok nem az interneten keresztül haladnak át, ezek a kapcsolatok megbízhatóbbak, gyorsabbak, kisebb késések és nagyobb biztonságot biztosítanak, mint a szokásos kapcsolatok az interneten keresztül. Továbbá, mivel ez az ügyfél távközlési szolgáltatójának közvetlen kapcsolata, az adat nem az interneten keresztül történik, ezért nem lesz elérhető.

   Ajánlott eljárások egy biztonságos hibrid hálózat megvalósításához, amely kiterjeszti a helyszíni hálózatot az Azure- [ra.](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)

#### <a name="implement-azure-active-directory-oidc"></a>Azure Active Directory OIDC megvalósítása

1. A forráskód-tárház klónozásához használja ezt a git-parancsot

 ``` git
 git clone https://github.com/Azure-Samples/AAD-Security
   ```
## <a name="update-the-redirect-urls"></a>Az átirányítási URL-címek frissítése
1.  Váltson vissza a Azure Portal. A bal oldali navigációs ablaktáblán válassza ki a Azure Active Directory szolgáltatást, majd válassza a Alkalmazásregisztrációk lehetőséget.
2.  Az eredő képernyőn válassza ki a WebApp-OpenIDConnect-DotNet-Code-v2 alkalmazást.
3.  Az URI-k átirányítása szakaszban található hitelesítés lapon válassza a web elemet a kombinált listában, és adja hozzá a következő átirányítási URI-ket.
    https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net/signin-oidc o a speciális beállítások szakaszban a kijelentkezési URL-cím beállítása https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net/signout-oidc
4.  A branding (védjegyezés) lapon frissítse a Kezdőlap URL-címét az App Service címére, például https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net.
        o mentse a konfigurációt.
5.  Ha az alkalmazás webes API-t hív meg, ügyeljen arra, hogy alkalmazza a szükséges módosításokat a appSettings. JSON projektben, hogy a a localhost helyett meghívja a közzétett API URL-címet.
A minta közzététele
    1.  A App Service áttekintés lapján töltse le a közzétételi profilt a közzétételi profil beolvasása hivatkozásra kattintva, és mentse. Más üzembe helyezési mechanizmusok, például a verziókövetés, is használhatók.
    2.  Váltson a Visual Studióra, és lépjen a WebApp-OpenIDConnect-DotNet-Code-v2 projekthez. Kattintson a jobb gombbal a projektre a Megoldáskezelő, majd válassza a közzététel lehetőséget. Kattintson az alsó sávban a profil importálása elemre, és importálja a korábban letöltött közzétételi profilt.
    3.  Kattintson a konfigurálás elemre, és a kapcsolat lapon frissítse a cél URL-címet úgy, hogy az egy https legyen a Kezdőlap URL-címében, például https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net. Kattintson a Next (Tovább) gombra.
    4.  A beállítások lapon győződjön meg arról, hogy nincs bejelölve a szervezeti hitelesítés engedélyezése jelölőnégyzet. Kattintson a Mentés gombra. Kattintson a közzététel elemre a fő képernyőn.
    5.  A Visual Studio közzéteszi a projektet, és automatikusan megnyit egy böngészőt a projekt URL-címére. Ha a projekt alapértelmezett weblapja jelenik meg, a kiadvány sikeres volt.
#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>Multi-Factor Authentication implementálása Azure Active Directory
   A rendszergazdáknak biztosítaniuk kell, hogy a portálon lévő előfizetési fiókok védve legyenek. Az előfizetés sebezhető a támadásokkal szemben, mert kezeli a létrehozott erőforrásokat. Az előfizetés védelemmel való ellátásához engedélyezze a Multi-Factor Authentication az előfizetés **Azure Active Directory** lapján.

   Az Azure AD olyan szabályzatok alapján működik, amelyek egy adott feltételnek megfelelő felhasználóra vagy felhasználói csoportokra vonatkoznak.
Az Azure egy alapértelmezett szabályzatot hoz létre, amely megadja, hogy a rendszergazdáknak kétfaktoros hitelesítésre van szükségük a portálra való bejelentkezéshez.
A házirend engedélyezése után a rendszer felszólítja a kijelentkezésre, majd a Azure Portalba való bejelentkezésre.

Az MFA engedélyezése rendszergazdai bejelentkezésekhez

   1. Lépjen a **Azure Active Directory** lapra a Azure Portal
   2. A biztonság kategóriában válassza a feltételes hozzáférés lehetőséget. Ezt a képernyőt látja

       ![Feltételes hozzáférés – házirendek](./media/secure-aad-app/ad-mfa-conditional-add.png)

Ha nem tud új szabályzatot létrehozni

   1. Nyissa meg az **MFA** lapot.
   2. Válasszon ki egy prémium szintű Azure AD **ingyenes próbaverzió** hivatkozást az ingyenes próbaverzióra való előfizetéshez.

   ![prémium szintű Azure AD ingyenes próbaverzió](./media/secure-aad-app/ad-trial-premium.png)

Térjen vissza a feltételes hozzáférés képernyőre.

   1. Válassza az új házirend lapot.
   2. Adja meg a házirend nevét.
   3. Válassza ki azokat a felhasználókat vagy csoportokat, amelyeknek engedélyezni szeretné az MFA-t.
   4. A **hozzáférés-vezérlés**területen válassza az **Engedélyezés** lapot, majd válassza a **többtényezős hitelesítés megkövetelése** (és egyéb beállítások) lehetőséget.

   ![MFA megkövetelése](./media/secure-aad-app/ad-mfa-conditional-add.png)

   A házirendet a képernyő felső részén található jelölőnégyzet bejelölésével vagy a **feltételes hozzáférés** lapon is engedélyezheti. Ha a házirend engedélyezve van, a felhasználóknak MFA-ra van szükségük a portálra való bejelentkezéshez.

   Létezik egy alapkonfigurációra vonatkozó szabályzat, amely minden Azure-rendszergazda esetében MFA-t igényel. Azonnal engedélyezheti a portálon. Előfordulhat, hogy a házirend engedélyezése érvényteleníti az aktuális munkamenetet, és ismét be kell jelentkeznie.

   Ha az alapkonfiguráció házirendje nincs engedélyezve
   1.   Válassza **az MFA megkövetelése rendszergazdák számára**lehetőséget.
   2.   Válassza a **házirend azonnali használata**lehetőséget.

   ![Válassza a házirend azonnali használata lehetőséget](./media/secure-aad-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>Alkalmazások és erőforrások figyelése az Azure Sentinel használatával

   Mivel az alkalmazás növekszik, nehéz lesz összesíteni az erőforrásoktól kapott összes biztonsági jelet és mérőszámot, és azok működés közbeni módon elérhetővé válnak.

   Az Azure Sentinel úgy lett kialakítva, hogy adatokat gyűjtsön, észlelje a fenyegetések típusait, és betekintést nyújtson a biztonsági incidensekre.
Amíg a manuális beavatkozásra vár, az Azure Sentinel előre megírt forgatókönyvekkel is hivatkozhat a riasztások és az incidensek kezelési folyamatainak kiindítására.

   A minta alkalmazás több, az Azure Sentinel által figyelhető erőforrásból áll.
Az Azure Sentinel beállításához először létre kell hoznia egy Log Analytics munkaterületet, amely a különböző erőforrásokból gyűjtött összes adatokat tárolja.

A munkaterület létrehozása

   1. A Azure Portal keresési mezőjében keresse meg a **log Analytics**. Válassza **log Analytics munkaterületek**lehetőséget.

   ![Log Analytics munkaterületek keresése](./media/secure-aad-app/sentinel-log-analytics.png)

   *Log Analytics munkaterületek keresése*

   2. A következő lapon válassza a **Hozzáadás** lehetőséget, majd adjon meg egy nevet, egy erőforráscsoportot és egy helyet a munkaterületnek.
   ![Log Analytics munkaterület létrehozása](./media/secure-aad-app/sentinel-log-analytics-create.png)

   *Log Analytics munkaterület létrehozása*

   3. Az **Azure Sentinel**kereséséhez használja a keresőmezőt.

   ![Az Azure Sentinel keresése](./media/secure-aad-app/sentinel-add.png)

   *Az Azure Sentinel keresése*

   4. Válassza a **Hozzáadás** lehetőséget, majd válassza ki a korábban létrehozott log Analytics munkaterületet.

   ![Log Analytics munkaterület hozzáadása](./media/secure-aad-app/sentinel-workspace-add.png)

   *Log Analytics munkaterület hozzáadása*

   5. Az **Azure Sentinel-adatösszekötők** lap **konfiguráció**területén válassza az **adatösszekötők**lehetőséget. Egy Azure-szolgáltatásokból álló tömb jelenik meg, amely a Log Analytics Storage-példányhoz kapcsolható az Azure Sentinel elemzéséhez.

   ![Log Analytics adatösszekötők](./media/secure-aad-app/sentinel-connectors.png)

      *Adatösszekötő hozzáadása az Azure Sentinelhez*

   Például az Application Gateway összekapcsolásához hajtsa végre a következő lépéseket:

   1. Nyissa meg az Azure Application Gateway példány panelt.
   2. A **figyelés**területen válassza a **diagnosztikai beállítások**elemet.
   3. Válassza a **diagnosztikai beállítás hozzáadása**lehetőséget.

   ![Application Gateway diagnosztika hozzáadása](./media/secure-aad-app/sentinel-gateway-connector.png)
         
   *Application Gateway diagnosztika hozzáadása*

   4. A **diagnosztikai beállítások** lapon válassza ki a létrehozott log Analytics munkaterületet, majd válassza ki az összes összegyűjteni kívánt metrikát, és küldje el az Azure sentinelnek. Kattintson a **Mentés** gombra.

   ![Azure Sentinel-összekötő beállításai](./media/secure-aad-app/sentinel-connector-settings.png)



## <a name="cost-considerations"></a>Költségekkel kapcsolatos szempontok
   Ha még nem rendelkezik Azure-fiókkal, akkor létrehozhat egy ingyenes fiókot. A kezdéshez nyissa meg az [ingyenes fiók lapot](https://azure.microsoft.com/free/) , és tekintse meg, hogy mit tehet egy ingyenes Azure-fiókkal, és hogy mely termékek ingyenesek 12 hónapig.

   Ha a minta alkalmazásban lévő erőforrásokat a biztonsági funkciókkal szeretné üzembe helyezni, a prémium funkciókért kell fizetnie. Az alkalmazások skálázása, valamint az Azure által kínált ingyenes csomagok és kísérletek frissítése az alkalmazásokra vonatkozó követelmények kielégítése érdekében várható, hogy a költségek növekednek. A költségek becsléséhez használja az Azure [díjszabási számológépét](https://azure.microsoft.com/pricing/calculator/) .

## <a name="next-steps"></a>Következő lépések
   A következő cikkek segíthetnek a biztonságos alkalmazások megtervezésében, fejlesztésében és üzembe helyezésében.

- [Tervezési](secure-design.md)
- [Fejlesztés](secure-develop.md)
- [Üzembe helyezés](secure-deploy.md)
