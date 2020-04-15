---
title: Egyéni mintavétel létrehozása a PowerShell használatával
titleSuffix: Azure Application Gateway
description: Megtudhatja, hogy miként hozhat létre egyéni mintavételt az Application Gateway alkalmazáshoz a PowerShell használatával az Erőforrás-kezelőben
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: f720a94d3467ce15ea5d58a8ece6de2a669f6258
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312589"
---
# <a name="create-a-custom-probe-for-azure-application-gateway-by-using-powershell-for-azure-resource-manager"></a>Egyéni mintavétel létrehozása az Azure Application Gateway alkalmazáshoz a PowerShell for Azure Resource Manager használatával

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Klasszikus Azure PowerShell](application-gateway-create-probe-classic-ps.md)

Ebben a cikkben egy egyéni mintavételt ad hozzá egy meglévő alkalmazásátjáróhoz a PowerShell használatával. Az egyéni mintavételek olyan alkalmazásokhoz hasznosak, amelyek egy adott állapot-ellenőrző lappal rendelkeznek, vagy olyan alkalmazások hoz hasznosak, amelyek nem nyújtanak sikeres választ az alapértelmezett webalkalmazáshoz.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-application-gateway-with-a-custom-probe"></a>Alkalmazásátjáró létrehozása egyéni mintavétellel

### <a name="sign-in-and-create-resource-group"></a>Bejelentkezés és erőforráscsoport létrehozása

1. A `Connect-AzAccount` hitelesítéshez használható.

   ```powershell
   Connect-AzAccount
   ```

1. A fiók előfizetésének lekérnie.

   ```powershell
   Get-AzSubscription
   ```

1. Válassza ki, hogy melyek Azure-előfizetését használja.

   ```powershell
   Select-AzSubscription -Subscriptionid '{subscriptionGuid}'
   ```

1. Hozzon létre egy erőforráscsoportot. Ezt a lépést kihagyhatja, ha meglévő erőforráscsoporttal rendelkezik.

   ```powershell
   New-AzResourceGroup -Name appgw-rg -Location 'West US'
   ```

Az Azure Resource Manager megköveteli, hogy minden erőforráscsoport adjon meg egy helyet. Ez a hely lesz az erőforráscsoport erőforrásainak alapértelmezett helye. Győződjön meg arról, hogy az alkalmazásátjáró létrehozásához szükséges összes parancs ugyanazt az erőforráscsoportot használja.

Az előző példában létrehoztunk egy **appgw-RG** nevű erőforráscsoportot **az USA nyugati részén.**

### <a name="create-a-virtual-network-and-a-subnet"></a>Hozzon létre egy virtuális hálózatot és egy alhálózatot

A következő példa létrehoz egy virtuális hálózatot és egy alhálózatot az alkalmazásátjáróhoz. Az alkalmazásátjáró használatához saját alhálózatra van szükség. Ezért az alkalmazásátjáróhoz létrehozott alhálózatnak kisebbnek kell lennie, mint a virtuális hálózat címterének, hogy más alhálózatokat hozhassanak létre és használhathassanak.

```powershell
# Assign the address range 10.0.0.0/24 to a subnet variable to be used to create a virtual network.
$subnet = New-AzVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

# Create a virtual network named appgwvnet in resource group appgw-rg for the West US region using the prefix 10.0.0.0/16 with subnet 10.0.0.0/24.
$vnet = New-AzVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet

# Assign a subnet variable for the next steps, which create an application gateway.
$subnet = $vnet.Subnets[0]
```

### <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Nyilvános IP-cím létrehozása az előtérbeli konfigurációhoz

Hozzon létre egy **publicIP01** nevű, nyilvános IP-címhez tartozó erőforrást az **appgw-rg** nevű erőforráscsoportban, az USA nyugati régiójában. Ez a példa egy nyilvános IP-címet használ az alkalmazásátjáró előtér-IP-címéhez.  Az alkalmazásátjáró megköveteli, hogy a nyilvános IP-cím `-DomainNameLabel` dinamikusan létrehozott DNS-névvel rendelkezik, ezért a nyilvános IP-cím létrehozása során nem adható meg.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName appgw-rg -Name publicIP01 -Location 'West US' -AllocationMethod Dynamic
```

### <a name="create-an-application-gateway"></a>Application Gateway létrehozása

Az alkalmazásátjáró létrehozása előtt az összes konfigurációs elemet be kell állítania. A következő példa létrehozza az alkalmazásátjáró-erőforrásokhoz szükséges konfigurációs elemeket.

| **Összetevő** | **Leírás** |
|---|---|
| **Átjáró IP-konfigurációja** | Egy alkalmazásátjáró IP-konfigurációja.|
| **Háttérkészlet** | A webalkalmazást üzemeltető alkalmazáskiszolgálókip-címek, teljes tartománynevek vagy hálózati adapterek készlete|
| **Állapotadat-mintavétel** | Egyéni mintavétel, amely a háttérkészlet-tagok állapotának figyelésére szolgál|
| **HTTP-beállítások** | Beállítások gyűjteménye, beleértve a portot, a protokollt, a cookie-alapú affinitást, a mintavételt és az időbeli meghosszabbítást.  Ezek a beállítások határozzák meg, hogyan történik a forgalom a háttérkészlet tagjaihoz való átirányítása.|
| **Előtér-port** | Az alkalmazásátjáró által figyelt port a forgalmat|
| **Figyelő** | Protokoll, előtér-IP-konfiguráció és előtér-port kombinációja. Ez az, ami figyeli a bejövő kérelmeket.
|**Szabály**| Http-beállítások alapján a megfelelő háttérrendszerhez irányítja a forgalmat.|

```powershell
# Creates an application gateway Frontend IP configuration named gatewayIP01
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

#Creates a back-end IP address pool named pool01 with IP addresses 134.170.185.46, 134.170.188.221, 134.170.185.50.
$pool = New-AzApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221, 134.170.185.50

# Creates a probe that will check health at http://contoso.com/path/path.htm
$probe = New-AzApplicationGatewayProbeConfig -Name probe01 -Protocol Http -HostName 'contoso.com' -Path '/path/path.htm' -Interval 30 -Timeout 120 -UnhealthyThreshold 8

# Creates the backend http settings to be used. This component references the $probe created in the previous command.
$poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 80

# Creates a frontend port for the application gateway to listen on port 80 that will be used by the listener.
$fp = New-AzApplicationGatewayFrontendPort -Name frontendport01 -Port 80

# Creates a frontend IP configuration. This associates the $publicip variable defined previously with the front-end IP that will be used by the listener.
$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

# Creates the listener. The listener is a combination of protocol and the frontend IP configuration $fipconfig and frontend port $fp created in previous steps.
$listener = New-AzApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

# Creates the rule that routes traffic to the backend pools.  In this example we create a basic rule that uses the previous defined http settings and backend address pool.  It also associates the listener to the rule
$rule = New-AzApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

# Sets the SKU of the application gateway, in this example we create a small standard application gateway with 2 instances.
$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

# The final step creates the application gateway with all the previously defined components.
$appgw = New-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location 'West US' -BackendAddressPools $pool -Probes $probe -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

## <a name="add-a-probe-to-an-existing-application-gateway"></a>Mintavétel hozzáadása meglévő alkalmazásátjáróhoz

A következő kódrészlet egy mintavételt ad hozzá egy meglévő alkalmazásátjáróhoz.

```powershell
# Load the application gateway resource into a PowerShell variable by using Get-AzApplicationGateway.
$getgw =  Get-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

# Create the probe object that will check health at http://contoso.com/path/path.htm
$getgw = Add-AzApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name probe01 -Protocol Http -HostName 'contoso.com' -Path '/path/custompath.htm' -Interval 30 -Timeout 120 -UnhealthyThreshold 8

# Set the backend HTTP settings to use the new probe
$getgw = Set-AzApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 120

# Save the application gateway with the configuration changes
Set-AzApplicationGateway -ApplicationGateway $getgw
```

## <a name="remove-a-probe-from-an-existing-application-gateway"></a>Mintavétel eltávolítása meglévő alkalmazásátjáróból

A következő kódrészlet eltávolítja a mintavételt egy meglévő alkalmazásátjáróból.

```powershell
# Load the application gateway resource into a PowerShell variable by using Get-AzApplicationGateway.
$getgw =  Get-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

# Remove the probe from the application gateway configuration object
$getgw = Remove-AzApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name $getgw.Probes.name

# Set the backend HTTP settings to remove the reference to the probe. The backend http settings now use the default probe
$getgw = Set-AzApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol http -CookieBasedAffinity Disabled

# Save the application gateway with the configuration changes
Set-AzApplicationGateway -ApplicationGateway $getgw
```

## <a name="get-application-gateway-dns-name"></a>Az Application Gateway DNS-nevének beszerzése

Az átjáró létrehozása után a következő lépés a kommunikációra szolgáló előtér konfigurálása. Nyilvános IP-cím esetén az Application Gateway használatához dinamikusan hozzárendelt DNS-névre van szükség, amely nem valódi név. Ha szeretné, hogy a végfelhasználók elérjék az Application Gatewayt, használjon egy Application Gateway nyilvános végpontjára mutató CNAME-rekordot. [Egyéni tartománynév konfigurálása az Azure-ban](../cloud-services/cloud-services-custom-domain-name-portal.md). A művelet végrehajtásához az Application Gateway részleteinek beszerzésére és a kapcsolódó IP/DNS-név lekérésére van szükség az Application Gatewayhez csatolt PublicIPAddress használatával. Az Application Gateway DNS-nevének használatával létrehozhat egy CNAME rekordot, amely a két webalkalmazást erre a DNS-névre irányítja. Az A-bejegyzések használata nem javasolt, mivel a virtuális IP-cím változhat az Application Gateway újraindításakor.

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

A TLS kiszervezésének konfigurálása a következő felkereséssel: [TLS kiszervezés konfigurálása](application-gateway-ssl-arm.md)

