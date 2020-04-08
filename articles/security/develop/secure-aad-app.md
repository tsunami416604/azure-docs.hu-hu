---
title: Biztonságos Azure AD webalkalmazás fejlesztése | Microsoft dokumentumok
description: Ez az egyszerű mintaalkalmazás olyan biztonsági gyakorlati tanácsokat valósít meg, amelyek javítják az alkalmazást és a szervezet biztonsági állapotát az Azure-ban való fejlesztés során.
keywords: na
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
ms.openlocfilehash: 599c4a31840b47294b43c4c4d1f0200b17f04540
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80810545"
---
# <a name="develop-secure-app-for-an-azure-ad-app"></a>Biztonságos alkalmazás fejlesztése egy Azure AD-alkalmazáshoz
## <a name="overview"></a>Áttekintés

Ez a minta egy egyszerű Azure Active Directory webalkalmazással, amely az Azure-beli alkalmazások fejlesztéséhez szükséges biztonsági erőforrásokra hivatkozik. Az alkalmazás olyan biztonsági gyakorlati tanácsokat valósít meg, amelyek segíthetnek az alkalmazás és a szervezet biztonsági helyzetének javításában, amikor alkalmazásokat fejleszt az Azure-ban.

A központi telepítési parancsfájlok beállítják az infrastruktúrát. A központi telepítési parancsfájlok futtatása után az Azure Portalon manuálisan kell konfigurálnia az összetevők és szolgáltatások összekapcsolását. Ez a minta az Azure tapasztalt fejlesztőinek szól, akik a kiskereskedelmi ágazatban dolgoznak, és biztonságos Azure-infrastruktúrával rendelkező biztonságos Azure Active Directoryt szeretnének építeni. 


Az alkalmazás fejlesztése és üzembe helyezése során megtudhatja, hogyan 
- Hozzon létre egy Azure Key Vault-példányt, tárolja és olvassa le a titkokat.
- Telepítse az Azure Web App, amely dedikált elkülönített előtér-tűzfal-hozzáféréssel. 
- Hozzon létre és konfiguráljon egy Azure Application Gateway-példányt egy oWASP Top 10 ruleset használó tűzfallal. 
- Engedélyezze az adatok titkosítását az átvitel és az inaktív szolgáltatások használatával. 
- Állítsa be az Azure-szabályzat és biztonsági központ a megfelelőségek kiértékeléséhez. 

Az alkalmazás fejlesztése és üzembe helyezése után a következő minta webalkalmazást, valamint a leírt konfigurációs és biztonsági intézkedéseket fogja beállítani.

## <a name="architecture"></a>Architektúra
Az alkalmazás egy tipikus n-szintű alkalmazás három réteggel. Az előtér, a háttérrendszer és az integrált figyelési és titkos felügyeleti összetevőket tartalmazó adatbázisréteg itt látható:

![Alkalmazásarchitektúra](./media/secure-aad-app/architecture.png)

Ez a megoldás a következő Azure-szolgáltatásokat használja. A központi telepítési architektúra részletei a Telepítési architektúra szakaszban találhatók. 

Az architektúra ezekből az összetevőkből áll

- [Az Azure Application Gateway](../../application-gateway/index.yml)alkalmazást. Biztosítja az átjárót és a tűzfalat az alkalmazásarchitektúrához.
- [Application Insights](../../azure-monitor/app/app-insights-overview.md). Bővíthető alkalmazásteljesítmény-kezelési (APM) szolgáltatást biztosít több platformon.
- [Az Azure Key Vault](../../key-vault/index.yml). Tárolja és titkosítja az alkalmazás titkosítatait, és kezeli a hozzáférési szabályzatok létrehozását körülöttük.
- [Az Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md). Felhőalapú identitás- és hozzáférés-kezelési szolgáltatást biztosít, bejelentkezést és hozzáférési erőforrásokat biztosít.
- [Azure tartománynév-rendszer](../../dns/dns-overview.md). Adja meg a tartomány üzemeltetéséhez.
- [Az Azure terheléselosztó](../../load-balancer/load-balancer-overview.md). Az alkalmazások méretezéséhez és a szolgáltatások magas rendelkezésre állásának létrehozásához.
- [Az Azure Web App](../../app-service/overview.md).  HTTP-alapú szolgáltatást biztosít a webalkalmazások üzemeltetéséhez.
- [Az Azure Security Center .](../../security-center/index.yml) Fejlett veszélyforrások elleni védelmet biztosít a felhőben lévő hibrid számítási feladatok között.
- [Azure-szabályzat](../../governance/policy/overview.md). Az erőforrások kiértékelése a hozzárendelt házirendeknek való meg nem felelés miatt.

## <a name="threat-model"></a>Fenyegetésmodell
A fenyegetésmodellezés az üzleti és alkalmazásra leselkedő potenciális biztonsági fenyegetések azonosításának folyamata, majd annak biztosítása, hogy megfelelő kockázatcsökkentési terv legyen érvényben.

Ez a minta a [Microsoft Threat Modeling Tool segítségével](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) valósította meg a biztonságos mintaalkalmazás fenyegetésmodellezését. Az összetevők és az adatfolyamok diagrammal azonosíthatja a problémákat és fenyegetéseket a fejlesztési folyamat korai szakaszában. Ezzel később időt és pénzt takarítunk meg ezzel a célzokkal.

Itt van a mintaalkalmazás fenyegetésmodellje

![Fenyegetésmodell](./media/secure-aad-app/threat-model.png)

Néhány minta fenyegetések és a potenciális biztonsági rések, amelyek a fenyegetés modellezési eszköz generálja a következő képernyőképen látható. A fenyegetésmodell áttekintést ad a támadási felület elérhetővé, és kéri a fejlesztőket, hogy gondolja át, hogyan lehet enyhíteni a problémákat.

![Fenyegetésmodell kimenete](./media/secure-aad-app/threat-model-output.png)

### <a name="prerequisites"></a>Előfeltételek
Az alkalmazás üzembe kapcsolásához telepítenie kell az alábbi eszközöket:

- Kódszerkesztő az alkalmazáskód módosításához és megtekintéséhez. [A Visual Studio Code](https://code.visualstudio.com/) egy nyílt forráskódú lehetőség.
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest,) a fejlesztői számítógépen.
- [Git](https://git-scm.com/) a rendszer. A Git a forráskód helyi klónozására szolgál.
- [jq](https://stedolan.github.io/jq/), egy UNIX eszköz a JSON felhasználóbarát lekérdezésére.

A mintaalkalmazás erőforrásainak üzembe helyezéséhez Azure-előfizetésre van szükség. Ha nem rendelkezik Azure-előfizetéssel, [létrehozhat egy ingyenes fiókot](https://azure.microsoft.com/free/) a mintaalkalmazás teszteléséhez.

Az eszközök telepítése után készen áll az alkalmazás azure-ra való üzembe helyezésére.

### <a name="implementation-guidance"></a>Végrehajtási útmutató
A központi telepítési parancsfájl egy parancsfájl, amely négy fázisra bontható. Minden fázis telepíti és konfigurálja az [architektúradiagramban](#architecture)található Azure-erőforrást.

A négy fázis

- Telepítse az Azure Key Vaultot.
- Telepítse az Azure Web Apps alkalmazásokat.
- Telepítse az Application Gateway alkalmazást webalkalmazás-tűzfallal.
- Konfigurálja az Azure AD telepített alkalmazással.

Minden fázis az előzőre épül a korábban üzembe helyezett erőforrások konfigurációjának használatával.

A megvalósítási lépések végrehajtásához győződjön meg arról, hogy telepítette az [Előfeltételek](#prerequisites)csoportban felsorolt eszközöket.

#### <a name="deploy-azure-key-vault"></a>Az Azure Key Vault telepítése
Ebben a szakaszban hozzon létre és telepítsen egy Azure Key Vault-példányt, amely titkos kulcsok és tanúsítványok tárolására szolgál.

Miután befejezte a központi telepítést, rendelkezik egy Azure Key Vault-példány üzembe helyezésével az Azure-ban.

Az Azure Key Vault üzembe helyezése a PowerShell használatával
 
1. Deklarálja az Azure Key Vault változóit.
2. Regisztrálja az Azure Key Vault-szolgáltatót.
3. Hozza létre a példány erőforráscsoportját.
4. Hozza létre az Azure Key Vault-példányt a 3.

#### <a name="the-below-azure-ad-user-will-have-admin-permissions-to-the-key-vault"></a>Az alábbi Azure AD-felhasználó rendszergazdai engedélyekkel fog rendelkezni a Key Vaulthoz
    $keyVaultAdminUsers = @($user1,user2)

#### <a name="register-the-az-providers"></a>Az Az szolgáltatók regisztrálása
    Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault

#### <a name="create-the-azure-key-vault-instance"></a>Az Azure Key Vault-példány létrehozása
    New-AzKeyVault -Name $kvName 
                -ResourceGroupName $ResourceGroup 
                -Location 'East US'
                -EnabledForDiskEncryption

#### <a name="add-the-administrator-policies-to-the-key-vault"></a>A rendszergazdai házirendek hozzáadása a Key Vaulthoz
    foreach ($keyVaultAdminUser in $keyVaultAdminUsers) {
    $UserObjectId = (Get-AzADUser -SearchString $keyVaultAdminUser).Id
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ResourceGroupName $resourceGroupName -ObjectId $UserObjectId 
    -PermissionsToKeys all -PermissionsToSecrets all -PermissionsToCertificates all
    }

#### <a name="to-create-an-access-policy-to-allow-a-user-to-get-and-list-cryptographic-keys-certificates-and-secrets-if-you-know-the-user-principal-name"></a>Hozzáférési házirend létrehozása, amely lehetővé teszi a felhasználó számára, hogy kriptográfiai kulcsokat, tanúsítványokat és titkos kulcsokat kapjon és listázzon, ha ismeri az egyszerű felhasználónevet:
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName 
                           -ResourceGroupName $resourceGroupName 
                           -UserPrincipalName 'user1@contoso.com 
                           -PermissionsToCertificates list, get 
                           -PermissionsToKeys list, get 
                           -PermissionsToSecrets list, get 

Ajánlott eljárás az Azure-erőforrások felügyelt identitások használata olyan alkalmazásokban, amelyek key vault használatával erőforrások eléréséhez. A biztonsági állapot növekszik, ha a Key Vault hozzáférési kulcsai nem kódvagy konfiguráció ban tárolódnak.

A tárolóban egy főtanúsítvány is található. A tanúsítvány megszerzése érdekében tett lépések

1. Töltse le a tanúsítványfájlt a [hitelesítésszolgáltatótól.](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt)
2. A tanúsítványfájl dekódolása:

   ```powershell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```
Ez a parancsfájl létrehoz egy hozzárendelt identitást az App Service-példány, amely az MSI-vel használható az Azure Key Vault használatával, kód vagy konfiguráció ban titkos kulcsok szigorú kódolása nélkül.

Nyissa meg az Azure Key Vault-példányt a portálon, hogy engedélyezze a hozzárendelt identitást a hozzáférési szabályzat **lapon.** Az **Egyszerű kiválasztása csoportban**keresse meg a létrehozott App Service-példány nevéhez hasonló alkalmazásnevet.
Az alkalmazáshoz csatolt egyszerű szolgáltatásnak láthatónak kell lennie. Jelölje ki, és mentse a hozzáférési szabályzat lapját, ahogy az az alábbi képernyőképen látható.

Mivel az alkalmazásnak csak kulcsokat kell letöltenie, válassza az Engedély **beolvasása** a titkos kulcsok beállításokat, amely engedélyezi a hozzáférést, miközben csökkenti a megadott jogosultságokat.

![A Key Vault hozzáférési irányelvei](./media/secure-aad-app/kv-access-policy.png)

*Key Vault-hozzáférési házirend létrehozása*

Mentse a hozzáférési házirendet, majd mentse az új módosítást a **Hozzáférési házirendek** lapon a házirendek frissítéséhez.

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>Alkalmazásátjáró központi telepítése engedélyezve a webalkalmazás tűzfalával
A webes alkalmazásokban nem ajánlott a szolgáltatásokat közvetlenül a külvilág számára elérhetővé tenni az interneten.
A terheléselosztás és a tűzfalszabályok nagyobb biztonságot nyújtanak, és szabályozhatják a bejövő forgalmat, és segítenek annak kezelésében.

Alkalmazásátjáró-példány telepítése

1. Hozza létre az erőforráscsoportot az alkalmazásátjáró elhelyezésére.
2. Az átjáróhoz csatolandó virtuális hálózat kiépítése.
3. Hozzon létre egy alhálózatot az átjáróhoz a virtuális hálózatban.
4. Nyilvános IP-cím kiépítése.
5. Az alkalmazásátjáró kiépítése.
6. Engedélyezze a webalkalmazás tűzfalát az átjárón.

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

#Upload the certificate to be used on the TLS/SSL-enabled back-end pool resources

#$authcert = New-AzApplicationGatewayAuthenticationCertificate -Name 'allowlistcert1' -CertificateFile C:\cert.cer

$trustedRootCert01 = New-AzApplicationGatewayTrustedRootCertificate -Name "test1" -CertificateFile "C:\AAD\Securities\Certificates\sslcert.com.cer"

#Configure the HTTP settings for the application gateway back end

$poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name "setting01" -Port 443 -Protocol Https -CookieBasedAffinity Disabled -TrustedRootCertificate $trustedRootCert01 -HostName "test1"

#Create a load-balancer routing rule that configures the load balancer

$rule = New-AzApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

#Configure the instance size of the application gateway

$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

#Configure the TLS/SSL policy to be used on the application gateway

$SSLPolicy = New-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -PolicyType Custom

$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "East US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -TrustedRootCertificate $trustedRootCert01 -Verbose

```

#### <a name="deploy-azure-web-apps"></a>Azure webalkalmazások telepítése
Az Azure App Service lehetővé teszi, hogy webes alkalmazásokat hozzon létre és üzemeltetjen olyan nyelvek használatával, mint a Python, a Ruby, a C#és a Java. Az Azure egyéni tárolókat is támogat, amelyek gyakorlatilag az összes programozási nyelv futtatását lehetővé teszik az Azure App Service platformon.

#### <a name="create-an-app-service-plan-in-free-tier"></a>App Szolgáltatási csomag létrehozása az ingyenes csomagban
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName $webappname -Tier Free

#### <a name="create-a-web-app"></a>Webalkalmazás létrehozása
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName $webappname

    Write-Host "Configure a CNAME record that maps $fqdn to $webappname.azurewebsites.net"
    Read-Host "Press [Enter] key when ready ..."

#### <a name="before-continuing-go-to-your-azure-domain-name-system-configuration-ui-for-your-custom-domain-and-follow-the-instructions-at-httpsakamsappservicecustomdns-to-configure-a-cname-record-for-the-hostname-www-and-point-it-your-web-apps-default-domain-name"></a>A folytatás előtt nyissa meg az Egyéni tartományhoz tartozó Azure Domain https://aka.ms/appservicecustomdns Name System konfigurációs felhasználói felületét, és kövesse az utasításokat a "www" állomásnév CNAME rekordjának konfigurálásához, és mutassa meg a webalkalmazás alapértelmezett tartománynevét.

#### <a name="upgrade-app-service-plan-to-shared-tier-minimum-required-by-custom-domains"></a>Az App Service-csomag frissítése megosztott szintre (az egyéni tartományok által minimálisan szükséges)
    Set-AzAppServicePlan -Name $webappname -ResourceGroupName $webappname -Tier Shared

#### <a name="add-a-custom-domain-name-to-the-web-app"></a>Egyéni tartománynév hozzáadása a webalkalmazáshoz
    Set-AzWebApp -Name $webappname -ResourceGroupName $webappname `-HostNames @($fqdn,"$webappname.azurewebsites.net")

## <a name="guidance-and-recommendations"></a>Útmutatás és ajánlások

### <a name="network"></a>Network (Hálózat)
A központi telepítés befejezése után egy alkalmazásátjáró, amelyen engedélyezve van a webalkalmazás tűzfala.

Az átjárópéldány https-hez való 443-as portot teszi elérhetővé. Ez a konfiguráció biztosítja, hogy az alkalmazás unk csak a 443-as https-porton érhető el.

A nem használt portok blokkolása és a támadási felület expozíciójának korlátozása biztonsági gyakorlat.

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>Hálózati biztonsági csoportok hozzáadása az App Service-példányhoz

Az App Service-példányok integrálhatók a virtuális hálózatokkal. Ez az integráció lehetővé teszi, hogy konfigurálható a hálózati biztonsági csoport házirendek, amelyek kezelik az alkalmazás bejövő és kimenő forgalmat.

1. A funkció engedélyezéséhez az Azure App szolgáltatáspéldány **panelen**a Beállítások csoportban válassza a **Hálózat lehetőséget.** A jobb oldali ablaktáblában konfigurálja a **VNet-integráció csoportban.**

   ![Új virtuális hálózati integráció](./media/secure-web-app/app-vnet-menu.png)

    *Új virtuális hálózati integráció az App Service-hez*
1. A következő lapon válassza a **VNET hozzáadása (előnézet) lehetőséget.**

1. A következő menüben válassza ki a telepítésben `aad-vnet`létrehozott virtuális hálózatot, amely a alkalmazáslal kezdődik. Létrehozhat egy új alhálózatot, vagy kijelölhet egy meglévőt.
   Ebben az esetben hozzon létre egy új alhálózatot. Állítsa a **Cím tartományt** **10.0.3.0/24-re,** és nevezze el az alhálózati **alkalmazás-alhálózatot.**

   ![Az App Service virtuális hálózati konfigurációja](./media/secure-web-app/app-vnet-config.png)

    *Virtuális hálózati konfiguráció az App Service-hez*

Most, hogy engedélyezte a virtuális hálózati integrációt, hálózati biztonsági csoportokat adhat az alkalmazáshoz.

1. Használja a keresőmezőt, keressen **hálózati biztonsági csoportokat**. Az eredmények között válassza a **Hálózati biztonsági csoportok** lehetőséget.

    ![Hálózati biztonsági csoportok keresése](./media/secure-web-app/nsg-search-menu.png)

    *Hálózati biztonsági csoportok keresése*

2. A következő menüben válassza a **Hozzáadás parancsot.** Adja meg annak az NSG-nek és **erőforráscsoportnak** a **nevét,** amelyben lennie kell. Ez az NSG az alkalmazásátjáró alhálózatára lesz alkalmazva.

    ![NSG létrehozása](./media/secure-web-app/nsg-create-new.png)

    *NSG létrehozása*

3. Az NSG létrehozása után válassza ki. A panelen a **Beállítások**csoportban válassza **a Bejövő biztonsági szabályok lehetőséget.** Konfigurálja ezeket a beállításokat úgy, hogy a 443-as porton keresztül az alkalmazásátjáróba érkező kapcsolatok is elérhetők.

   ![Az NSG konfigurálása](./media/secure-web-app/nsg-gateway-config.png)

   *Az NSG konfigurálása*

4. Az átjáró NSG kimenő szabályaiban adjon hozzá egy szabályt, amely lehetővé teszi a kimenő kapcsolatokat az App Service-példányhoz egy olyan szabály létrehozásával, amely a szolgáltatáscímkét célozza`AppService`

   ![Kimenő szabályok hozzáadása az NSG-hez](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   *Kimenő szabályok hozzáadása az NSG-hez*

    Adjon hozzá egy másik kimenő szabályt, amely lehetővé teszi, hogy az átjáró kimenő szabályokat küldjön egy virtuális hálózatra.

   ![Másik kimenő szabály hozzáadása](./media/secure-web-app/nsg-outbound-vnet.png)

    *Másik kimenő szabály hozzáadása*

5. Az NSG alhálózati paneljén válassza a **Társítás**lehetőséget, válassza ki a központi telepítésben létrehozott virtuális hálózatot, és válassza ki a **gw-alhálózat nevű átjáró-alhálózatot.** Az NSG az alhálózatra van alkalmazva.

6. Hozzon létre egy másik NSG, mint a korábbi lépésben, ezúttal az App Service-példány. Mondj neki egy nevet. Adja hozzá a 443-as port bejövő szabályát, ahogy azt az nsg-hez az alkalmazásátjáróhoz.

   Ha egy App Service-példány telepítve van egy App Service-környezet-példány, amely nem ez a helyzet az alkalmazás, hozzáadhat bejövő szabályokat, amelyek lehetővé teszik az Azure Service Health-mintavételek megnyitásával portok 454-455 az App Service NSG bejövő biztonsági csoportok. Itt a konfiguráció:

   ![Szabályok hozzáadása az Azure Service Health-mintavételekhez](./media/secure-web-app/nsg-create-healthprobes.png)

    *Az Azure Service Health-mintavételek szabályainak hozzáadása (csak Az App Service-környezetben)*

A támadási felület korlátozásához módosítsa az App Service hálózati beállításait úgy, hogy csak az alkalmazásátjáró férhessen hozzá az alkalmazáshoz.
A beállítások alkalmazásához lépjen az App Service-hálózat fülre, jelölje ki az **IP-korlátozások** lapot, és hozzon létre egy engedélyezési szabályt, amely lehetővé teszi, hogy csak az alkalmazásátjáró IP-címe férhessen hozzá közvetlenül a szolgáltatáshoz. Az átjáró IP-címét az áttekintő lapról kérheti le. Az **IP-cím CIDR** lapján adja meg `<GATEWAY_IP_ADDRESS>/32`az IP-címet a következő formátumban: .

![Csak az átjáró engedélyezése](./media/secure-web-app/app-allow-gw-only.png)

*Csak az átjáró IP-címének engedélyezése az App Service eléréséhez*

### <a name="azure-domain-name-system"></a>Azure tartománynév-rendszer 
Az Azure domainnév-rendszer vagy az Azure domainnév-rendszer felelős a webhely vagy szolgáltatás nevének ip-címre fordításáért (vagy feloldásáért). Az Azure Domainhttps://docs.microsoft.com/azure/dns/dns-overview) Name System( egy üzemeltetési szolgáltatás a tartománynév-rendszer tartományok, amely névfeloldást azure-infrastruktúra használatával. A tartományok Azure-beli üzemeltetésével a felhasználók kezelhetik a tartománynév-rendszer rekordjait ugyanazzal a hitelesítő adatokkal, API-kkal, eszközökkel és számlázással, mint más Azure-szolgáltatások. Az Azure Domain Name System is támogatja a magán domain névrendszer tartományok.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption
Az Azure Disk Encryption a Windows BitLocker szolgáltatását használja az adatlemezek kötettitkosításának biztosításához. A megoldás integrálható az Azure Key Vault segítségével a lemeztitkosítási kulcsok vezérlése és kezelése.

### <a name="identity-management"></a>Identitáskezelés
A következő technológiák lehetővé tetszetést biztosítanak a kártyabirtokosi adatokhoz való hozzáférés kezeléséhez az Azure-környezetben
- Az Azure Active Directory a Microsoft több-bérlős felhőalapú címtár- és identitáskezelési szolgáltatása. A megoldás minden felhasználója az Azure Active Directoryban jön létre, beleértve az Azure WebApp-ot elérő felhasználókat is.
- Az Azure szerepköralapú hozzáférés-vezérlés lehetővé teszi a rendszergazdák számára, hogy részletes hozzáférési engedélyeket határozzanak meg, hogy csak annyi hozzáférést adjanak a felhasználóknak, amennyia feladataik elvégzéséhez szükséges. Ahelyett, hogy minden felhasználó nak korlátlan engedélyt az Azure-erőforrások, a rendszergazdák engedélyezhetik csak bizonyos műveletek eléréséhez kártyatulajdonos iadatok eléréséhez. Az előfizetési hozzáférés az előfizetés rendszergazdájára korlátozódik.
- Az Azure Active Directory kiemelt identitáskezelés lehetővé teszi az ügyfelek számára, hogy minimálisra csökkentsék a felhasználók száma, akik hozzáférnek bizonyos információkhoz, például a kártyatulajdonos adataihoz. A rendszergazdák az Azure Active Directory kiemelt identitáskezelés segítségével felderíthetik, korlátozhatják és figyelhetik a kiemelt identitásokat és az erőforrásokhoz való hozzáférésüket. Ez a funkció igény szerinti, igény szerinti rendszergazdai hozzáférés kényszerítésére is használható, ha szükséges.
- Az Azure Active Directory Identity Protection észleli a szervezet identitásait érintő potenciális biztonsági réseket, konfigurálja az automatikus válaszokat a szervezet identitásaihoz kapcsolódó gyanús műveletekre, és megvizsgálja a gyanús incidenseket, hogy megtegye a megfelelő lépéseket azok megoldásához.
### <a name="secrets-management"></a>Titkok kezelése
A megoldás az Azure Key Vault a kulcsok és a titkos kulcsok kezelésére. Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. A következő Azure Key Vault-funkciók segítenek az ügyfeleknek az ilyen adatok védelmében és elérésében
   - A speciális hozzáférési házirendek igény szerint vannak konfigurálva.
   - A Key Vault hozzáférési szabályzatai a kulcsokhoz és titkos kulcsokhoz szükséges minimális anamcsal vannak definiálva.
   - A Key Vault minden kulcsa és titkos kulcsa lejárati dátummal rendelkezik.
   - A Key Vault ban lévő összes kulcsot speciális hardveres biztonsági modulok védik. A kulcs típusa egy hardveres biztonsági modul (HSM) védett 2048 bites RSA kulcs.
   - A Key Vault segítségével titkosíthatja a kulcsokat és a titkos kulcsokat (például a hitelesítési kulcsokat, a tárfiók kulcsait, az adattitkosítási kulcsokat, a . PFX fájlok és jelszavak) hardveres biztonsági modulokkal (HSM) védett kulcsokkal. 
   - A szerepköralapú hozzáférés-vezérlés (RBAC) segítségével engedélyeket rendelhet a felhasználókhoz, csoportokhoz és alkalmazásokhoz egy adott hatókörben.     
   - A Key Vault segítségével automatikus megújítással kezelheti a TLS-tanúsítványokat. 
   - A Key Vault diagnosztikai naplói legalább 365 napos megőrzési időtartammal vannak engedélyezve.
   - A kulcsok engedélyezett titkosítási műveletei a szükséges kulcsokra korlátozódnak.
### <a name="azure-security-center"></a>Azure Security Center
Az Azure Security Center segítségével az ügyfelek központilag alkalmazhatják és kezelhetik a biztonsági házirendeket a számítási feladatok között, korlátozhatják a fenyegetéseknek való kitettséget, és észlelhetik és reagálhatnak a támadásokra. Továbbá 
   - Az Azure Security Center az Azure-szolgáltatások meglévő konfigurációihoz fér hozzá, hogy konfigurációs és szolgáltatási javaslatokat nyújtson a biztonsági testhelyzet javítása és az adatok védelme érdekében.
   - Az Azure Security Center számos észlelési lehetőséget használ, hogy figyelmeztesse az ügyfeleket a környezetüket célzó lehetséges támadásokra. Ezek a riasztások értékes információkat tartalmaznak arról, hogy mi váltotta ki a riasztást, valamint a támadás forrásáról és az általa célba vett erőforrásokról. Az Azure Security Center egy előre definiált biztonsági riasztások készletét, amelyek akkor aktiválódnak, ha egy fenyegetés, vagy gyanús tevékenység történik. Az Azure Security Center egyéni riasztási szabályai lehetővé teszik az ügyfelek számára, hogy új biztonsági riasztásokat határozzanak meg a környezetükből már gyűjtött adatok alapján.
   - Az Azure Security Center kiemelt biztonsági riasztásokat és incidenseket biztosít, így az ügyfelek egyszerűbben fedezhetik fel és oldják meg a lehetséges biztonsági problémákat. A fenyegetések felderítéséről szóló jelentés minden észlelt fenyegetés, hogy segítse az incidens-elhárítási csapatok a fenyegetések kivizsgálásában és elhárításában.
### <a name="azure-application-gateway"></a>Azure Application Gateway 
   Az architektúra csökkenti a biztonsági rések kockázatát egy webalkalmazás-tűzfallal rendelkező Azure Application Gateway használatával, és az OWASP szabálykészlet engedélyezve van. További képességek közé tartozik
   - Végpontok között A TLS.
   - Tiltsa le a TLS 1.0-s és 1.1-es és 1.1-es és 1.1-es
   - TLSv1.2 engedélyezése.
   - Webalkalmazás tűzfala (megelőzési mód).
   - Megelőzési mód owasp 3.0 szabálykészlettel.
   - Engedélyezze a diagnosztika naplózását.
   - Egyéni állapotpróbák.
   - Az Azure Security Center és az Azure Advisor további védelmet és értesítéseket biztosít. Az Azure Security Center is biztosít egy jó hírnév rendszer.
### <a name="logging-and-auditing"></a>Naplózás
Az Azure-szolgáltatások széles körben naplózzák a rendszer- és felhasználói tevékenységeket, valamint a rendszer állapotát:
   - Tevékenységnaplók: [A tevékenységnaplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) betekintést nyújtanak az előfizetésben lévő erőforrásokon végrehajtott műveletekbe. A tevékenységnaplók segíthetnek meghatározni a művelet kezdeményezőjének, az előfordulás idejét és állapotát.
   - Diagnosztikai naplók: [Diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) tartalmazzák az összes által kibocsátott naplók minden erőforrás által kibocsátott. Ezek a naplók közé tartoznak a Windows eseményrendszer-naplók, az Azure Storage-naplók, a Key Vault naplózási naplói, valamint az Application Gateway-hozzáférés és a tűzfalnaplók. Minden diagnosztikai naplók írása egy központi és titkosított Azure storage-fiók archiválási. Az adatmegőrzés felhasználó által konfigurálható, akár 730 napig, hogy megfeleljen a szervezet-specifikus megőrzési követelményeknek.
### <a name="azure-monitor-logs"></a>Azure Monitor-naplók
   Ezek a naplók az [Azure Monitor naplók](https://azure.microsoft.com/services/log-analytics/) feldolgozása, tárolása és irányítópult-jelentéskészítési konszolidált. Az adatgyűjtést követően az adatok külön táblákba vannak rendezve a Log Analytics-munkaterületeken belüli egyes adattípusokhoz, ami lehetővé teszi az összes adat együttes elemzését, függetlenül az eredeti forrástól. Továbbá az Azure Security Center integrálható az Azure Monitor naplóival, így az ügyfelek kusto-lekérdezések használatával hozzáférhetnek a biztonsági eseményadataikhoz, és kombinálhatják azokat más szolgáltatásokból származó adatokkal.

   A következő Azure [figyelési megoldások](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) szerepelnek ennek az architektúrának a részeként

   - [Active Directory-felmérés:](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment)Az Active Directory állapot-ellenőrzési megoldás rendszeres időközönként felméri a kiszolgálói környezetek kockázatát és állapotát, és rangsorolt listát ad a telepített kiszolgálói infrastruktúrára vonatkozó javaslatokról.
   - [Ügynök állapota:](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth)Az ügynök állapotmegoldás jelenti, hogy hány ügynökök vannak telepítve, és azok földrajzi eloszlása, valamint hány ügynökök, amelyek nem válaszolnak, és az ügynökök száma, amelyek működési adatokat.
   - [Tevékenységnapló-elemzés:](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)A Tevékenységnapló-elemzési megoldás az Azure-tevékenységnaplók elemzését segíti az összes Azure-előfizetésben egy ügyfél számára.
### <a name="azure-monitor"></a>Azure Monitor
   [Az Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/)segítségével a felhasználók nyomon követhetik a teljesítményt, fenntarthatja a biztonságot, és azonosíthatja a trendeket azáltal, hogy lehetővé teszik a szervezetek számára az adatok naplózását, a riasztások létrehozását és az adatok archiválását, beleértve az API-hívások nyomon követését az Azure-erőforrásokban.
### <a name="application-insights"></a>Application Insights 
   [Az Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) egy bővíthető alkalmazásteljesítmény-kezelési szolgáltatás több platformon a webfejlesztők számára. Az Application Insights észleli a teljesítményanomáliákat, és az ügyfelek használhatják az élő webalkalmazás figyelésére. Hatékony elemzési eszközöket tartalmaz, amelyek segítenek az ügyfeleknek diagnosztizálni a problémákat, és megérteni, hogy a felhasználók valójában mit csinálnak az alkalmazásukkal. Úgy tervezték, hogy segítsen az ügyfeleknek folyamatosan javítani a teljesítményt és a használhatóságot.

### <a name="azure-key-vault"></a>Azure Key Vault
   Hozzon létre egy tárolót a szervezet számára, amelyben kulcsokat tárolhat, és fenntarthatja az elszámoltathatóságot az alábbihoz hasonló operatív feladatokért

   - A Key Vaultban tárolt adatok   
   - Alkalmazásélesnek kulcs
   - Adattárolási hozzáférési kulcs
   - Kapcsolati sztring
   - Adattábla neve
   - Felhasználói hitelesítő adatok
   - A speciális hozzáférési házirendek igény szerint vannak konfigurálva
   - A Key Vault hozzáférési szabályzatai a kulcsokhoz és titkos kulcsokhoz szükséges minimális engedélyekkel vannak definiálva
   - A Key Vault összes kulcsa és titkos kulcsa lejárati dátummal rendelkezik
   - A Key Vault összes kulcsát hardveres biztonsági modul (HSM) [Kulcstípus = hardveres biztonsági modul (HSM) védett       
     2048 bites RSA-kulcs]
   - Minden felhasználó/identitás minimálisan szükséges engedélyekkel rendelkezik a szerepköralapú hozzáférés-vezérlés (RBAC) használatával
   - Az alkalmazások csak akkor osztoznak a Key Vaulton, ha megbíznak egymásban, és futásidőben hozzá kell férniük ugyanazokhoz a titkos kulcsokhoz.
   - A Key Vault diagnosztikai naplói legalább 365 napos megőrzési időtartammal vannak engedélyezve.
   - A kulcsok engedélyezett kriptográfiai műveletei a szükséges kulcsokra korlátozódnak

### <a name="vpn-and-expressroute"></a>VPN és ExpressRoute
   Egy biztonságos VPN-alagutat vagy [ExpressRoute-ot](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) úgy kellett konfigurálni, hogy biztonságosan létre kell hozni a kapcsolatot a PaaS webalkalmazás referenciaarchitektúra részeként üzembe helyezett erőforrásokkal. A VPN vagy expressroute megfelelő beállításával az ügyfelek hozzáadhatnak egy védelmi réteget az átvitel alatt álló adatokszámára.

   Egy biztonságos VPN-alagút megvalósítása az Azure-ral, egy virtuális privát kapcsolat egy helyszíni hálózat és egy Azure virtuális hálózat hozható létre. Ez a kapcsolat az interneten keresztül történik, és lehetővé teszi az ügyfelek számára, hogy biztonságosan "bújtassák" az információkat az ügyfél hálózata és az Azure közötti titkosított kapcsolaton belül. A helyek közötti VPN egy biztonságos, kiforrott technológia, amelyet a vállalatok évtizedek óta alkalmaznak bármilyen méretű vállalatnál. Az IPsec-alagút mód ebben a beállításban titkosítási mechanizmusként használatos.

   Mivel a VPN-alagúton belüli forgalom egy helyek közötti VPN-nel halad át az interneten, a Microsoft egy másik, még biztonságosabb csatlakozási lehetőséget kínál. Az Azure ExpressRoute egy dedikált WAN-kapcsolat az Azure és egy helyszíni hely vagy egy Exchange-tárhelyszolgáltató között. Mivel az ExpressRoute-kapcsolatok nem futnak át az interneten, ezek a kapcsolatok nagyobb megbízhatóságot, gyorsabb sebességet, kisebb késést és nagyobb biztonságot nyújtanak, mint az interneten keresztül isznak. Továbbá, mivel ez az ügyfél távközlési szolgáltatójának közvetlen kapcsolata, az adatok nem utaznak az interneten keresztül, és ezért nincsenek kitéve annak.

   A helyszíni hálózatot az Azure-ra kiterjesztő biztonságos hibrid hálózat megvalósításának gyakorlati [tanácsai elérhetők.](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)

#### <a name="implement-azure-active-directory-oidc"></a>Az Azure Active Directory oidc megvalósítása

1. A forráskódtár klónozásához használja ezt a Git parancsot

 ``` git
 git clone https://github.com/Azure-Samples/AAD-Security
   ```
## <a name="update-the-redirect-urls"></a>Az átirányítási URL-címek frissítése
1.  Keresse meg vissza az Azure Portalon. A bal oldali navigációs ablakban válassza ki az Azure Active Directory-szolgáltatást, majd válassza az Alkalmazásregisztrációk lehetőséget.
2.  Az eredményül lépő képernyőn válassza a WebApp-OpenIDConnect-DotNet-code-v2 alkalmazást.
3.  A Hitelesítés lapon o Az Irányított urik átirányítása szakaszban válassza a Web lehetőséget a kombinált listában, és adja hozzá a következő átirányítási URI-kat.
    https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.nethttps://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net/signin-oidc o A Speciális beállítások szakaszban állítsa be a kijelentkezés iurl-címéthttps://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net/signout-oidc
4.  A Márkajelzés lapon o Frissítse a kezdőlap URL-címét például https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.netaz alkalmazásszolgáltatás címére.
        o Mentse a konfigurációt.
5.  Ha az alkalmazás meghívja a webes API-t, győződjön meg arról, hogy alkalmazza a szükséges módosításokat a projekt appsettings.json, így meghívja a közzétett API-URL-t a localhost helyett.
A minta közzététele
    1.  Az App Service Áttekintés lapján töltse le a közzétételi profilt a Közzétételi profil hivatkozásának letöltésével és mentésével. Más üzembe helyezési mechanizmusok, például a forrás-ellenőrzés, is használható.
    2.  Váltson a Visual Studióra, és nyissa meg a WebApp-OpenIDConnect-DotNet-code-v2 projektet. Kattintson a jobb gombbal a projektre a Megoldáskezelőben, és válassza a Közzététel parancsot. Kattintson az alsó sáv Profil importálása gombjára, és importálja a korábban letöltött közzétételi profilt.
    3.  Kattintson a Beállítás gombra, és a Kapcsolat lapon frissítse a cél URL-címét úgy, hogy az például https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.netegy https legyen a kezdőlap URL-címében. Kattintson a Tovább gombra.
    4.  A Beállítások lapon győződjön meg arról, hogy a szervezeti hitelesítés engedélyezése nincs bejelölve. Kattintson a Mentés gombra. Kattintson a Közzététel gombra a főképernyőn.
    5.  A Visual Studio közzéteszi a projektet, és automatikusan megnyit egy böngészőt a projekt URL-címére. Ha a projekt alapértelmezett weblapja látható, a kiadvány sikeres volt.
#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>Többtényezős hitelesítés megvalósítása az Azure Active Directoryhoz
   A rendszergazdáknak gondoskodniuk kell arról, hogy a portálon lévő előfizetési fiókok védettek legyenek. Az előfizetés ki van téve a támadásoknak, mert kezeli a létrehozott erőforrásokat. Az előfizetés védelme érdekében engedélyezze a többtényezős hitelesítést az **előfizetés Azure Active Directory** lapján.

   Az Azure AD egy bizonyos feltételeknek megfelelő felhasználóra vagy felhasználói csoportokra alkalmazott szabályzatok alapján működik.
Az Azure létrehoz egy alapértelmezett szabályzatot, amely meghatározza, hogy a rendszergazdáknak kétfaktoros hitelesítésre van szükségük a portálra való bejelentkezéshez.
A szabályzat engedélyezése után előfordulhat, hogy a rendszer kéri, hogy jelentkezzen ki, és jelentkezzen be újra az Azure Portalra.

Többszintű bejelentkezés engedélyezése a rendszergazdai bejelentkezések számára

   1. Nyissa meg az **Azure Active Directory** lapot az Azure Portalon
   2. A biztonsági kategória alatt válassza a feltételes hozzáférést. Látod ezt a képernyőt

       ![Feltételes hozzáférés – házirendek](./media/secure-aad-app/ad-mfa-conditional-add.png)

Ha nem tud új házirendet létrehozni

   1. Nyissa meg az **MFA** lapot.
   2. Válasszon ki egy Ingyenes Azure-prémium verzióra mutató **hivatkozást** az ingyenes próbaverzióra való előfizetéshez.

   ![Ingyenes Azure AD Premium-próbaverzió](./media/secure-aad-app/ad-trial-premium.png)

Visszatérés a feltételes hozzáférési képernyőre.

   1. Válassza az új házirend lapot.
   2. Adja meg a házirend nevét.
   3. Jelölje ki azokat a felhasználókat vagy csoportokat, amelyeknek engedélyezni szeretné az MFA-t.
   4. Az **Access-vezérlők csoportban**jelölje be a **Grant** fül, majd a **Többtényezős hitelesítés megkövetelése** (és egyéb beállítások szükség esetén) lehetőséget.

   ![MFA megkövetelése](./media/secure-aad-app/ad-mfa-conditional-add.png)

   A házirendet a képernyő tetején lévő jelölőnégyzet bejelölésével engedélyezheti, vagy a **Feltételes hozzáférés** lapon. Ha a házirend engedélyezve van, a felhasználóknak többfunkciós kell lennia a portálra való bejelentkezéshez.

   Van egy alaptervi szabályzat, amely az összes Azure-rendszergazdák többszintű szolgáltatását igényli. Engedélyezheti azt azonnal a portálon. A házirend engedélyezése érvénytelenítheti az aktuális munkamenetet, és kényszerítheti, hogy újra jelentkezzen be.

   Ha az alapházirend nincs engedélyezve
   1.   Válassza **az MFA megkövetelése a rendszergazdákszámára**lehetőséget.
   2.   Válassza a **Házirend használata azonnal**lehetőséget.

   ![Azonnal válassza a Házirend használata lehetőséget.](./media/secure-aad-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>Alkalmazások és erőforrások figyelése az Azure Sentinel segítségével

   Az alkalmazás növekedésével nehéz lesz összesíteni az erőforrásokból kapott összes biztonsági jelet és metrikát, és azokat műveletorientált módon hasznossá tenni.

   Az Azure Sentinel célja az adatok gyűjtése, a lehetséges fenyegetések típusának észlelése és a biztonsági incidensek láthatósága.
Amíg manuális beavatkozásra vár, az Azure Sentinel előre megírt forgatókönyvekre támaszkodhat a riasztások és az incidenskezelési folyamatok indításához.

   A mintaalkalmazás több erőforrásból áll, amelyeket az Azure Sentinel figyelhet.
Az Azure Sentinel beállításához először létre kell hoznia egy Log Analytics-munkaterületet, amely tárolja a különböző erőforrásokból gyűjtött összes adatot.

A munkaterület létrehozása

   1. Az Azure Portal keresőmezőjében keresse meg a **Log Analytics**kifejezést. Válassza **a Log Analytics-munkaterületek lehetőséget.**

   ![Log Analytics-munkaterületek keresése](./media/secure-aad-app/sentinel-log-analytics.png)

   *Log Analytics-munkaterületek keresése*

   2. A következő lapon válassza a **Hozzáadás** lehetőséget, majd adja meg a munkaterület nevét, erőforráscsoportját és helyét.
   ![A Log Analytics-munkaterület létrehozása](./media/secure-aad-app/sentinel-log-analytics-create.png)

   *A Log Analytics-munkaterület létrehozása*

   3. A keresőmező segítségével keresse meg az **Azure Sentinel**.

   ![Az Azure Sentinel keresése](./media/secure-aad-app/sentinel-add.png)

   *Az Azure Sentinel keresése*

   4. Válassza **a Hozzáadás** lehetőséget, majd válassza ki a korábban létrehozott Log Analytics-munkaterületet.

   ![Log Analytics-munkaterület hozzáadása](./media/secure-aad-app/sentinel-workspace-add.png)

   *Log Analytics-munkaterület hozzáadása*

   5. Az **Azure Sentinel – Adatösszekötők** lap **Konfiguráció csoportban**válassza az **Adatösszekötők**lehetőséget. Megjelenik egy azure-szolgáltatások tömbje, amely az Azure Sentinel ben elemzésre hivatkozhat a Log Analytics-tárházhoz.

   ![Log Analytics-adatösszekötők](./media/secure-aad-app/sentinel-connectors.png)

      *Adatösszekötő hozzáadása az Azure Sentinelhez*

   Az alkalmazásátjáró csatlakoztatásához például tegye a következőket:

   1. Nyissa meg az Azure Application Gateway példány panel.
   2. A **Monitorozás** területen kattintson a **Diagnosztikai beállítások** elemre.
   3. Válassza **a Diagnosztikai beállítás hozzáadása lehetőséget.**

   ![Alkalmazásátjáró diagnosztikájának hozzáadása](./media/secure-aad-app/sentinel-gateway-connector.png)
         
   *Alkalmazásátjáró diagnosztikájának hozzáadása*

   4. A **Diagnosztikai beállítások** lapon válassza ki a létrehozott Log Analytics-munkaterületet, majd válassza ki az azure Sentinelnek gyűjtésre és elküldésre kívánt összes metrikát. Kattintson a **Mentés** gombra.

   ![Az Azure Sentinel-összekötő beállításai](./media/secure-aad-app/sentinel-connector-settings.png)



## <a name="cost-considerations"></a>Költségekkel kapcsolatos szempontok
   Ha még nem rendelkezik Azure-fiókkal, létrehozhat egy ingyeneset. Az [ingyenes fiókoldalra](https://azure.microsoft.com/free/) feladhatja a kezdéshez, megtudhatja, hogy mit tehet egy ingyenes Azure-fiókkal, és megtudhatja, hogy mely termékek 12 hónapig ingyenesek.

   Az erőforrások üzembe helyezéséhez a mintaalkalmazásban a biztonsági funkciókkal, meg kell fizetnie néhány prémium funkciók. Mivel az alkalmazás méretei és az Azure által kínált ingyenes szintek és próbaverziók frissítése az alkalmazás követelményeinek teljesítése érdekében, a költségek növekedhetnek. Az Azure [díjkalkulátor](https://azure.microsoft.com/pricing/calculator/) segítségével megbecsülheti a költségeket.

## <a name="next-steps"></a>További lépések
   Az alábbi cikkek segítségével biztonságos alkalmazásokat tervezhet, fejleszthet és helyezhet üzembe.

- [Tervezés](secure-design.md)
- [Fejlesztés](secure-develop.md)
- [Telepítés](secure-deploy.md)
