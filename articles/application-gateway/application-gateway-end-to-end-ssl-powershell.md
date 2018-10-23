---
title: Végpontok közötti SSL konfigurálása az Azure Application Gatewayen
description: Ez a cikk bemutatja, hogyan végpontok közötti SSL konfigurálása az Azure Application Gatewayen a PowerShell használatával
services: application-gateway
documentationcenter: na
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/23/2018
ms.author: victorh
ms.openlocfilehash: f5b63c7448a502d681bbafb0ac80237a8e2c63c6
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49650094"
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-powershell"></a>Végpontok közötti SSL konfigurálása az Application Gateway a PowerShell használatával

## <a name="overview"></a>Áttekintés

Az Azure Application Gateway támogatja a forgalom végpontok közötti titkosítást. Az Application Gateway leállítja az SSL-kapcsolatot az application gatewayben. Az átjáró ezután alkalmazza az útválasztási szabályokat a forgalomra, a csomag reencrypts, és továbbítja azt a megfelelő háttér-kiszolgáló a megadott útválasztási szabályok alapján. A webkiszolgáló esetleges válasza ugyanilyen módon jut el a végfelhasználóhoz.

Az Application Gateway támogatja az egyéni SSL-beállítások meghatározása. Ezenkívül támogatja a következő verziójú címprotokollokhoz letiltása: **TLSv1.0**, **TLSv1.1**, és **TLSv1.2**, valamint melyik titkosító csomagok használatához és preferencia sorrendje . SSL konfigurálható beállításokkal kapcsolatos további tudnivalókért tekintse meg a [SSL-házirend áttekintése](application-gateway-SSL-policy-overview.md).

> [!NOTE]
> Az SSL 2.0 és az SSL 3.0 alapértelmezés szerint le vannak tiltva, és nem lehet engedélyezni. Azok a nem biztonságos tekinti, és nem használható az Application Gateway szolgáltatással.

![a forgatókönyv kép][scenario]

## <a name="scenario"></a>Forgatókönyv

Ebben a forgatókönyvben megtudhatja hogyan hozhat létre átjáróalkalmazást teljes körű SSL a PowerShell használatával.

Ebben a forgatókönyvben lesz:

* Hozzon létre egy erőforráscsoportot **appgw-rg**.
* Hozzon létre egy virtuális hálózatot nevű **appgwvnet** -címtere a **10.0.0.0/16**.
* Hozzon létre két alhálózattal nevű **appgwsubnet** és **appsubnet**.
* Hozzon létre egy kis application gateway támogató végpontok közötti SSL-titkosítást, a korlátok az SSL protokoll verziója és a titkosító csomagok.

## <a name="before-you-begin"></a>Előkészületek

Végpontok közötti SSL konfigurálása az application gateway szolgáltatással, egy tanúsítvány szükséges az átjáró és a háttér-kiszolgálók számára tanúsítványokra szükség. Az átjáró tanúsítványa titkosítása és visszafejtése az SSL-en keresztül küldött szolgál. Az átjáró tanúsítványa kell lennie a személyes információcsere (PFX) formátumban. Ez a fájlformátum exportálja a titkos kulcsot, a titkosítási és visszafejtési forgalmat az application gateway által igényelt teszi lehetővé.

A végpontok közötti SSL-titkosítás a háttéralkalmazás az Alkalmazásátjáró engedélyezési listán kell lennie. A háttér-kiszolgálók a nyilvános tanúsítvány feltöltése az application gateway kell. A tanúsítvány hozzáadása biztosítja, hogy az application gateway csak kommunikál a háttér-ismert példányok. Ez további védi a végpontok közötti kommunikációt.

A konfigurációs folyamat a következő szakaszokban ismertetett.

## <a name="create-the-resource-group"></a>Az erőforráscsoport létrehozása

Ez a szakasz végigvezeti egy erőforráscsoport, amely tartalmazza az application gateway létrehozása.


   1. Jelentkezzen be Azure-fiókjába.

   ```powershell
   Connect-AzureRmAccount
   ```


   2. Válassza ki a ebben a forgatókönyvben használni kívánt előfizetést.

   ```powershell
   Select-AzureRmsubscription -SubscriptionName "<Subscription name>"
   ```


   3. Hozzon létre egy erőforráscsoportot. (Hagyja ki ezt a lépést, ha egy meglévő erőforráscsoportot használ.)

   ```powershell
   New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
   ```

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Virtuális hálózat és alhálózat létrehozása az Application Gateway számára

Az alábbi példa létrehoz egy virtuális hálózatot és két alhálózatra. Egy alhálózatot az application gateway tárolásához. A másik alhálózatot a háttérrendszerek, amelyek a webalkalmazás üzemeltetéséhez.


   1. Rendeljen hozzá egy használható az application gateway az alhálózat-címtartományt.

   ```powershell
   $gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24
   ```

   > [!NOTE]
   > Az application Gateway konfigurált alhálózatok megfelelően kell méretezni. Egy application gateway konfigurálható legfeljebb 10-példányokhoz. Minden példány egy IP-címet az alhálózatról vesz igénybe. Túl kicsi alhálózat kedvezőtlen hatással lehet, horizontális felskálázás egy application gateway.
   > 
   > 

   2. A háttér-címkészletet kell használni egy olyan címtartományt rendel.

   ```powershell
   $nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24
   ```

   3. Hozzon létre egy virtuális hálózatot az előző lépések során meghatározott alhálózatot.

   ```powershell
   $vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
   ```

   4. A virtuális hálózat és alhálózat erőforrást a következő lépések használhatók lekéréséhez.

   ```powershell
   $vnet = Get-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
   $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
   $nicSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet
   ```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Nyilvános IP-cím létrehozása az előtérbeli konfigurációhoz

Hozzon létre egy nyilvános IP-erőforráshoz lehet használni az application gateway számára. A nyilvános IP-cím szerepel a következő lépések egyikét.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "West US" -AllocationMethod Dynamic
```

> [!IMPORTANT]
> Az Application Gateway nem támogatja egy meghatározott tartománycímkéjét létrehozott nyilvános IP-cím használatát. Csak egy nyilvános IP-címet egy dinamikusan létrehozott tartománycímkéjét használata támogatott. Ha szüksége van az application gateway DNS egy rövid nevet, javasoljuk, egy CNAME-rekordot használhat aliasként.

## <a name="create-an-application-gateway-configuration-object"></a>Hozzon létre egy Application Gateway konfigurációs objektumot

Minden konfigurációs elemet az application gateway létrehozása előtt vannak beállítva. Az alábbi lépések létrehozzák az Application Gateway erőforráshoz szükséges konfigurációs elemeket.

1. Hozzon létre egy application gateway IP-konfigurációt. Ezzel a beállítással konfigurálható, amely az alhálózatok az application gateway használja. Application gateway elindul, amikor egy IP-címet a konfigurált alhálózatból buildkonfigurációtól és útvonalak hálózati forgalmat a háttérbeli IP-készlet IP-címek. Ne feledje, hogy minden példány egy IP-címet vesz fel.

   ```powershell
   $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet
   ```


2. Hozzon létre egy előtérbeli IP-konfigurációhoz. Ez a beállítás egy nyilvános vagy magánhálózati IP-címet az előtér az application Gateway rendeli hozzá. A következő lépés a nyilvános IP-címet az előző lépésben társítja az előtérbeli IP-konfigurációhoz.

   ```powershell
   $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip
   ```

3. A háttérbeli IP-címkészletet konfigurálja a háttér-webkiszolgálók IP-címét. Ezek az IP-címek fogadják majd az előtérbeli IP-cím végpontból érkező hálózati forgalmat. Cserélje le a példában szereplő IP-címeket a saját alkalmazása IP cím végpontok.

   ```powershell
   $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3
   ```

   > [!NOTE]
   > Egy teljesen minősített tartománynevét (FQDN) is egy érvényes értéket használja az IP-cím helyett a háttér-kiszolgálókhoz. Használatával engedélyezheti a **- BackendFqdns** váltani. 


4. Konfigurálja az előtérbeli IP-portot a nyilvános IP-cím végponthoz. A port a port, amelyet a végfelhasználók csatlakozni.

   ```powershell
   $fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443
   ```

5. Konfigurálja a tanúsítványt az application gateway számára. Ezt a tanúsítványt a forgalmat az application gateway reencrypt és visszafejtésére szolgál.

   ```powershell
   $passwd = ConvertTo-SecureString  <certificate file password> -AsPlainText -Force 
   $cert = New-AzureRmApplicationGatewaySSLCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password $passwd 
   ```

   > [!NOTE]
   > Ez a példa az SSL-kapcsolathoz használt tanúsítványt konfigurálja. A tanúsítvány .pfx formátumúnak kell, és a jelszónak kell lennie 4 és 12 karakter.

6. Hozzon létre a HTTP-figyelő az application gateway számára. Rendelje hozzá az előtérbeli IP-konfigurációt, egy portszám és egy SSL-tanúsítvány használatára.

   ```powershell
   $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert
   ```

7. Töltse fel a tanúsítványt az SSL-kompatibilis háttérkészlet-erőforrásokat kell használni.

   > [!NOTE]
   > Az alapértelmezett mintavétel lekérdezi a nyilvános kulcs a a *alapértelmezett* SSL-kötés van a háttérbeli IP-címet, és összehasonlítja a nyilvános kulcs értékét, kap, a nyilvános kulcs értékét Ön adja meg itt. 
   
   > Állomásfejléc és kiszolgálónév jelzése (SNI) a háttérben használja, ha a lekért nyilvános kulcs nem lehet a kívánt helyet, hogy melyik adatforgalmi folyamatokat. Ha kétségei vannak, látogasson el a https://127.0.0.1/ győződjön meg arról, melyik tanúsítványt használják a háttér-kiszolgálókon a *alapértelmezett* SSL-kötés létrehozásához. A kérelemből a nyilvános kulcsot használja az ebben a szakaszban. Ha a HTTPS-kötések használunk állomásfejléc és SNI, és nem jelenik meg egy válasz és a tanúsítvány manuális böngésző kérést a https://127.0.0.1/ a háttér-kiszolgálókon be kell állítania egy alapértelmezett SSL-kötés van a számukra. Ha ezt nem teszi meg, a sikertelen mintavételek és a háttér nem szerepel az engedélyezési listán.

   ```powershell
   $authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile C:\users\gwallace\Desktop\cert.cer
   ```

   > [!NOTE]
   > Ebben a lépésben megadott tanúsítvány nyilvános kulcsát a .pfx tanúsítvány megtalálható a háttérrendszer a kell lennie. Exportálja a tanúsítványt (főtanúsítványt nem), a jogcím, tanúsítás és mintafelismerési (CER) formátumban a háttér-kiszolgálóra telepíthető, és használhatja ezt a lépést. Ezen lépés listáinak a háttéralkalmazáshoz a az application gateway.

   Ha az Application Gateway v2 szintű Termékváltozatot használja, hozzon létre egy megbízható főtanúsítvány-hitelesítési tanúsítvány helyett. További információkért lásd: [az Application Gateway teljes körű SSL áttekintése](ssl-overview.md#end-to-end-ssl-with-the-v2-sku):

   ```powershell
   $trustedRootCert01 = New-AzureRmApplicationGatewayTrustedRootCertificate -Name "test1" -CertificateFile  <path to root cert file>
   ```

8. A application gateway háttér HTTP-beállítások konfigurálása. Rendelje hozzá a a HTTP-beállítások az előző lépésben feltöltött tanúsítványt.

   ```powershell
   $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert
   ```

   Az Application Gateway v2 szintű Termékváltozatot használja a következő parancsot:

   ```powershell
   $poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name “setting01” -Port 443 -Protocol Https -CookieBasedAffinity Disabled -TrustedRootCertificate $trustedRootCert01 -HostName "test1"
   ```

9. Hozzon létre egy terheléselosztó útválasztási szabályt, amely a terheléselosztó viselkedését konfigurálja. Ebben a példában egy egyszerű ciklikus multiplexelési szabályt jön létre.

   ```powershell
   $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   ```

10. Konfigurálja az Application Gateway példányméretét. Az elérhető méretek a következők **Standard\_kis**, **Standard\_Közepes**, és **Standard\_nagy**.  A kapacitást, az elérhető értékek a következők **1** keresztül **10**.

    ```powershell
    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
    ```

    > [!NOTE]
    > Tesztelési célokra példányszámnak 1 választható ki. Fontos tudni, hogy bármely példányok száma alapján két példánnyal az SLA nem vonatkozik, és ezért nem ajánlott. Kis átjárók vannak a fejlesztési-tesztelési és nem termelési célra használható.

11. Az application gateway esetében használható SSL-szabályzat konfigurálása. Az Application Gateway támogatja a minimális verziója az SSL-protokollverziók megadására.

   A következő értékek közül, amelyek az protokollverziók listája:

    - **TLSV1_0**
    - **TLSV1_1**
    - **TLSV1_2**
    
   Az alábbi példa állítja a minimális protokoll verziója **TLSv1_2** , és lehetővé teszi **TLS\_ECDHE\_ECDSA\_WITH\_AES\_128\_GCM\_SHA256**, **TLS\_ECDHE\_ECDSA\_WITH\_AES\_256\_GCM\_SHA384**, és **TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256** csak.

   ```powershell
   $SSLPolicy = New-AzureRmApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256"
   ```

## <a name="create-the-application-gateway"></a>Application Gateway létrehozása

Az application gateway az előző lépések segítségével hozzon létre. Az átjáró létrehozása az, amely a futtatása hosszú időt vesz igénybe.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -AuthenticationCertificates $authcert -Verbose
```

## <a name="limit-ssl-protocol-versions-on-an-existing-application-gateway"></a>A meglévő application gateway SSL-protokollverziók korlátozása

Az előző lépések végrehajtásának egy alkalmazás létrehozásának teljes körű SSL-lel, és bizonyos SSL-protokollverziók letiltása. A következő példa letiltja az egyes SSL-szabályzatokat egy meglévő alkalmazásátjárón.

   1. Az Alkalmazásátjáró frissítése lekéréséhez.

   ```powershell
   $gw = Get-AzureRmApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```

   2. Egy SSL-szabályzat meghatározása. A következő példában **TLSv1.0** és **TLSv1.1** le van tiltva, és a titkosító csomagok **TLS\_ECDHE\_ECDSA\_WITH\_ AES\_128\_GCM\_SHA256**, **TLS\_ECDHE\_ECDSA\_WITH\_AES\_256\_GCM\_SHA384**, és **TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256** csak kiépítettektől engedélyezett.

   ```powershell
   Set-AzureRmApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -PolicyType Custom -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -ApplicationGateway $gw

   ```

   3. Végül frissítse az átjárót. Vegye figyelembe, hogy az utolsó lépés egy hosszan futó feladatot. Amikor kész van, az application gateway teljes körű SSL van konfigurálva.

   ```powershell
   $gw | Set-AzureRmApplicationGateway
   ```

## <a name="get-an-application-gateway-dns-name"></a>Egy application gateway DNS-nevének lekérése

Az átjáró létrehozása után a következő lépés a kommunikációra szolgáló előtér konfigurálása. Az Application Gateway egy dinamikusan hozzárendelt DNS-név szükséges, ha a nyilvános IP-cím, která není rövid. Hogy a végfelhasználók elérjék az application gateway, használhatja, az application Gateway nyilvános végpontjára mutató CNAME rekord. További információkért lásd: [egy egyéni tartománynév konfigurálása az Azure-ban](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Aliasként beállítandó részleteinek lekérése az application gateway és a kapcsolódó IP/DNS-név használatával a **PublicIPAddress** az application gatewayhez csatolt elem. Az application gateway DNS-név használatával hozzon létre egy CNAME rekordot, a két webalkalmazást, a DNS-névhez. Azt nem-bejegyzések használata ajánlott, mert a virtuális IP-cím is megváltozik az indítsa újra az application Gateway.

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

## <a name="next-steps"></a>További lépések

A webalkalmazási tűzfal Application Gatewayen keresztül a webalkalmazások biztonságának megerősítése kapcsolatos további információkért lásd: a [webalkalmazás-tűzfal áttekintése](application-gateway-webapplicationfirewall-overview.md).

[scenario]: ./media/application-gateway-end-to-end-SSL-powershell/scenario.png
