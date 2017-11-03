---
title: "Konfigurálja az SSL kiszervezési - Azure Application Gateway - PowerShell |} Microsoft Docs"
description: "Ez a cikk ismerteti az utasításokat követve hozzon létre egy alkalmazás SSL-kiszervezés Azure Resource Manager használatával"
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 3c3681e0-f928-4682-9d97-567f8e278e13
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/19/2017
ms.author: davidmu
ms.openlocfilehash: ee48ca45ae0d337b5b919dbbb28341caf8af0d45
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-azure-resource-manager"></a>Application Gateway konfigurálása SSL-alapú kiszervezéshez az Azure Resource Manager használatával

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-ssl-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)
> * [Az Azure klasszikus PowerShell](application-gateway-ssl.md)
> * [Azure CLI 2.0](application-gateway-ssl-cli.md)

Az Azure Application Gateway konfigurálható úgy, hogy leállítsa a Secure Sockets Layer (SSL) munkamenetét az átjárónál, így elkerülhetők a költséges SSL visszafejtési feladatok a webfarmon. Az SSL-alapú kiszervezés emellett leegyszerűsíti az előtér-kiszolgáló számára webalkalmazás telepítését és kezelését.

## <a name="before-you-begin"></a>Előkészületek

1. Telepítse az Azure PowerShell-parancsmagok legújabb verzióját a Webplatform-telepítővel. A [Letöltések lap](https://azure.microsoft.com/downloads/) **Windows PowerShell** szakaszából letöltheti és telepítheti a legújabb verziót.
2. Hozzon létre egy virtuális hálózatot és az Alkalmazásátjáró alhálózatot. Győződjön meg arról, hogy egy virtuális gép vagy felhőalapú telepítés sem használja az alhálózatot. Az Application Gateway-nek egyedül kell lennie a virtuális hálózat alhálózatán.
3. A kiszolgálókat, amelyeket az alkalmazás átjáró használatához konfigurálnia kell lennie, különben a végpontokat hozott létre a virtuális hálózat vagy a nyilvános IP-cím vagy a hozzárendelt virtuális IP-cím (VIP).

## <a name="what-is-required-to-create-an-application-gateway"></a>Mire van szükség egy Application Gateway létrehozásához?

* **Háttér-kiszolgálófiók készlet**: a háttér-kiszolgálók IP-címek listáját. Az IP-címek felsorolt kell tartoznia, a virtuális hálózati alhálózat, vagy egy nyilvános IP-cím/VIP kell lennie.
* **Háttér-kiszolgálófiók Készletbeállítások**: minden készlethez beállítások, például a portot, a protokoll és a cookie-alapú kapcsolat van. Ezek a beállítások egy adott készlethez kapcsolódnak, és a készlet minden kiszolgálójára érvényesek.
* **Előtér-port**: Ez a port nem nyilvános port meg van nyitva, az alkalmazás-átjárón. Amikor a forgalom eléri ezt a portot, a port átirányítja az egyik háttérkiszolgálóra.
* **Figyelő**: A figyelő rendelkezik egy előtér-portot, a protokollt (Http vagy Https; ezek a beállítások-és nagybetűk), és az SSL-tanúsítvány neve (ha az SSL beállításának-kiszervezés).
* **A szabály**: A szabály van kötve, a figyelő és a háttér-kiszolgálófiók készletben, és határozza meg, melyik háttér-kiszolgálófiók készlet át tudja irányítani a forgalmat, a találatok, amikor egy adott figyelő. Jelenleg csak a *basic* szabály támogatott. A *basic* szabály a ciklikus időszeleteléses terheléselosztás.

**További konfigurációs megjegyzések**

Az SSL-tanúsítványok konfigurálásához **Https**-re kell módosítani a **HttpListener** protokollját (megkülönböztetve a kis- és nagybetűket). Adja hozzá a **SslCertificate** elem **HttpListener** az SSL-tanúsítvány konfigurálva a változó értékével. Az előtér-port frissíteni kell, hogy **443-as**.

**Ahhoz, hogy a cookie-alapú kapcsolat**: annak érdekében, hogy egy ügyfél kérelmet mindig van irányítva az azonos virtuális gép a webfarm Alkalmazásátjáró konfigurálhatja. Ennek megvalósítása érdekében beszúrása egy munkamenetcookie-t, amely lehetővé teszi, hogy az átjáró megfelelően irányítja a forgalmat. A cookie-alapú affinitás engedélyezéséhez a **CookieBasedAffinity** paraméter beállítása legyen **Enabled** a **BackendHttpSettings** elemen belül.

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

Az Azure klasszikus üzembe helyezési modellel, és az Azure Resource Manager használatával közötti különbség Alkalmazásátjáró és be kell állítani a cikkek létrehozása sorrendje.

A Resource Manager összes összetevőjének olyan átjárót konfigurált külön-külön, és majd helyezze egy alkalmazás átjáró erőforrás létrehozásához.

Egy Application Gateway létrehozásához a következő lépéseket kell végrehajtania:

1. [Hozzon létre egy erőforráscsoportot az erőforrás-kezelő](#create-a-resource-group-for-resource-manager)
2. [Virtuális hálózati alhálózat és nyilvános IP-Címek használhatók az Alkalmazásátjáró létrehozása](#create-virtual-network-subnet-and-public-IP-for-the-application-gateway)
3. [Az alkalmazás átjáró konfigurációs objektum létrehozásához](#create-an-application-gateway-configuration-object)
4. [Alkalmazás átjáró erőforrás létrehozása](#create-an-application-gateway-resource)

## <a name="create-a-resource-group-for-resource-manager"></a>Erőforráscsoport létrehozása a Resource Managerhez

Az Azure Resource Manager parancsmagjainak használatához váltson át PowerShell módba. További információ: [A Windows PowerShell használata a Resource Managerrel](../powershell-azure-resource-manager.md).

   1. Írja be a következő parancsot:

   ```powershell
   Login-AzureRmAccount
   ```

   2. A fiókhoz tartozó előfizetések ellenőrzéséhez írja be a következő parancsokat:

   ```powershell
   Get-AzureRmSubscription
   ```

   A rendszer kérni fogja a hitelesítő adatokkal történő hitelesítést.

   3. Válassza ki, amelyek használatához Azure-előfizetését, írja be a következő parancsot:

   ```powershell
   Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
   ```

   4. Hozzon létre egy erőforráscsoportot, írja be a következő parancsot. (Hagyja ki ezt a lépést, ha egy meglévő erőforráscsoportot használ.)

   ```powershell
   New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
   ```

Az Azure Resource Manager megköveteli, hogy minden erőforráscsoport megadjon egy helyet. Ez a beállítás szolgál az erőforráscsoport erőforrásainak alapértelmezett helyeként. Győződjön meg arról, hogy minden parancs Alkalmazásátjáró létrehozása ugyanabban az erőforráscsoportban.

Az előző példában létrehozott nevű erőforráscsoport **appgw-RG** és a hely **USA nyugati régiója**.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Virtuális hálózat és alhálózat létrehozása az Application Gateway számára

Az alábbi példa bemutatja, hogyan hozhat létre egy virtuális hálózatot a Resource Manager használatával:

   1. Írja be a következő parancsot:

   ```powershell
   $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
   ```

   Ez a minta hozzárendeli a címtartomány **10.0.0.0/24** virtuális hálózat létrehozásához használandó alhálózati változóhoz.

   2. Írja be a következő parancsot:

   ```powershell
   $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
   ```

   Ez a minta létrehoz egy virtuális hálózatot nevű **appgwvnet** erőforráscsoportban **appgw-rg** a a **USA nyugati régiója** a előtag használatával régió **10.0.0.0/16** alhálózattal **10.0.0.0/24**.

   3. Írja be a következő parancsot:

   ```powershell
   $subnet = $vnet.Subnets[0]
   ```

   Ez a minta változóhoz rendeli hozzá az alhálózati objektum **$subnet** a következő lépéseket.

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Nyilvános IP-cím létrehozása az előtérbeli konfigurációhoz

Hozzon létre egy nyilvános IP-címet az előtér-konfigurációhoz, írja be a következő parancsot:

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

Ez a minta létrehoz egy nyilvános IP-erőforrás **publicIP01** erőforráscsoportban **appgw-rg** a a **USA nyugati régiója** régióban.

## <a name="create-an-application-gateway-configuration-object"></a>Hozzon létre egy Application Gateway konfigurációs objektumot

   1. Egy alkalmazás átjáró konfigurációs objektum létrehozásához adja meg a következő parancsot:

   ```powershell
   $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
   ```

   Ez a minta létrehoz egy alkalmazás átjáró IP-konfiguráció nevű **gatewayIP01**. Alkalmazásátjáró indításakor szerzi be a beállított alhálózatból származó IP-címnek, és továbbítja a háttér IP-címkészlet IP-címek a hálózati forgalom. Ne feledje, hogy minden példány egy IP-címet vesz fel.

   2. Írja be a következő parancsot:

   ```powershell
   $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50
   ```

   Ez a minta beállítja a háttér IP-címkészlet nevű **pool01** IP-címekkel rendelkező **134.170.185.46**, **134.170.188.221**, és **134.170.185.50 **. Ezek az értékek azok az IP-címek, amelyek fogadják majd az előtérbeli IP-végpontból érkező hálózati forgalmat. Cserélje le az előző példában szereplő IP-címeket a webalkalmazás végpontjainak IP-címeire.

   3. Írja be a következő parancsot:

   ```powershell
   $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Enabled
   ```

   Ez a minta konfigurálja az átjáró Alkalmazásbeállítás **poolsetting01** terheléselosztásához hálózati forgalmat a háttér-készletben.

   4. Írja be a következő parancsot:

   ```powershell
   $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 443
   ```

   Ez a minta konfigurálja az előtér-IP-port nevű **frontendport01** a nyilvános IP-végponton.

   5. Írja be a következő parancsot:

   ```powershell
   $cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path for certificate file> -Password "<password>"
   ```

   Ez a példa az SSL-kapcsolathoz használt tanúsítványt konfigurálja. A tanúsítvány PFX formátumban kell kell, és a jelszó 4 – 12 karakter közé kell esnie.

   6. Írja be a következő parancsot:

   ```powershell
   $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip
   ```

   Ez a minta hoz létre az előtér-IP-konfiguráció **fipconfig01** és a nyilvános IP-címet társít az előtér-IP-konfigurációt.

   7. Írja be a következő parancsot:

   ```powershell
   $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert
   ```

   Ez a minta létrehoz nevű figyelő **listener01** , és hozzárendeli az előtér-IP-konfiguráció és a tanúsítvány az előtér-port.

   8. Írja be a következő parancsot:

   ```powershell
   $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   ```

   Ez a minta hoz létre a terheléselosztó útválasztási szabály nevű **rule01** , konfigurálja a terheléselosztó viselkedését.

   9. Írja be a következő parancsot:

   ```powershell
   $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
   ```

   Ez az Application Gateway példányméretét konfigurálja.

   > [!NOTE]
   > Az alapértelmezett érték **InstanceCount** van **2**, maximális értéke 10. Az alapértelmezett érték **GatewaySize** van **Közepes**. A Standard_Small, Standard_Medium és a Standard_Large lehetőségek közül választhat.

   10. Írja be a következő parancsot:

   ```powershell
   $policy = New-AzureRmApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName AppGwSslPolicy20170401S
   ```

   Ez a lépés határozza meg az SSL-házirend az Alkalmazásátjáró használatára. További információkért lásd: [SSL konfigurálása házirend verziója és az Application Gateway titkosító csomagok](application-gateway-configure-ssl-policy-powershell.md).

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>Application Gateway létrehozása a New-AzureApplicationGateway használatával

Írja be a következő parancsot:

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslCertificates $cert -SslPolicy $policy
```

Ez a minta Alkalmazásátjáró az előző lépéseiből összes konfigurációs elemet hoz létre. A példában az Alkalmazásátjáró nevezik **appgwtest**.

## <a name="get-the-application-gateway-dns-name"></a>Az alkalmazás átjáró DNS-név beolvasása

Az átjáró létrehozása után a következő lépésre konfigurálhatja az előtér-kommunikációra. Alkalmazásátjáró szükséges egy dinamikusan hozzárendelt DNS-név, ha egy nyilvános IP-cím használatával, amely nincs rövid. Annak érdekében, hogy a végfelhasználók is elérte az Alkalmazásátjáró, egy CNAME rekordot a segítségével a nyilvános végpontot az Alkalmazásátjáró mutasson. További információkért lásd: [egyéni tartománynév konfigurálása az Azure-ban](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Ahhoz, hogy az alkalmazás átjáró DNS-név, részleteinek lehívására szolgáló az Alkalmazásátjáró és a hozzá tartozó IP-/ DNS-nevet használva a **PublicIPAddress** elem csatolva az Alkalmazásátjáró. Az Alkalmazásátjáró DNS-név használatával hozzon létre egy CNAME rekordot, amely mutat, a két webes alkalmazásokhoz, hogy a DNS-név. Azt nem A-rekordok használatát javasoljuk, mivel a VIP módosíthatja a indítsa újra az Alkalmazásátjáró.


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

Ha szeretne konfigurálni a belső terheléselosztót használ, tekintse meg az Alkalmazásátjáró [hozzon létre egy alkalmazást a belső terheléselosztók](application-gateway-ilb.md).

További információ a terheléselosztás beállítások általában, lásd:

* [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)
