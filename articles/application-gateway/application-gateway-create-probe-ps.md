---
title: Egyéni mintavétel - létrehozása az Azure Application Gatewayjel – PowerShell |} A Microsoft Docs
description: Egyéni mintavétel létrehozása az Application Gateway a Resource Managerben PowerShell használatával.
services: application-gateway
documentationcenter: na
author: vhorne
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: 68feb660-7fa4-4f69-a7e4-bdd7bdc474db
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: victorh
ms.openlocfilehash: b5ea7822d96e45d61ecef384d15d78e6a75fa46c
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2019
ms.locfileid: "57310726"
---
# <a name="create-a-custom-probe-for-azure-application-gateway-by-using-powershell-for-azure-resource-manager"></a>Egyéni mintavétel létrehozása az Azure Application Gateway Azure Resource Manager PowerShell használatával.

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Klasszikus Azure PowerShell](application-gateway-create-probe-classic-ps.md)

Ebben a cikkben egy meglévő application gateway a PowerShell használatával vegyen fel egy egyéni mintát. Az alkalmazásokat, egy adott állapotellenőrzési oldalt, vagy az alapértelmezett webalkalmazást a sikeres válasz nem biztosító alkalmazások egyéni minták hasznosak lehetnek.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-application-gateway-with-a-custom-probe"></a>Egyéni mintavétel az application gateway létrehozása

### <a name="sign-in-and-create-resource-group"></a>Bejelentkezés és erőforráscsoport létrehozása

1. Használat `Connect-AzAccount` hitelesítéséhez.

  ```powershell
  Connect-AzAccount
  ```

1. A fiókhoz tartozó előfizetések beolvasása.

  ```powershell
  Get-AzSubscription
  ```

1. Válassza ki, hogy melyik Azure előfizetést fogja használni.

  ```powershell
  Select-AzSubscription -Subscriptionid '{subscriptionGuid}'
  ```

1. Hozzon létre egy erőforráscsoportot. Ezt a lépést kihagyhatja, ha rendelkezik egy meglévő erőforráscsoportot.

  ```powershell
  New-AzResourceGroup -Name appgw-rg -Location 'West US'
  ```

Az Azure Resource Manager megköveteli, hogy minden erőforráscsoport megadjon egy helyet. Ez a hely lesz az erőforráscsoport erőforrásainak alapértelmezett helye. Győződjön meg arról, hogy az application gateway létrehozására irányuló összes parancs ugyanazt az erőforráscsoportot.

Az előző példában létrehoztunk egy erőforráscsoportot, nevű **appgw-RG** helyen **USA nyugati RÉGIÓJA**.

### <a name="create-a-virtual-network-and-a-subnet"></a>Hozzon létre egy virtuális hálózatot és egy alhálózatot

Az alábbi példa létrehoz egy virtuális hálózatot és egy alhálózatot az application gateway számára. Az Application gateway használja a saját alhálózatához van szüksége. Emiatt az application gateway számára létrehozott alhálózat kisebb, mint a többi alhálózathoz való létrehozásának és használatának engedélyezése a virtuális hálózat címteréhez kell lennie.

```powershell
# Assign the address range 10.0.0.0/24 to a subnet variable to be used to create a virtual network.
$subnet = New-AzVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

# Create a virtual network named appgwvnet in resource group appgw-rg for the West US region using the prefix 10.0.0.0/16 with subnet 10.0.0.0/24.
$vnet = New-AzVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet

# Assign a subnet variable for the next steps, which create an application gateway.
$subnet = $vnet.Subnets[0]
```

### <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Nyilvános IP-cím létrehozása az előtérbeli konfigurációhoz

Hozzon létre egy **publicIP01** nevű, nyilvános IP-címhez tartozó erőforrást az **appgw-rg** nevű erőforráscsoportban, az USA nyugati régiójában. Ebben a példában az előtérbeli IP-cím az application Gateway nyilvános IP-címet használ.  Az Application gateway igényel, ezért rendelkeznie egy dinamikusan létrehozott DNS-nevet a nyilvános IP-címet a `-DomainNameLabel` nyilvános IP-cím létrehozása közben nem adható meg.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName appgw-rg -Name publicIP01 -Location 'West US' -AllocationMethod Dynamic
```

### <a name="create-an-application-gateway"></a>Application Gateway létrehozása

Az application gateway létrehozása előtt beállítása minden konfigurációs elemet. Az alábbi példa létrehoz egy application gateway erőforráshoz szükséges konfigurációs elemeket.

| **Összetevő** | **Leírás** |
|---|---|
| **Átjáró IP-konfiguráció** | Az application Gateway IP-konfigurációval.|
| **Háttérkészlet** | A pool of IP addresses, FQDN's, or NICs that are to the application servers that host the web application|
| **Állapotadat-mintavétel** | Egyéni mintavétel háttérkészlet-tagokra állapotának figyelésére szolgál|
| **HTTP-beállítások** | Beállításai, többek között, port, protokoll, cookie-alapú affinitás, mintavétel és időtúllépési gyűjteménye.  Ezek a beállítások határozzák meg, hogyan van átirányítva a forgalom háttérkészlet-tagokra|
| **Előtérbeli port** | A port, amelyet az application gateway figyeli a forgalmat a|
| **Listener** | A protokoll, előtérbeli IP-konfiguráció és előtérbeli port kombinációját. Ez a mi figyeli a bejövő kéréseket.
|**Rule**| HTTP-beállításai alapján a forgalom a megfelelő háttérrendszerre útvonalakat.|

```powershell
# Creates a application gateway Frontend IP configuration named gatewayIP01
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

## <a name="add-a-probe-to-an-existing-application-gateway"></a>Mintavétel hozzáadása egy meglévő application gateway

A következő kódrészletet a mintavétel hozzáadása meglévő application gateway.

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

## <a name="remove-a-probe-from-an-existing-application-gateway"></a>Mintavétel eltávolítja a meglévő application gateway

Az alábbi kódrészlet egy mintavételt eltávolítja a meglévő application gateway.

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

Ismerkedjen meg az SSL-kiürítés funkcionáló konfigurálása: [SSL-alapú kiszervezés konfigurálása](application-gateway-ssl-arm.md)

