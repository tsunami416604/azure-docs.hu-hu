---
title: Végpontok között a TLS konfigurálása az Azure Application Gateway alkalmazással
description: Ez a cikk azt ismerteti, hogyan konfigurálhatók a végpontok között TLS-ek az Azure Application Gateway használatával a PowerShell használatával
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/8/2019
ms.author: victorh
ms.openlocfilehash: 481cbda1d35f7d630dabca00fd01677f542447c2
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312498"
---
# <a name="configure-end-to-end-tls-by-using-application-gateway-with-powershell"></a>Végpontok között a TLS konfigurálása az Application Gateway és a PowerShell használatával

## <a name="overview"></a>Áttekintés

Az Azure Application Gateway támogatja a forgalom végpontok közötti titkosítását. Az Application Gateway leállítja a TLS/SSL-kapcsolatot az alkalmazásátjárón. Az átjáró ezután alkalmazza az útválasztási szabályokat a forgalomra, újra titkosítja a csomagot, és továbbítja a csomagot a megfelelő háttérkiszolgálónak a megadott útválasztási szabályok alapján. A webkiszolgáló esetleges válasza ugyanilyen módon jut el a végfelhasználóhoz.

Az Application Gateway támogatja az egyéni TLS-beállítások meghatározását. Támogatja a következő protokollverziók letiltását is: **TLSv1.0**, **TLSv1.1**és **TLSv1.2**, valamint meghatározza, hogy mely titkosítási csomagokat kell használni, és milyen sorrendben. A konfigurálható TLS-beállításokról a [TLS-házirend áttekintése című témakörben olvashat bővebben.](application-gateway-SSL-policy-overview.md)

> [!NOTE]
> Az SSL 2.0 és az SSL 3.0 alapértelmezés szerint le van tiltva, ezért nem engedélyezhető. Ezek nem biztonságosnak minősülnek, és nem használhatók az Application Gateway-rel.

![forgatókönyv képe][scenario]

## <a name="scenario"></a>Forgatókönyv

Ebben a forgatókönyvben megtudhatja, hogyan hozhat létre egy alkalmazásátjáró ta-végpontok között TLS powershell használatával.

Ez a forgatókönyv:

* Hozzon létre egy **appgw-rg**nevű erőforráscsoportot.
* Hozzon létre egy **appgwvnet** nevű virtuális hálózatot **10.0.0.0/16**címtérrel.
* Hozzon létre két **alhálózatot, az appgwsubnet** és **az appsubnet nevű.**
* Hozzon létre egy kis alkalmazásátjárót, amely támogatja a tls protokollverziókat és titkosítási csomagokat.

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A végpontok között tls alkalmazásátjáróval történő konfigurálásához tanúsítvány szükséges az átjáróhoz, és a háttérkiszolgálók tanúsítványai szükségesek. Az átjárótanúsítvány a TLS protokoll specifikációja szerint szimmetrikus kulcs származtatására szolgál. A szimmetrikus kulcsot ezután az átjárónak küldött forgalom titkosításával és visszafejtéseként használják. Az átjárótanúsítványnak személyes adatcsere (PFX) formátumban kell lennie. Ez a fájlformátum lehetővé teszi az alkalmazásátjáró által a forgalom titkosításához és visszafejtéséhez szükséges személyes kulcs exportálását.

Végpontok közötti TLS-titkosítás esetén a háttérrendszert az alkalmazásátjárónak kifejezetten engedélyeznie kell. Töltse fel a háttérkiszolgálók nyilvános tanúsítványát az alkalmazásátjáróba. A tanúsítvány hozzáadása biztosítja, hogy az alkalmazásátjáró csak ismert háttérpéldányokkal kommunikáljon. Ez tovább biztosítja a végpontok közötti kommunikációt.

A konfigurációs folyamat leírása a következő szakaszokban található.

## <a name="create-the-resource-group"></a>Az erőforráscsoport létrehozása

Ez a szakasz végigvezeti az alkalmazásátjárót tartalmazó erőforráscsoport létrehozásán.

1. Jelentkezzen be Azure-fiókjába.

   ```powershell
   Connect-AzAccount
   ```

2. Válassza ki az ehhez a forgatókönyvhöz használandó előfizetést.

   ```powershell
   Select-Azsubscription -SubscriptionName "<Subscription name>"
   ```

3. Hozzon létre egy erőforráscsoportot. (Hagyja ki ezt a lépést, ha egy meglévő erőforráscsoportot használ.)

   ```powershell
   New-AzResourceGroup -Name appgw-rg -Location "West US"
   ```

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Virtuális hálózat és alhálózat létrehozása az Application Gateway számára

A következő példa létrehoz egy virtuális hálózatot és két alhálózatot. Az alkalmazásátjáró tárolására egy alhálózat használatos. A másik alhálózat a webalkalmazást tartalmazó háttérrendszerekhez használatos.

1. Rendeljen hozzá egy címtartományt az alkalmazásátjáróhoz használandó alhálózathoz.

   ```powershell
   $gwSubnet = New-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24
   ```

   > [!NOTE]
   > Az alkalmazásátjáróhoz konfigurált alhálózatokat megfelelően kell méretezni. Az alkalmazásátjáró legfeljebb 10 példányra konfigurálható. Minden példány egy IP-címet vesz fel az alhálózatból. Az alhálózat túl kicsi, hátrányosan befolyásolhatja az alkalmazásátjáró horizontális felskálázását.
   >

2. Rendeljen hozzá egy címtartományt, amelyet a háttércímkészlethez kell használni.

   ```powershell
   $nicSubnet = New-AzVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24
   ```

3. Hozzon létre egy virtuális hálózatot az előző lépésekben definiált alhálózatokkal.

   ```powershell
   $vnet = New-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
   ```

4. A következő lépésekben használandó virtuális hálózati és alhálózati erőforrások beolvasása.

   ```powershell
   $vnet = Get-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
   $gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
   $nicSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet
   ```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Nyilvános IP-cím létrehozása az előtérbeli konfigurációhoz

Hozzon létre egy nyilvános IP-erőforrást az alkalmazásátjáróhoz használva. Ez a nyilvános IP-cím a következő lépések egyikében használatos.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "West US" -AllocationMethod Dynamic
```

> [!IMPORTANT]
> Az Application Gateway nem támogatja a megadott tartományi címkével létrehozott nyilvános IP-cím használatát. Csak egy dinamikusan létrehozott tartományi címkével rendelkező nyilvános IP-cím támogatott. Ha az alkalmazásátjáróhoz rövid DNS-név szükséges, javasoljuk, hogy aliasként használjon CNAME rekordot.

## <a name="create-an-application-gateway-configuration-object"></a>Hozzon létre egy Application Gateway konfigurációs objektumot

Az alkalmazásátjáró létrehozása előtt minden konfigurációs elem be van állítva. Az alábbi lépések létrehozzák az Application Gateway erőforráshoz szükséges konfigurációs elemeket.

1. Hozzon létre egy alkalmazásátjáró IP-konfigurációját. Ez a beállítás azt határozza meg, hogy az alkalmazásátjáró melyik alhálózatot használja. Amikor az alkalmazásátjáró elindul, felveszi az IP-címet a konfigurált alhálózatról, és a hálózati forgalmat a háttér-IP-készlet IP-címére irányítja. Ne feledje, hogy minden példány egy IP-címet vesz fel.

   ```powershell
   $gipconfig = New-AzApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet
   ```

2. Előtér-IP-konfiguráció létrehozása. Ez a beállítás egy privát vagy nyilvános IP-címet rendel az alkalmazásátjáró előtéréhez. A következő lépés az előző lépésben lévő nyilvános IP-címet társítja az előtér-IP-konfigurációhoz.

   ```powershell
   $fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip
   ```

3. Konfigurálja a háttér-IP-címkészletet a háttérkiszolgálók IP-címével. Ezek az IP-címek fogadják majd az előtérbeli IP-cím végpontból érkező hálózati forgalmat. Cserélje le a mintában lévő IP-címeket a saját alkalmazás IP-címvégpontjaira.

   ```powershell
   $pool = New-AzApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3
   ```

   > [!NOTE]
   > A teljesen minősített tartománynév (FQDN) szintén érvényes érték a háttérkiszolgálók IP-címe helyett. A **-BackendFqdns** kapcsoló használatával engedélyezheti. 

4. Konfigurálja a nyilvános IP-végpont előtér-IP-portját. Ez a port az a port, amelyhez a végfelhasználók csatlakoznak.

   ```powershell
   $fp = New-AzApplicationGatewayFrontendPort -Name 'port01'  -Port 443
   ```

5. Konfigurálja az alkalmazásátjáró tanúsítványát. Ez a tanúsítvány az alkalmazásátjáró forgalmának visszafejtésére és újrakódolására szolgál.

   ```powershell
   $passwd = ConvertTo-SecureString  <certificate file password> -AsPlainText -Force 
   $cert = New-AzApplicationGatewaySSLCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password $passwd 
   ```

   > [!NOTE]
   > Ez a minta konfigurálja a TLS-kapcsolathoz használt tanúsítványt. A tanúsítványnak .pfx formátumúnak kell lennie, és a jelszónak 4 és 12 karakter között kell lennie.

6. Hozza létre a HTTP-figyelőt az alkalmazásátjáróhoz. Rendelje hozzá az előtér-IP-konfigurációt, a portot és a TLS/SSL tanúsítványt.

   ```powershell
   $listener = New-AzApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert
   ```

7. Töltse fel a TLS-kompatibilis háttérkészlet-erőforrásokon használandó tanúsítványt.

   > [!NOTE]
   > Az alapértelmezett mintavétel lenyeri a nyilvános kulcsot a háttérrendszer IP-címének *alapértelmezett* TLS-kötésétől, és összehasonlítja az itt megadott nyilvános kulcs értékével. 
   > 
   > Ha a háttérrendszeren állomásfejléceket és kiszolgálónév-jelzést (SNI) használ, előfordulhat, hogy a beolvasott nyilvános kulcs nem az a tervezett hely, ahamelybe a forgalom áramlik. Ha kétségei vannak, https://127.0.0.1/ látogasson el a háttérkiszolgálókra, és ellenőrizze, hogy melyik tanúsítványt használja az *alapértelmezett* TLS-kötéshez. Ebben a szakaszban használja a kérelem nyilvános kulcsát. Ha állomásfejléceket és SNI-t használ HTTPS-kötéseken, és nem kap választ https://127.0.0.1/ és tanúsítványt a manuális böngészőkéréstől a háttérkiszolgálókon, be kell állítania egy alapértelmezett TLS-kötést. Ha ezt nem teszi meg, a mintavételek sikertelenek lesznek, és a háttérlista nem szerepel a listán.

   ```powershell
   $authcert = New-AzApplicationGatewayAuthenticationCertificate -Name 'allowlistcert1' -CertificateFile C:\cert.cer
   ```

   > [!NOTE]
   > Az előző lépésben megadott tanúsítványnak a háttérterületen található .pfx tanúsítvány nyilvános kulcsának kell lennie. Exportálja a háttérkiszolgálóra telepített tanúsítványt (nem a főtanúsítványt) jogcím, bizonyíték és érvelés (CER) formátumban, és használja ebben a lépésben. Ez a lépés az alkalmazásátjáró háttérlistáját listázza.

   Ha az Application Gateway v2 Termékváltozatot használja, majd hozzon létre egy megbízható főtanúsítványt hitelesítési tanúsítvány helyett. További információ: [A végpontok között a TLS áttekintése az Application Gateway alkalmazásátjáróval című témakörben:](ssl-overview.md#end-to-end-tls-with-the-v2-sku)

   ```powershell
   $trustedRootCert01 = New-AzApplicationGatewayTrustedRootCertificate -Name "test1" -CertificateFile  <path to root cert file>
   ```

8. Konfigurálja az alkalmazásátjáró háttérrendszerének HTTP-beállításait. Rendelje hozzá az előző lépésben feltöltött tanúsítványt a HTTP-beállításokhoz.

   ```powershell
   $poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert
   ```

   Az Application Gateway v2 Termékváltozathoz használja a következő parancsot:

   ```powershell
   $poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name “setting01” -Port 443 -Protocol Https -CookieBasedAffinity Disabled -TrustedRootCertificate $trustedRootCert01 -HostName "test1"
   ```

9. Hozzon létre egy terheléselosztó útválasztási szabályt, amely konfigurálja a terheléselosztó viselkedését. Ebben a példában egy egyszerű ciklikus multiplexelési szabály jön létre.

   ```powershell
   $rule = New-AzApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   ```

10. Konfigurálja az Application Gateway példányméretét. A rendelkezésre álló méretek **\_standard kicsi**, **standard\_közepes**és **\_standard nagy**.  A kapacitás esetében a rendelkezésre álló értékek **1** és **10 között**vannak.

    ```powershell
    $sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
    ```

    > [!NOTE]
    > Tesztelési célokra 1 példányszám választható ki. Fontos tudni, hogy a példányok száma két példány alatt nem tartozik az SLA hatálya alá, ezért nem ajánlott. Kis átjárók fejlesztési tesztelésre és nem éles célokra használható.

11. Konfigurálja az alkalmazásátjárón használandó TLS-házirendet. Az Application Gateway támogatja a TLS protokollverziók minimális verziójának beállítását.

    A következő értékek a definiálható protokollverziók listáját képezik:

    - **TLSV1_0**
    - **TLSV1_1**
    - **TLSV1_2**
    
    A következő példa a minimális protokollverziót **TLSv1_2** állítja, és lehetővé teszi a **\_TLS ECDHE\_ECDSA-t\_\_AES\_128\_GCM\_SHA256,** **TLS\_ECDHe\_ECDSA\_With\_AES\_256\_GCM\_SHA384**és **TLS\_RSA\_Csak AES\_\_128\_GCM\_SHA256-tal.**

    ```powershell
    $SSLPolicy = New-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -PolicyType Custom
    ```

## <a name="create-the-application-gateway"></a>Application Gateway létrehozása

Az összes előző lépéssel hozza létre az alkalmazásátjárót. Az átjáró létrehozása olyan folyamat, amely hosszú időt vesz igénybe.

V1 Termékváltozat esetén használja az alábbi parancsot
```powershell
$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -AuthenticationCertificates $authcert -Verbose
```

V2 Termékváltozat esetén használja az alábbi parancsot
```powershell
$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -TrustedRootCertificate $trustedRootCert01 -Verbose
```

## <a name="apply-a-new-certificate-if-the-back-end-certificate-is-expired"></a>Új tanúsítvány alkalmazása, ha a háttértanúsítvány lejárt

Ezzel az eljárással új tanúsítványt alkalmazhat, ha a háttértanúsítvány lejárt.

1. A frissítéshez az alkalmazásátjáró beolvasása.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```
   
2. Adja hozzá az új tanúsítvány-erőforrást a .cer fájlból, amely a tanúsítvány nyilvános kulcsát tartalmazza, és ugyanazt a tanúsítványt is hozzáadhatja a figyelőhöz a TLS-végződtetéshez az alkalmazásátjárón.

   ```powershell
   Add-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name 'NewCert' -CertificateFile "appgw_NewCert.cer" 
   ```
    
3. Az új hitelesítési tanúsítványobjektum bekérése egy változóba (TypeName: Microsoft.Azure.Commands.Network.Models.PSApplicationApplicationAuthenticationCertificate).

   ```powershell
   $AuthCert = Get-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name NewCert
   ```
 
 4. Rendelje hozzá az új tanúsítványt a **HáttérrendszerhezHttp** beállítás, és utalja át a $AuthCert változóval. (Adja meg a módosítani kívánt HTTP-beállítás nevét.)
 
   ```powershell
   $out= Set-AzApplicationGatewayBackendHttpSetting -ApplicationGateway $gw -Name "HTTP1" -Port 443 -Protocol "Https" -CookieBasedAffinity Disabled -AuthenticationCertificates $Authcert
   ```
    
 5. Véglegesítse a módosítást az alkalmazásátjáróban, és adja át az $out változóban található új konfigurációt.
 
   ```powershell
   Set-AzApplicationGateway -ApplicationGateway $gw  
   ```

## <a name="remove-an-unused-expired-certificate-from-http-settings"></a>Nem használt lejárt tanúsítvány eltávolítása a HTTP-beállítások közül

Ezzel az eljárással eltávolíthatja a nem használt lejárt tanúsítványokat a HTTP-beállítások közül.

1. A frissítéshez az alkalmazásátjáró beolvasása.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```
   
2. Sorolja fel az eltávolítani kívánt hitelesítési tanúsítvány nevét.

   ```powershell
   Get-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw | select name
   ```
    
3. Távolítsa el a hitelesítési tanúsítványt egy alkalmazásátjáróból.

   ```powershell
   $gw=Remove-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name ExpiredCert
   ```
 
 4. Véglegesítse a módosítást.
 
   ```powershell
   Set-AzApplicationGateway -ApplicationGateway $gw
   ```

   
## <a name="limit-tls-protocol-versions-on-an-existing-application-gateway"></a>TLS protokollverziók korlátozása meglévő alkalmazásátjárón

Az előző lépések során létrehozott egy alkalmazást teljes körű TLS-sel, és letiltásával bizonyos TLS protokollverziókat. A következő példa letiltja bizonyos TLS-házirendek egy meglévő alkalmazásátjáró.

1. A frissítéshez az alkalmazásátjáró beolvasása.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```

2. TLS-házirend definiálása. A következő példában a **TLSv1.0** és a **TLSv1.1** le van tiltva, és a **\_TLS ECDHE\_ECDSA\_With\_AES\_128\_GCM\_SHA256**, **TLS\_ECDHE\_ECDSA\_With\_AES\_256\_GCM\_SHA384**, és a **TLS\_RSA\_With\_AES\_128\_GCM\_SHA256** az egyetlenek.

   ```powershell
   Set-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -PolicyType Custom -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -ApplicationGateway $gw

   ```

3. Végül frissítse az átjárót. Ez az utolsó lépés egy hosszú ideig futó feladat. Ha ez megtörtént, végpontok között a TLS az alkalmazásátjárón van konfigurálva.

   ```powershell
   $gw | Set-AzApplicationGateway
   ```

## <a name="get-an-application-gateway-dns-name"></a>Alkalmazásátjáró DNS-nevének beszereznie

Az átjáró létrehozása után a következő lépés az előtér konfigurálása a kommunikációhoz. Az Application Gateway dinamikusan hozzárendelt DNS-nevet igényel nyilvános IP-cím használata esetén, amely nem megfelelő. Annak érdekében, hogy a végfelhasználók megüt az alkalmazás átjáró, használhatja a CNAME rekord az alkalmazásátjáró nyilvános végpontjára mutat. További információt az [Egyéni tartománynév konfigurálása az Azure-ban című témakörben talál.](../cloud-services/cloud-services-custom-domain-name-portal.md) 

Alias konfigurálásához olvassa be az alkalmazásátjáró és a hozzá tartozó IP/DNS-név adatait az alkalmazásátjáróhoz csatolt **PublicIPAddress** elem használatával. Az alkalmazásátjáró DNS-nevével hozzon létre egy CNAME rekordot, amely a két webalkalmazást erre a DNS-névre rögzíti. Nem javasoljuk az A-rekordok használatát, mert a VIP az alkalmazásátjáró újraindításakor módosítható.

```powershell
Get-AzPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
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

A webalkalmazások biztonságának az Application Gateway-en keresztül történő megerősítéséről a [Webalkalmazás tűzfalának áttekintése](application-gateway-webapplicationfirewall-overview.md)című témakörben olvashat bővebben.

[scenario]: ./media/application-gateway-end-to-end-SSL-powershell/scenario.png
