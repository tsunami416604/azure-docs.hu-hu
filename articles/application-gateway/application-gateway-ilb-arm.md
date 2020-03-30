---
title: Belső terheléselosztóval való használat - Azure Application Gateway
description: Ez az oldal utasításokat tartalmaz egy belső terheléselosztóval (ILB) rendelkező Azure Application Gateway létrehozásához, konfigurálásához, indításához és törléséhez az Azure Resource Manager számára
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: 9fbde84c499ba5b086ce812de63602c626b416b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74179333"
---
# <a name="create-an-application-gateway-with-an-internal-load-balancer-ilb"></a>Alkalmazásátjáró létrehozása belső terheléselosztóval (ILB)

Az Azure Application Gateway konfigurálható egy internetre irányuló virtuális IP-címhez vagy egy internettel nem érintkező belső végponthoz, más néven egy belső terheléselosztó (ILB) végponthoz. Az átjáró ILB-vel történő konfigurálása a belső üzletági alkalmazásoknál hasznos, amelyek nem érintkeznek az internettel. Emellett a többrétegű alkalmazások szolgáltatásai és rétegei számára is hasznos, amelyek egy internettel nem érintkező biztonsági korláton belül vannak, de attól még igényelik az időszeleteléses terheléselosztást, a munkamenet tartós használatát vagy a Secure Sockets Layer (SSL) lezárását.

Ez a cikk részletesen ismerteti egy Application Gateway ILB-hez történő konfigurálásának lépéseit.

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Telepítse az Azure PowerShell-modul legújabb verzióját a [telepítési utasításokat](/powershell/azure/install-az-ps)követve.
2. Létre kell hozni egy virtuális hálózatot és alhálózatot az Application Gateway számára. Győződjön meg arról, hogy egy virtuális gép vagy felhőalapú telepítés sem használja az alhálózatot. Az Application Gateway-nek egyedül kell lennie a virtuális hálózat alhálózatán.
3. A kiszolgálóknak, amelyeket az Application Gateway használatára konfigurál, már létezniük kell, illetve a virtuális hálózatban vagy hozzárendelt nyilvános/virtuális IP-címmel létrehozott végpontokkal kell rendelkezniük.

## <a name="what-is-required-to-create-an-application-gateway"></a>Mire van szükség egy Application Gateway létrehozásához?

* **Háttér-kiszolgálókészlet:** A háttérkiszolgálók IP-címeinek listája. A lenti listán szereplő IP-címeknek a virtuális hálózathoz kell tartozniuk, egy Application Gateway számára fenntartott másik alhálózatban, vagy nyilvános/virtuális IP-címnek kell lenniük.
* **Háttérkiszolgáló-készlet beállításai:** Minden készlet rendelkezik olyan beállításokkal, mint a port, a protokoll és a cookie-alapú affinitás. Ezek a beállítások egy adott készlethez kapcsolódnak, és a készlet minden kiszolgálójára érvényesek.
* **Előtér-port:** Ez a port az alkalmazásátjárón megnyitott nyilvános port. Amikor a forgalom eléri ezt a portot, a port átirányítja az egyik háttérkiszolgálóra.
* **Figyelő:** A figyelő egy előtérbeli porttal, egy protokollal (Http vagy Https, kis- és a nagybetűk megkülönböztetésével) és az SSL tanúsítványnévvel rendelkezik.
* **Szabály:** A szabály összeköti a figyelőt és a háttérkiszolgáló-készletet, és meghatározza, hogy mely háttérkiszolgáló-készletre legyen átirányítva a forgalom, ha elér egy adott figyelőt. Jelenleg csak a *basic* szabály támogatott. A *basic* szabály a ciklikus időszeleteléses terheléselosztás.

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

A klasszikus Azure és az Azure Resource Manager használata abban tér el egymástól, hogy más sorrendben kell létrehoznia az Application Gateway-t és a konfigurálást igénylő elemeket.
A Resource Managerrel az Application Gateway összes alkotóelemét külön kell konfigurálni, és csak utána kell őket összeállítani az Application Gateway-erőforrás létrehozásához.

A következő lépések szükségesek egy Application Gateway létrehozásához:

1. Erőforráscsoport létrehozása a Resource Managerhez
2. Virtuális hálózat és alhálózat létrehozása az Application Gateway számára
3. Hozzon létre egy Application Gateway konfigurációs objektumot
4. Application Gateway erőforrás létrehozása

## <a name="create-a-resource-group-for-resource-manager"></a>Erőforráscsoport létrehozása a Resource Managerhez

Az Azure Resource Manager parancsmagjainak használatához váltson át PowerShell módba. További információ: [A Windows PowerShell használata a Resource Managerrel](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>1. lépés

```powershell
Connect-AzAccount
```

### <a name="step-2"></a>2. lépés

Keresse meg a fiókot az előfizetésekben.

```powershell
Get-AzSubscription
```

A rendszer kérni fogja a hitelesítő adatokkal történő hitelesítést.

### <a name="step-3"></a>3. lépés

Válassza ki, hogy melyek Azure-előfizetését használja.

```powershell
Select-AzSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>4. lépés

Hozzon létre egy új erőforráscsoportot (hagyja ki ezt a lépést, ha egy meglévő erőforráscsoportot használ).

```powershell
New-AzResourceGroup -Name appgw-rg -location "West US"
```

Az Azure Resource Manager megköveteli, hogy minden erőforráscsoport adjon meg egy helyet. Ez szolgál az erőforráscsoport erőforrásainak alapértelmezett helyeként. Győződjön meg arról, hogy az Application Gateway létrehozására irányuló összes parancs ugyanazt az erőforráscsoportot használja.

Az előző példában létrehoztunk egy "appgw-rg" nevű erőforráscsoportot és "USA nyugati telephelye" nevű erőforráscsoportot.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Virtuális hálózat és alhálózat létrehozása az Application Gateway számára

Az alábbi példa bemutatja, hogyan hozhat létre egy virtuális hálózatot a Resource Manager használatával:

### <a name="step-1"></a>1. lépés

```powershell
$subnetconfig = New-AzVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

Ez a lépés a 10.0.0.0/24 címtartományt egy virtuális hálózat létrehozásához használt alhálózati változóhoz rendeli.

### <a name="step-2"></a>2. lépés

```powershell
$vnet = New-AzVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnetconfig
```

Ez a lépés létrehoz egy "appgwvnet" nevű virtuális hálózatot az "appgw-rg" erőforráscsoportban az USA nyugati régiójában a 10.0.0.0/16 előtag 10.0.0.0/24 alhálózattal.

### <a name="step-3"></a>3. lépés

```powershell
$subnet = $vnet.subnets[0]
```

Ez a lépés az alhálózati objektumot a változóhoz rendeli $subnet a következő lépésekhez.

## <a name="create-an-application-gateway-configuration-object"></a>Hozzon létre egy Application Gateway konfigurációs objektumot

### <a name="step-1"></a>1. lépés

```powershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

Ez a lépés létrehoz egy "gatewayIP01" nevű alkalmazásátjáró IP-konfigurációját. Amikor az Application Gateway elindul, a konfigurált alhálózatból felvesz egy IP-címet, és a hálózati forgalmat a háttérbeli IP-készlet IP-címeihez irányítja. Ne feledje, hogy minden példány egy IP-címet vesz fel.

### <a name="step-2"></a>2. lépés

```powershell
$pool = New-AzApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 10.1.1.8,10.1.1.9,10.1.1.10
```

Ez a lépés a "pool01" nevű háttér-IP-címkészletet "10.1.1.8, 10.1.1.9, 10.1.1.10" IP-címmel konfigurálja. Ezek az IP-címek fogadják az előtérbeli IP-végpontból érkező hálózati forgalmat. Az előző IP-címeket lecseréli a saját alkalmazása IP-címvégpontjaira.

### <a name="step-3"></a>3. lépés

```powershell
$poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

Ez a lépés az alkalmazásátjáró "poolsetting01" beállítását konfigurálja a háttérkészlet terheléselosztásos hálózati forgalmára.

### <a name="step-4"></a>4. lépés

```powershell
$fp = New-AzApplicationGatewayFrontendPort -Name frontendport01  -Port 80
```

Ez a lépés az ILB "frontendport01" nevű előtér-IP-portját konfigurálja.

### <a name="step-5"></a>5. lépés

```powershell
$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name fipconfig01 -Subnet $subnet
```

Ez a lépés létrehozza a "fipconfig01" nevű előtér-IP-konfigurációt, és az aktuális virtuális hálózati alhálózat privát IP-címéhez társítja azt.

### <a name="step-6"></a>6. lépés

```powershell
$listener = New-AzApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp
```

Ez a lépés létrehozza a figyelő úgynevezett "figyelő01", és társítja az előtér-port az előtér-IP-konfiguráció.

### <a name="step-7"></a>7. lépés

```powershell
$rule = New-AzApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

Ez a lépés létrehozza a "rule01" nevű terheléselosztó útválasztási szabályt, amely konfigurálja a terheléselosztó viselkedését.

### <a name="step-8"></a>8. lépés

```powershell
$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
```

Ez a lépés az alkalmazásátjáró példányméretét konfigurálja.

> [!NOTE]
> A kapacitás alapértelmezett értéke 2. A Sku-név hez Standard_Small, Standard_Medium és Standard_Large között választhat.

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>Application Gateway létrehozása a New-AzureApplicationGateway használatával

Létrehoz egy alkalmazásátjárót az előző lépések összes konfigurációs elemével. Ebben a példában az Application Gateway neve „appgwtest”.

```powershell
$appgw = New-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

Ez a lépés létrehoz egy alkalmazásátjárót az előző lépések összes konfigurációs elemével. Ebben a példában az Application Gateway neve „appgwtest”.

## <a name="delete-an-application-gateway"></a>Application Gateway törlése

Alkalmazásátjáró törléséhez a következő lépéseket kell tennie a következő sorrendben:

1. Állítsa le az átjárót a `Stop-AzApplicationGateway` parancsmaggal.
2. Távolítsa el az átjárót a `Remove-AzApplicationGateway` parancsmaggal.
3. A `Get-AzureApplicationGateway` parancsmaggal győződjön meg arról, hogy az átjáró el lett távolítva.

### <a name="step-1"></a>1. lépés

Társítsa az Application Gateway objektumot a „$getgw” változóhoz.

```powershell
$getgw =  Get-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

### <a name="step-2"></a>2. lépés

Állítsa le az Application Gatewayt a `Stop-AzApplicationGateway` parancsmaggal. Ez a `Stop-AzApplicationGateway` minta az első sorban lévő parancsmaletet mutatja, majd a kimenetet.

```powershell
Stop-AzApplicationGateway -ApplicationGateway $getgw  
```

```
VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error
----       ----------------     ------------                             ----
Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8
```

Amint az Application Gateway leállított állapotba kerül, távolítsa el a szolgáltatást a `Remove-AzApplicationGateway` parancsmaggal.

```powershell
Remove-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Force
```

```
VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error
----       ----------------     ------------                             ----
Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301
```

> [!NOTE]
> A **-force** kapcsolóval le lehet tiltani az eltávolítás jóváhagyása üzenetet.

A szolgáltatás eltávolításának ellenőrzéséhez használhatja a `Get-AzApplicationGateway` parancsmagot. Ez a lépés nem kötelező.

```powershell
Get-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

```
VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
```

## <a name="next-steps"></a>További lépések

Ha SSL-alapú kiszervezést szeretne konfigurálni: [Application Gateway konfigurálása SSL-alapú kiszervezéshez](application-gateway-ssl.md).

Ha további általános információra van szüksége a terheléselosztás beállításaival kapcsolatban:

* [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

