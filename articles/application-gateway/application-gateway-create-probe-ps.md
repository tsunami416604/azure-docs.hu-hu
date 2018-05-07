---
title: Hozzon létre egy egyéni mintavételt - Azure Application Gateway - PowerShell |} Microsoft Docs
description: Megtudhatja, hogyan hozzon létre egy egyéni mintavétel az Alkalmazásátjáró PowerShell erőforrás-kezelő használatával
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
ms.openlocfilehash: 5180e659851a0ef5dbe92c451a9e2ba545821d07
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2018
---
# <a name="create-a-custom-probe-for-azure-application-gateway-by-using-powershell-for-azure-resource-manager"></a>Egy egyéni mintavétel létrehozása az Azure Application Gateway az Azure Resource Manager PowerShell használatával.

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Klasszikus Azure PowerShell](application-gateway-create-probe-classic-ps.md)

Ebben a cikkben ad hozzá egy egyéni mintavételt meglévő Alkalmazásátjáró a PowerShell használatával. Egyéni mintavételt az alkalmazásokat, amelyek egy adott állapotának ellenőrzése lapon vagy az alapértelmezett webes alkalmazás a sikeres válasz nem biztosító alkalmazások hasznosak.

> [!NOTE]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../azure-resource-manager/resource-manager-deployment-model.md).  Ez a cikk a Resource Manager-alapú üzemi modell használatát ismerteti, amelyet a Microsoft a legtöbb új telepítéshez a [klasszikus üzemi modell](application-gateway-create-probe-classic-ps.md) helyett javasol.

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-application-gateway-with-a-custom-probe"></a>Hozzon létre egy alkalmazás egy egyéni mintavétel

### <a name="sign-in-and-create-resource-group"></a>Jelentkezzen be, és az erőforráscsoport létrehozása

1. Használjon `Connect-AzureRmAccount` hitelesítéséhez.

  ```powershell
  Connect-AzureRmAccount
  ```

1. A fiókhoz tartozó előfizetések beolvasása.

  ```powershell
  Get-AzureRmSubscription
  ```

1. Válassza ki, hogy melyik Azure előfizetést fogja használni.

  ```powershell
  Select-AzureRmSubscription -Subscriptionid '{subscriptionGuid}'
  ```

1. Hozzon létre egy erőforráscsoportot. Ezt a lépést kihagyhatja, ha egy meglévő erőforráscsoportot.

  ```powershell
  New-AzureRmResourceGroup -Name appgw-rg -Location 'West US'
  ```

Az Azure Resource Manager megköveteli, hogy minden erőforráscsoport megadjon egy helyet. Ez a hely lesz az erőforráscsoport erőforrásainak alapértelmezett helye. Győződjön meg arról, hogy minden parancs Alkalmazásátjáró létrehozása ugyanabban az erőforráscsoportban.

Az előző példában létrehozott nevű erőforráscsoport **appgw-RG** helyen **USA nyugati régiója**.

### <a name="create-a-virtual-network-and-a-subnet"></a>Hozzon létre egy virtuális hálózatot és egy alhálózatot

Az alábbi példakód létrehozza a virtuális hálózat és az Alkalmazásátjáró alhálózatot. Alkalmazásátjáró saját alhálózatba használatát igényli. Emiatt az Alkalmazásátjáró létrehozott alhálózati kisebb, mint a vnet, hogy más alhálózatok létrehozott és használt lehetővé a címterületen belülre kell lennie.

```powershell
# Assign the address range 10.0.0.0/24 to a subnet variable to be used to create a virtual network.
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

# Create a virtual network named appgwvnet in resource group appgw-rg for the West US region using the prefix 10.0.0.0/16 with subnet 10.0.0.0/24.
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet

# Assign a subnet variable for the next steps, which create an application gateway.
$subnet = $vnet.Subnets[0]
```

### <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Nyilvános IP-cím létrehozása az előtérbeli konfigurációhoz

Hozzon létre egy **publicIP01** nevű, nyilvános IP-címhez tartozó erőforrást az **appgw-rg** nevű erőforráscsoportban, az USA nyugati régiójában. Ebben a példában az Alkalmazásátjáró előtér-IP-címét egy nyilvános IP-címet használja.  Alkalmazásátjáró igényel a nyilvános IP-cím, ezért rendelkeznie egy dinamikusan létrehozott DNS-nevet a `-DomainNameLabel` a nyilvános IP-cím létrehozása közben nem adható meg.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -Name publicIP01 -Location 'West US' -AllocationMethod Dynamic
```

### <a name="create-an-application-gateway"></a>Application Gateway létrehozása

Az Alkalmazásátjáró létrehozása előtt beállítása összes konfigurációs elemet. Az alábbi példakód létrehozza a konfigurációs elemek, amelyek szükségesek az alkalmazás átjáró-erőforráshoz.

| **Összetevő** | **Leírás** |
|---|---|
| **Átjáró IP-konfiguráció** | Egy alkalmazás átjáró IP-konfigurációt.|
| **Háttérkészlet** | IP-címek, FQDN-eknek vagy, a webszolgáltatási alkalmazás üzemeltetéséhez alkalmazáskiszolgálókra két hálózati adapterrel áll|
| **Állapotmintáihoz** | Egy egyéni mintavételt a háttérrendszer a készlet tagjainak állapotának figyelésére szolgál|
| **HTTP-beállítások** | Beleértve, port, protokoll, affinitási cookie-alapú, mintavételi és időtúllépés gyűjteménye.  Ezek a beállítások határozzák meg, hogyan továbbítódik a háttér címkészletet tagok|
| **Az elülső rétegbeli portot** | A port, amelyet figyeli az Alkalmazásátjáró forgalom|
| **Figyelő** | A protokoll előtérbeli IP-konfigurációja és az elülső rétegbeli portot kombinációja. Ez az, hogy mi a bejövő kéréseket figyeli.
|**A szabály**| Útvonalak a forgalmat a megfelelő háttér HTTP-beállításai alapján.|

```powershell
# Creates a application gateway Frontend IP configuration named gatewayIP01
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

#Creates a back-end IP address pool named pool01 with IP addresses 134.170.185.46, 134.170.188.221, 134.170.185.50.
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221, 134.170.185.50

# Creates a probe that will check health at http://contoso.com/path/path.htm
$probe = New-AzureRmApplicationGatewayProbeConfig -Name probe01 -Protocol Http -HostName 'contoso.com' -Path '/path/path.htm' -Interval 30 -Timeout 120 -UnhealthyThreshold 8

# Creates the backend http settings to be used. This component references the $probe created in the previous command.
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 80

# Creates a frontend port for the application gateway to listen on port 80 that will be used by the listener.
$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01 -Port 80

# Creates a frontend IP configuration. This associates the $publicip variable defined previously with the front-end IP that will be used by the listener.
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

# Creates the listener. The listener is a combination of protocol and the frontend IP configuration $fipconfig and frontend port $fp created in previous steps.
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

# Creates the rule that routes traffic to the backend pools.  In this example we create a basic rule that uses the previous defined http settings and backend address pool.  It also associates the listener to the rule
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

# Sets the SKU of the application gateway, in this example we create a small standard application gateway with 2 instances.
$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

# The final step creates the application gateway with all the previously defined components.
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location 'West US' -BackendAddressPools $pool -Probes $probe -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

## <a name="add-a-probe-to-an-existing-application-gateway"></a>A mintavétel hozzáadása egy meglévő Alkalmazásátjáró

A következő kódrészletet a mintavételi hozzáadása egy meglévő Alkalmazásátjáró.

```powershell
# Load the application gateway resource into a PowerShell variable by using Get-AzureRmApplicationGateway.
$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

# Create the probe object that will check health at http://contoso.com/path/path.htm
$getgw = Add-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name probe01 -Protocol Http -HostName 'contoso.com' -Path '/path/custompath.htm' -Interval 30 -Timeout 120 -UnhealthyThreshold 8

# Set the backend HTTP settings to use the new probe
$getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 120

# Save the application gateway with the configuration changes
Set-AzureRmApplicationGateway -ApplicationGateway $getgw
```

## <a name="remove-a-probe-from-an-existing-application-gateway"></a>Távolítsa el a Hálózatfigyelő a meglévő Alkalmazásátjáró

A következő kódrészletet a mintavételi eltávolítja a meglévő Alkalmazásátjáró.

```powershell
# Load the application gateway resource into a PowerShell variable by using Get-AzureRmApplicationGateway.
$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

# Remove the probe from the application gateway configuration object
$getgw = Remove-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name $getgw.Probes.name

# Set the backend HTTP settings to remove the reference to the probe. The backend http settings now use the default probe
$getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol http -CookieBasedAffinity Disabled

# Save the application gateway with the configuration changes
Set-AzureRmApplicationGateway -ApplicationGateway $getgw
```

## <a name="get-application-gateway-dns-name"></a>Az Application Gateway DNS-nevének beszerzése

Az átjáró létrehozása után a következő lépés a kommunikációra szolgáló előtér konfigurálása. Nyilvános IP-cím esetén az Application Gateway használatához dinamikusan hozzárendelt DNS-névre van szükség, amely nem valódi név. Ha szeretné, hogy a végfelhasználók elérjék az Application Gatewayt, használjon egy Application Gateway nyilvános végpontjára mutató CNAME-rekordot. [Egyéni tartománynév konfigurálása az Azure-ban](../cloud-services/cloud-services-custom-domain-name-portal.md). A művelet végrehajtásához az Application Gateway részleteinek beszerzésére és a kapcsolódó IP/DNS-név lekérésére van szükség az Application Gatewayhez csatolt PublicIPAddress használatával. Az Application Gateway DNS-nevének használatával létrehozhat egy CNAME rekordot, amely a két webalkalmazást erre a DNS-névre irányítja. Az A-bejegyzések használata nem javasolt, mivel a virtuális IP-cím változhat az Application Gateway újraindításakor.

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

Ismerkedjen meg az SSL-feladatkiszervezést ellátogatva konfigurálása: [SSL-kiszervezés konfigurálása](application-gateway-ssl-arm.md)

