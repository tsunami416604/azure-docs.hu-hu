---
title: "Végpontok közötti SSL konfigurálása az Azure Application Gateway |} Microsoft Docs"
description: "Ez a cikk ismerteti, hogyan végpontok közötti SSL konfigurálása az Azure Application Gateway PowerShell használatával"
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: e6d80a33-4047-4538-8c83-e88876c8834e
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/19/2017
ms.author: davidmu
ms.openlocfilehash: df14d5c4572a250f9f8951ee3b86e87e6f652782
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-powershell"></a>Végpontok közötti SSL konfigurálása az Alkalmazásátjáró a PowerShell használatával

## <a name="overview"></a>Áttekintés

Azure Application Gateway-végpontok közötti forgalom titkosítása támogatja. Alkalmazás-átjáró megszakítja az SSL-kapcsolat az Alkalmazásátjáró. Az átjáró ezután az útválasztási szabályokat alkalmazza a forgalomra, a csomag reencrypts, és továbbítja a csomagot a megfelelő háttér-kiszolgálófiók megadott útválasztási szabályok alapján. A webkiszolgáló esetleges válasza ugyanilyen módon jut el a végfelhasználóhoz.

Alkalmazásátjáró támogatja egyéni SSL-beállítások. Is támogatja a következő protokollverziója letiltása: **TLSv1.0**, **TLSv1.1**, és **TLSv1.2**, valamint mely titkosító csomagok használatához és preferencia szerinti sorrendben . SSL konfigurálható beállításokkal kapcsolatos további tudnivalókért tekintse meg a [SSL házirendek – áttekintés](application-gateway-SSL-policy-overview.md).

> [!NOTE]
> Az SSL 2.0 és az SSL 3.0 alapértelmezés szerint le vannak tiltva, és nem lehet engedélyezni. Azok a nem biztonságos minősülnek, és Alkalmazásátjáró nem használható.

![a forgatókönyv kép][scenario]

## <a name="scenario"></a>Forgatókönyv

Ebben a forgatókönyvben megismerheti, hogyan Alkalmazásátjáró létrehozása a PowerShell-végpontok közötti SSL használatával.

Ez a forgatókönyv tartalma:

* Hozzon létre egy erőforráscsoportot nevű **appgw-rg**.
* Hozzon létre egy virtuális hálózatot nevű **appgwvnet** a cím rendelkező **10.0.0.0/16**.
* Hozzon létre két alhálózat nevű **appgwsubnet** és **appsubnet**.
* Hozzon létre egy kis alkalmazás átjáró támogató végpontok közötti SSL-titkosítást, adott korlátok SSL protokoll verziója és a titkosító csomagok.

## <a name="before-you-begin"></a>Előkészületek

Végpontok közötti SSL konfigurálása az Alkalmazásátjáró, egy tanúsítványra szükség az átjáró a, és a tanúsítványokra szükség a háttér-kiszolgálókon. Az átjáró tanúsítványa titkosítása és visszafejtése az SSL-en keresztüli küldött szolgál. Az átjáró tanúsítványa kell lennie a személyes információcseréhez kapcsolódó (PFX) formátumú. Ezt a formátumot exportálja a titkos kulcsot, a titkosítási és visszafejtési forgalom az alkalmazás-átjáró által igényelt teszi lehetővé.

A végpontok közötti SSL-titkosítást a háttér szerepel az engedélyezési listán az alkalmazás-átjáróval kell lennie. Töltse fel a háttérkiszolgálók nyilvános tanúsítványát az Alkalmazásátjáró kell. A tanúsítvány hozzáadásán biztosítja, hogy az Alkalmazásátjáró csak kommunikál ismert háttér-példányok. Ez a végpontok közötti kommunikáció további biztonságossá tételére.

A konfigurációs folyamat az alábbi szakaszokban ismertetett.

## <a name="create-the-resource-group"></a>Az erőforráscsoport létrehozása

Ez a szakasz végigvezeti az Alkalmazásátjáró tartalmazó erőforráscsoport létrehozása.


   1. Jelentkezzen be az Azure-fiókjával.

   ```powershell
   Login-AzureRmAccount
   ```


   2. Válassza ki az előfizetést, az ebben a forgatókönyvben használandó.

   ```powershell
   Select-AzureRmsubscription -SubscriptionName "<Subscription name>"
   ```


   3. Hozzon létre egy erőforráscsoportot. (Hagyja ki ezt a lépést, ha egy meglévő erőforráscsoportot használ.)

   ```powershell
   New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
   ```

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Virtuális hálózat és alhálózat létrehozása az Application Gateway számára

Az alábbi példakód létrehozza a virtuális hálózat és két alhálózatból állnak. Egy alhálózat az Alkalmazásátjáró tárolására szolgál. A más alhálózati a hátsó végpontok, amelyek a webszolgáltatási alkalmazás üzemeltetéséhez használható.


   1. Az Alkalmazásátjáró használt alhálózati címtartományt rendelni.

   ```powershell
   $gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24
   ```

   > [!NOTE]
   > Az Alkalmazásátjáró konfigurált alhálózatok megfelelően kell méretezni. Alkalmazásátjáró legfeljebb 10 példányok konfigurálható. Minden példány egy IP-címet az alhálózatról vesz igénybe. Túl kicsi alhálózaton kedvezőtlen hatással lehet, olyan átjárót kiterjesztése.
   > 
   > 

   2. A háttér-címkészlethez használandó címtartományt rendelni.

   ```powershell
   $nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24
   ```

   3. Hozzon létre egy virtuális hálózat az előző lépések során meghatározott alhálózatokat.

   ```powershell
   $vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
   ```

   4. A virtuális hálózati és alhálózati erőforrást a következő lépések használhatók beolvasása.

   ```powershell
   $vnet = Get-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
   $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
   $nicSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet
   ```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Nyilvános IP-cím létrehozása az előtérbeli konfigurációhoz

Hozzon létre egy nyilvános IP-erőforrást az Alkalmazásátjáró használandó. A nyilvános IP-cím szerepel a következő lépések egyikét.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "West US" -AllocationMethod Dynamic
```

> [!IMPORTANT]
> Alkalmazásátjáró nem támogatja a nyilvános IP-cím létrehozása a megadott tartomány címkére használatát. Csak nyilvános IP-címnek a dinamikusan létrehozott tartományi címkére esetén támogatott. Ha egy rövid DNS-nevet az Alkalmazásátjáró van szüksége, ajánlott aliast egy CNAME rekordot használhat.

## <a name="create-an-application-gateway-configuration-object"></a>Hozzon létre egy Application Gateway konfigurációs objektumot

Az összes konfigurációs elemek vannak állítva az Alkalmazásátjáró létrehozása előtt. Az alábbi lépések létrehozzák az Application Gateway erőforráshoz szükséges konfigurációs elemeket.

   1. Hozzon létre egy alkalmazás átjáró IP-konfiguráció. Ezzel a beállítással használó alhálózatok az Alkalmazásátjáró. Alkalmazásátjáró indításakor azt szerzi be a beállított alhálózatból származó IP-címnek, és a hálózati forgalmat a háttér IP-címkészlet IP-címek útválasztását. Ne feledje, hogy minden példány egy IP-címet vesz fel.

   ```powershell
   $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet
   ```


   2. Hozzon létre egy előtér-IP-konfigurációt. Ez a beállítás a magán- vagy nyilvános IP-címet az Alkalmazásátjáró eleje rendeli hozzá. A következő lépés az előtér-IP-konfiguráció a nyilvános IP-cím az előző lépésben társítja.

   ```powershell
   $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip
   ```

   3. Konfigurálja a háttér IP-címkészlet a háttér-webkiszolgálók IP-címét. Ezek az IP-címek fogadják majd az előtérbeli IP-cím végpontból érkező hálózati forgalmat. Az IP-címek minta cserélje le a saját alkalmazás IP-cím végpontokat.

   ```powershell
   $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3
   ```

   > [!NOTE]
   > Egy teljesen minősített tartománynevét (FQDN) érték is érvényes a IP-cím helyett a háttér-kiszolgálókhoz. Használatával engedélyezheti a **- BackendFqdns** váltani. 


   4. Konfigurálja az előtér-IP-portját a nyilvános IP-végponton. Ez a port nem a portot, amelyet a végfelhasználók csatlakozni.

   ```powershell
   $fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443
   ```

   5. A tanúsítvány az alkalmazás-átjáró konfigurálása. Ezt a tanúsítványt a forgalmat az Alkalmazásátjáró reencrypt és visszafejtésére szolgál.

   ```powershell
   $cert = New-AzureRmApplicationGatewaySSLCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>
   ```

   > [!NOTE]
   > Ez a minta konfigurálja az SSL-kapcsolat használt tanúsítványt. A tanúsítványt kell .pfx formátumú, és a jelszó 4 – 12 karakterből állhat.

   6. Az alkalmazás átjáró a HTTP-figyelő létrehozása. Rendelje hozzá az előtér-IP-konfiguráció, egy portszám és egy SSL-tanúsítvány használatára.

   ```powershell
   $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert
   ```

   7. Töltse fel a háttér-készlet SSL-kompatibilis erőforrások használni kívánt tanúsítványt.

   > [!NOTE]
   > Az alapértelmezett mintavétel lekérdezi a nyilvános kulcs a a *alapértelmezett* SSL-kötést a háttér IP-cím és a nyilvános kulcs értékét, kapott a nyilvános kulcs értékét Ön adja meg itt hasonlítja össze. 
   
   > Ha állomásfejléc és kiszolgálónév jelzése (SNI) a háttérben futó használ, a letöltött nyilvános kulcs nem feltétlenül a kívánt helyet, hogy milyen forgalom. Ha kétségei vannak, látogasson el https://127.0.0.1/ erősítse meg, melyik tanúsítványt használják a háttér-kiszolgálókon a *alapértelmezett* SSL-kötést. A nyilvános kulcs a kérés használja ebben a szakaszban. Ha a HTTPS-kötések használunk állomásfejléc és SNI, és nem jelenik meg egy válasz és a tanúsítvány egy manuális böngésző kérelemből való https://127.0.0.1/ a háttér-kiszolgálókon, be kell állítania egy alapértelmezett SSL-kötést a rajtuk. Ha ezt nem teszi meg, mintavételt sikertelen lesz, és a program nem szerepel az engedélyezési listán.

   ```powershell
   $authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile C:\users\gwallace\Desktop\cert.cer
   ```

   > [!NOTE]
   > Ebben a lépésben megadott tanúsítvány nyilvános kulcsát a PFX-tanúsítványfájlt a háttérben futó jelen kell lennie. Exportálja a tanúsítványt (nem a legfelső szintű tanúsítvány) jogcím, a tanúsítás és mintafelismerési (CER) formátumban a háttér-kiszolgálóra telepíthető, és használhatja ezt a lépést. E lépés whitelists a háttérben, az alkalmazás átjáróval.

   8. Adja meg az alkalmazás átjáró háttér HTTP beállításait. Rendelje hozzá a HTTP-beállítások az előző lépésben feltöltött tanúsítvány.

   ```powershell
   $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert
   ```
   9. Hozzon létre egy terheléselosztó útválasztási szabály, amely a terheléselosztó GUID konfigurálja. Ebben a példában egy alapszintű ciklikus multiplexelési szabályt jön létre.

   ```powershell
   $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   ```

   10. Konfigurálja az Application Gateway példányméretét. Az elérhető értékek **szabványos\_kis**, **szabványos\_Közepes**, és **szabványos\_nagy**.  A kapacitás, a lehetséges értékek a következők **1** keresztül **10**.

   ```powershell
   $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
   ```

   > [!NOTE]
   > Tesztelési célokra példányszámának 1 választható ki. Fontos tudni, hogy bármely példányok száma alapján két példányt nem mutatja be az SLA-t, és ezért nem javasolt. Kis átjárók vannak fejlesztői teszteléshez és a termelési célra nem használható.

   11. Konfigurálja az SSL-házirend az Alkalmazásátjáró kell használni. Alkalmazásátjáró meg a minimális verziója az SSL protokoll verziója támogatja.

   A következő értékek, amelyek az protokoll-verziók listáját:

    - **TLSV1_0**
    - **TLSV1_1**
    - **TLSV1_2**
    
   Az alábbi példa állítja a minimális protokollverzió **TLSv1_2** és lehetővé teszi, hogy **TLS\_ECDHE\_ECDSA\_WITH\_AES\_128\_GCM\_SHA256**, **TLS\_ECDHE\_ECDSA\_WITH\_AES\_256\_GCM\_SHA384**, és **TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256** csak.

   ```powershell
   $SSLPolicy = New-AzureRmApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256"
   ```

## <a name="create-the-application-gateway"></a>Application Gateway létrehozása

Az Alkalmazásátjáró az előző lépések használatával hozhat létre. Az átjáró létrehozása az futtatásához hosszú ideig tart a művelet.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -AuthenticationCertificates $authcert -Verbose
```

## <a name="limit-ssl-protocol-versions-on-an-existing-application-gateway"></a>Az SSL protokoll verziói a meglévő Alkalmazásátjáró korlátozása

Az előző lépések tartott SSL végpontok közötti alkalmazás létrehozása és bizonyos SSL protokollverziója letiltása. A következő példa letiltja az egyes meglévő Alkalmazásátjáró SSL-házirendeket.

   1. Az Alkalmazásátjáró frissítése beolvasása.

   ```powershell
   $gw = Get-AzureRmApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```

   2. Egy SSL-házirend meghatározásához. A következő példában **TLSv1.0** és **TLSv1.1** le van tiltva, és a titkosítási csomagok **TLS\_ECDHE\_ECDSA\_WITH\_ AES\_128\_GCM\_SHA256**, **TLS\_ECDHE\_ECDSA\_WITH\_AES\_256\_GCM\_SHA384**, és **TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256** csak egyesek engedélyezett.

   ```powershell
   Set-AzureRmApplicationGatewaySSLPolicy -MinProtocolVersion -PolicyType Custom -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -ApplicationGateway $gw

   ```

   3. Végül frissítse az átjárót. Vegye figyelembe, hogy ezen utolsó lépésében megadja a hosszan futó feladatot. Ha elkészült, végpontok közötti SSL konfigurálva van az Alkalmazásátjáró.

   ```powershell
   $gw | Set-AzureRmApplicationGateway
   ```

## <a name="get-an-application-gateway-dns-name"></a>Egy alkalmazás átjáró DNS-név beolvasása

Az átjáró létrehozása után a következő lépésre konfigurálhatja az előtér-kommunikációra. Alkalmazásátjáró szükséges egy dinamikusan hozzárendelt DNS-név, ha egy nyilvános IP-cím használatával, amely nincs rövid. Annak érdekében, hogy a végfelhasználók is elérte az Alkalmazásátjáró, egy CNAME rekordot a segítségével a nyilvános végpontot az Alkalmazásátjáró mutasson. További információkért lásd: [az egyéni tartománynév konfigurálása az Azure-ban](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Alias konfigurálásához beolvasása részleteit az Alkalmazásátjáró és a hozzá tartozó IP-/ DNS-nevet használva a **PublicIPAddress** elem csatolva az Alkalmazásátjáró. Az Alkalmazásátjáró DNS-név használatával hozzon létre egy CNAME rekordot, mely a két webes alkalmazásokhoz, hogy a DNS-név. Azt nem A-rekordok használatát javasoljuk, mivel a VIP módosíthatja a indítsa újra az Alkalmazásátjáró.

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

Az alkalmazás átjárón keresztül webalkalmazási tűzfal a webalkalmazások biztonsági korlátozások kapcsolatos további információkért tekintse meg a [webalkalmazás tűzfal áttekintése](application-gateway-webapplicationfirewall-overview.md).

[scenario]: ./media/application-gateway-end-to-end-SSL-powershell/scenario.png
