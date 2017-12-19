---
title: "Webalkalmazási tűzfal konfigurálása: Azure Application Gateway |} Microsoft Docs"
description: "Ez a cikk útmutatást webalkalmazási tűzfal egy új vagy meglévő Alkalmazásátjáró használatának módjáról."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 670b9732-874b-43e6-843b-d2585c160982
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: davidmu
ms.openlocfilehash: e8106805d21b325e33fb3ab376db75cd783b9042
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="configure-a-web-application-firewall-on-a-new-or-existing-application-gateway"></a>Webalkalmazási tűzfal egy új vagy meglévő Alkalmazásátjáró konfigurálása

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-web-application-firewall-portal.md)
> * [PowerShell](application-gateway-web-application-firewall-powershell.md)
> * [Azure CLI](application-gateway-web-application-firewall-cli.md)

Megtudhatja, hogyan hozzon létre egy webes alkalmazás tűzfalat (waf-ot)-Alkalmazásátjáró engedélyezve van. Ismerje meg, egy WAF hozzáadása egy meglévő Alkalmazásátjáró is.

Az Azure alkalmazás átjáró WAF webalkalmazások védje a közös web-alapú támadások, például az SQL-injektálás, a többhelyes parancsfájlok futtatására és a munkamenet kihasználásának.

 Alkalmazásátjáró réteg-7 terheléselosztó. Feladatátvétel esetén a teljesítmény-útválasztási HTTP-kérelmek különböző kiszolgálók között, hogy be van kapcsolva a felhőbeli vagy helyszíni biztosít. Alkalmazásátjáró számos alkalmazás kézbesítési vezérlő (LÉPETT) szolgáltatásokat biztosítja:

 * HTTP terheléselosztás
 * A munkamenet cookie-alapú kapcsolat
 * Secure Sockets Layer (SSL) kiürítése
 * Egyéni állapot-mintavételi csomagjai
 * Többhelyes funkciók támogatása
 
 Támogatott szolgáltatások teljes listáját, lásd: [Alkalmazásátjáró áttekintése](application-gateway-introduction.md).

Ez a cikk bemutatja, hogyan [egy WAF hozzáadása egy meglévő Alkalmazásátjáró](#add-web-application-firewall-to-an-existing-application-gateway). Azt is bemutatja, hogyan [hozzon létre egy WAF használó Alkalmazásátjáró](#create-an-application-gateway-with-web-application-firewall).

![a forgatókönyv kép][scenario]

## <a name="waf-configuration-differences"></a>WAF konfigurációs különbségek

Ha, hogy elolvasta a [Alkalmazásátjáró létrehozása a PowerShell használatával](application-gateway-create-gateway-arm.md), hogy tudomásul veszi, hogy a Termékváltozat-beállítások konfigurálása az Alkalmazásátjáró létrehozása. A WAF Alkalmazásátjáró egy SKU konfigurálásakor adja meg a további beállításokat tartalmazza. Nincsenek további módosítások, amely akkor adja meg az Alkalmazásátjáró magát a.

| **Beállítás** | **Részletek**
|---|---|
|**Termékváltozat** |Egy normál Alkalmazásátjáró egy WAF nélkül támogatja **szabványos\_kis**, **szabványos\_Közepes**, és **szabványos\_nagy**méretét. A egy WAF bevezetése esetén két további SKU, **WAF\_Közepes** és **WAF\_nagy**. Egy WAF kis alkalmazásátjárót nem támogatott.|
|**Réteg** | A lehetséges értékek a következők **szabványos** vagy **WAF**. Egy WAF használata esetén ki kell választania **WAF**.|
|**Mód** | Ez a beállítás akkor a WAF módját. két érték engedélyezett **észlelési** és **megelőzési**. Ha a WAF be van állítva **észlelési** mód, minden fenyegetések vannak tárolva egy naplófájlt. A **megelőzési** mód, eseményeket naplózza, de a támadó megkapja a 403-as nem engedélyezett az Alkalmazásátjáró válaszát.|

## <a name="add-a-web-application-firewall-to-an-existing-application-gateway"></a>Webalkalmazási tűzfal hozzáadása egy meglévő Alkalmazásátjáró

Győződjön meg arról, hogy az Azure PowerShell legújabb verzióját használja. További információkért lásd: [a Windows Powershellt használja a Resource Manager](../powershell-azure-resource-manager.md).

1. Jelentkezzen be az Azure-fiókjával.

    ```powershell
    Login-AzureRmAccount
    ```

2. Válassza ki az előfizetést, az ebben a forgatókönyvben használandó.

    ```powershell
    Select-AzureRmSubscription -SubscriptionName "<Subscription name>"
    ```

3. Az átjáró, ahová a WAF hozzáadása beolvasása.

    ```powershell
    $gw = Get-AzureRmApplicationGateway -Name "AdatumGateway" -ResourceGroupName "MyResourceGroup"
    ```

4. Konfigurálja a WAF Termékváltozat. Az elérhető értékek **WAF\_nagy** és **WAF\_Közepes**. Ha egy WAF használja, a rétegnek különböznie kell **WAF**. Erősítse meg a kapacitás, ha úgy állítja be a Termékváltozat.

    ```powershell
    $gw | Set-AzureRmApplicationGatewaySku -Name WAF_Large -Tier WAF -Capacity 2
    ```

5. A WAF beállítások a következő példa a. A **FirewallMode**, a lehetséges értékek a következők **megelőzési** és **észlelési**.

    ```powershell
    $gw | Set-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode Prevention
    ```

6. Az Alkalmazásátjáró frissítése az előző lépésben megadott beállításokkal.

    ```powershell
    Set-AzureRmApplicationGateway -ApplicationGateway $gw
    ```

Ez a parancs egy WAF az Alkalmazásátjáró frissítése. Az alkalmazás átjáró naplók megtekintése ismertetése: [Alkalmazásátjáró diagnosztika](application-gateway-diagnostics.md). Egy WAF biztonsági jellegéből tekintse át a naplókat rendszeresen a webalkalmazások biztonsági állapotát.

## <a name="create-an-application-gateway-with-a-web-application-firewall"></a>Webalkalmazási tűzfal Alkalmazásátjáró létrehozása

A következő lépések a teljes folyamatot, amely egy WAF Alkalmazásátjáró létrehozása.

Győződjön meg arról, hogy az Azure PowerShell legújabb verzióját használja. További információkért lásd: [a Windows Powershellt használja a Resource Manager](../powershell-azure-resource-manager.md).

1. Jelentkezzen be Azure futtatásával `Login-AzureRmAccount`. Azokkal a hitelesítő adatait kéri.

2. Ellenőrizze a fiókhoz tartozó előfizetések futtatásával `Get-AzureRmSubscription`.

3. Válassza ki, mely Azure-előfizetés használatára.

    ```powershell
    Select-AzureRmsubscription -SubscriptionName "<Subscription name>"
    ```

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az Alkalmazásátjáró.

```powershell
New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
```

Az Azure Resource Manager megköveteli, hogy minden erőforráscsoport megadjon egy helyet. Ez a hely lesz az erőforráscsoport erőforrásainak alapértelmezett helye. Győződjön meg arról, hogy minden parancs Alkalmazásátjáró létrehozása ugyanabban az erőforráscsoportban.

Az előző példában létrehozott egy "appgw-RG" a következő helyen: "USA nyugati régiója." nevű erőforráscsoportban található

> [!NOTE]
> Ha egy egyéni mintát kell konfigurálnia az Application Gateway számára: [Application Gateway létrehozása egyéni mintákkal a PowerShell használatával](application-gateway-create-probe-ps.md). További információkért lásd: [egyéni mintavételt és az állapotfigyelés](application-gateway-probe-overview.md).

### <a name="configure-a-virtual-network"></a>Virtuális hálózat konfigurálása

Alkalmazásátjáró saját alhálózat szükséges. Ebben a lépésben egy címtartománnyal 10.0.0.0/16 és két alhálózat, egy alkalmazás átjáró és egy háttér-a készlet tagjainak a virtuális hálózat létrehozása.

```powershell
# Create a subnet configuration object for the application gateway subnet. A subnet for an application should have a minimum of 28 mask bits. This value leaves 10 available addresses in the subnet for application gateway instances. With a smaller subnet, you might not be able to add more instances of your application gateway in the future.
$gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

# Create a subnet configuration object for the back-end pool members subnet.
$nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

# Create the virtual network with the previously created subnets.
$vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
```

### <a name="configure-the-public-ip-address"></a>A nyilvános IP-cím konfigurálása

A külső tanúsítványigénylések az Alkalmazásátjáró egy nyilvános IP-címet igényel. A nyilvános IP-cím nem lehet egy `DomainNameLabel` definiálva az Alkalmazásátjáró által használható.

```powershell
# Create a public IP address for use with the application gateway. Defining the `DomainNameLabel` during creation is not supported for use with the application gateway.
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name 'appgwpip' -Location "West US" -AllocationMethod Dynamic
```

### <a name="configure-the-application-gateway"></a>Az Application Gateway konfigurálása

```powershell
# Create an IP configuration to configure which subnet the application gateway uses. When the application gateway starts, it picks up an IP address from the configured subnet and routes network traffic to the IP addresses in the back-end IP pool.
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

# Create a back-end pool to hold the addresses or NIC handles for the application that the application gateway is protecting.
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3

# Upload the authentication certificate to be used to communicate with the back-end servers.
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile <full path to .cer file>

# Configure the back-end HTTP settings to be used to define how traffic is routed to the back-end pool. The authentication certificate used in the previous step is added to the back-end HTTP settings.
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert

# Create a front-end port to be used by the listener.
$fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443

# Create a front-end IP configuration to associate the public IP address with the application gateway.
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

# Configure the certificate for the application gateway. This certificate is used to decrypt and re-encrypt the traffic on the application gateway.
$cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>

# Create the HTTP listener for the application gateway. Assign the front-end IP configuration, port, and SSL certificate to use.
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert

# Create a load-balancer routing rule that configures the load balancer behavior. In this example, a basic round-robin rule is created.
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

# Configure the SKU of the application gateway.
$sku = New-AzureRmApplicationGatewaySku -Name WAF_Medium -Tier WAF -Capacity 2

# Define the SSL policy to use.
$policy = New-AzureRmApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName AppGwSslPolicy20170401S

# Configure the WAF configuration settings.
$config = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"

# Create the application gateway by using all the previously created configuration objects.
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert
```

> [!NOTE]
> Az alapvető beállításokkal WAF létre alkalmazásátjárót védelmet CRS 3.0-val van állítva.

## <a name="get-an-application-gateway-dns-name"></a>Egy alkalmazás átjáró DNS-név beolvasása

Az átjáró létrehozása után a következő lépésre konfigurálhatja az előtér-kommunikációra. Egy nyilvános IP-cím használata esetén az Alkalmazásátjáró egy dinamikusan hozzárendelt DNS-nevet, amely nincs rövid van szükség. Győződjön meg arról, hogy a felhasználók is elérte az Alkalmazásátjáró, hogy használatával egy olyan CNAME rekordot a nyilvános végpontot az Alkalmazásátjáró. További információkért lásd: [egy egyéni tartománynév beállítása az Azure-felhőszolgáltatás](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Alias konfigurálásához beolvasása az Alkalmazásátjáró és a hozzá tartozó IP-/ DNS-nevet az Alkalmazásátjáró csatolva PublicIPAddress elem használatával. Az Alkalmazásátjáró DNS-név használatával hozzon létre egy CNAME rekordot, amely mutat, a két webes alkalmazásokhoz, hogy a DNS-név. Nem javasoljuk A rekordok használatával, mert a VIP előfordulhat, hogy módosítsa az Alkalmazásátjáró újraindításakor.

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

Diagnosztikai naplózás jelentkezzen az eseményeket, és megakadályozta egy WAF konfigurálásával kapcsolatban lásd: [Alkalmazásátjáró diagnosztika](application-gateway-diagnostics.md).

[scenario]: ./media/application-gateway-web-application-firewall-powershell/scenario.png
