---
title: Az Azure Application Gateway használatával a belső Load Balancer – PowerShell |} A Microsoft Docs
description: Ez az oldal utasításokat tartalmaz egy belső terheléselosztóval (ILB) rendelkező Azure Application Gateway létrehozásához, konfigurálásához, indításához és törléséhez az Azure Resource Manager számára
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.assetid: 75cfd5a2-e378-4365-99ee-a2b2abda2e0d
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/23/2018
ms.author: victorh
ms.openlocfilehash: 92d0e079f9fafbb6c000c6b1746f37a16add4cf7
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2019
ms.locfileid: "56417347"
---
# <a name="create-an-application-gateway-with-an-internal-load-balancer-ilb"></a>Application gateway létrehozása belső terheléselosztóval (ILB)

Az Azure Application Gateway konfigurálható egy internetre irányuló virtuális IP-címhez vagy egy internettel nem érintkező belső végponthoz, más néven egy belső terheléselosztó (ILB) végponthoz. Az átjáró ILB-vel történő konfigurálása a belső üzletági alkalmazásoknál hasznos, amelyek nem érintkeznek az internettel. Emellett a többrétegű alkalmazások szolgáltatásai és rétegei számára is hasznos, amelyek egy internettel nem érintkező biztonsági korláton belül vannak, de attól még igényelik az időszeleteléses terheléselosztást, a munkamenet tartós használatát vagy a Secure Sockets Layer (SSL) lezárását.

Ez a cikk részletesen ismerteti egy Application Gateway ILB-hez történő konfigurálásának lépéseit.

## <a name="before-you-begin"></a>Előkészületek

1. Telepítse az Azure PowerShell-parancsmagok legújabb verzióját a Webplatform-telepítővel. A [Letöltések lap](https://azure.microsoft.com/downloads/) **Windows PowerShell** szakaszából letöltheti és telepítheti a legújabb verziót.
2. Létre kell hozni egy virtuális hálózatot és alhálózatot az Application Gateway számára. Győződjön meg arról, hogy egy virtuális gép vagy felhőalapú telepítés sem használja az alhálózatot. Az Application Gateway-nek egyedül kell lennie a virtuális hálózat alhálózatán.
3. A kiszolgálóknak, amelyeket az Application Gateway használatára konfigurál, már létezniük kell, illetve a virtuális hálózatban vagy hozzárendelt nyilvános/virtuális IP-címmel létrehozott végpontokkal kell rendelkezniük.

## <a name="what-is-required-to-create-an-application-gateway"></a>Mire van szükség egy Application Gateway létrehozásához?

* **Háttér-kiszolgálókészlet:** A háttér-kiszolgálók IP-címek listája. A lenti listán szereplő IP-címeknek a virtuális hálózathoz kell tartozniuk, egy Application Gateway számára fenntartott másik alhálózatban, vagy nyilvános/virtuális IP-címnek kell lenniük.
* **Háttér-kiszolgálókészlet beállításai:** Minden készletnek vannak beállításai, például port, protokoll vagy cookie-alapú affinitás. Ezek a beállítások egy adott készlethez kapcsolódnak, és a készlet minden kiszolgálójára érvényesek.
* **Előtérbeli port:** A port az application gateway-en megnyitott nyilvános port. Amikor a forgalom eléri ezt a portot, a port átirányítja az egyik háttérkiszolgálóra.
* **Figyelő:** A figyelő rendelkezik egy előtérbeli porttal, egy protokollal (Http vagy Https, ezek olyan kis-és nagybetűket), és az SSL-tanúsítvány neve (-kiszervezés konfigurálásakor SSL).
* **Szabály:** A szabály összeköti a figyelőt és a háttérkiszolgáló-készletet, és meghatározza, melyik háttérkiszolgáló-készlethez átirányítva a forgalom kell lennie, ha elér egy adott figyelőt. Jelenleg csak a *basic* szabály támogatott. A *basic* szabály a ciklikus időszeleteléses terheléselosztás.

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
Connect-AzureRmAccount
```

### <a name="step-2"></a>2. lépés

Keresse meg a fiókot az előfizetésekben.

```powershell
Get-AzureRmSubscription
```

A rendszer kérni fogja a hitelesítő adatokkal történő hitelesítést.

### <a name="step-3"></a>3. lépés

Válassza ki, hogy melyik Azure előfizetést fogja használni.

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>4. lépés

Hozzon létre egy új erőforráscsoportot (hagyja ki ezt a lépést, ha egy meglévő erőforráscsoportot használ).

```powershell
New-AzureRmResourceGroup -Name appgw-rg -location "West US"
```

Az Azure Resource Manager megköveteli, hogy minden erőforráscsoport adjon meg egy helyet. Ez szolgál az erőforráscsoport erőforrásainak alapértelmezett helyeként. Győződjön meg arról, hogy az Application Gateway létrehozására irányuló összes parancs ugyanazt az erőforráscsoportot használja.

Az előző példában létrehoztunk egy "appgw-rg" és a helyen, "West US" nevű erőforráscsoportot.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Virtuális hálózat és alhálózat létrehozása az Application Gateway számára

Az alábbi példa bemutatja, hogyan hozhat létre egy virtuális hálózatot a Resource Manager használatával:

### <a name="step-1"></a>1. lépés

```powershell
$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

Ebben a lépésben a 10.0.0.0/24 címtartományt rendel egy virtuális hálózat létrehozásához használt egyik alhálózati változóhoz.

### <a name="step-2"></a>2. lépés

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnetconfig
```

Ebben a lépésben létrehoz egy virtuális hálózatot nevű "appgwvnet" erőforrás "appgw-rg" erőforráscsoportban az USA nyugati régiója, az előtag 10.0.0.0/16 használja a 10.0.0.0/24 alhálózattal.

### <a name="step-3"></a>3. lépés

```powershell
$subnet = $vnet.subnets[0]
```

Ebben a lépésben hozzárendeli az alhálózati objektumot a $subnet változóhoz a következő lépésekhez.

## <a name="create-an-application-gateway-configuration-object"></a>Hozzon létre egy Application Gateway konfigurációs objektumot

### <a name="step-1"></a>1. lépés

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

Ez a lépés létrehoz egy "gatewayIP01" nevű application gateway IP konfigurációt. Amikor az Application Gateway elindul, a konfigurált alhálózatból felvesz egy IP-címet, és a hálózati forgalmat a háttérbeli IP-készlet IP-címeihez irányítja. Ne feledje, hogy minden példány egy IP-címet vesz fel.

### <a name="step-2"></a>2. lépés

```powershell
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 10.1.1.8,10.1.1.9,10.1.1.10
```

Ebben a lépésben a háttérbeli IP-címkészletet konfigurálja a "pool01" nevű IP-címek "10.1.1.8, 10.1.1.9, 10.1.1.10". Ezek az IP-címek fogadják az előtérbeli IP-végpontból érkező hálózati forgalmat. Az előző IP-címeket lecseréli a saját alkalmazása IP-címvégpontjaira.

### <a name="step-3"></a>3. lépés

```powershell
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

Ebben a lépésben konfigurálja a háttérkészlet application gateway beállítást "poolsetting01"nevű a betöltés elosztott terhelésű hálózati forgalmat.

### <a name="step-4"></a>4. lépés

```powershell
$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80
```

Ebben a lépésben konfigurálja az ILB-hez a "frontendport01" nevű előtérbeli IP-portot.

### <a name="step-5"></a>5. lépés

```powershell
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -Subnet $subnet
```

Ebben a lépésben létrehozza a "fipconfig01" nevű előtérbeli IP-konfigurációt, és társítja azt az aktuális virtuális hálózati alhálózatról egy privát IP.

### <a name="step-6"></a>6. lépés

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp
```

Ebben a lépésben létrehozza a "listener01" nevű, és társítja az előtérbeli portot az előtérbeli IP-konfigurációhoz.

### <a name="step-7"></a>7. lépés

```powershell
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

Ebben a lépésben létrehozza a terheléselosztási útválasztási szabályt "rule01" nevű, amely a terheléselosztó viselkedését konfigurálja.

### <a name="step-8"></a>8. lépés

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
```

Ez a lépés az application gateway példányméretét konfigurálja.

> [!NOTE]
> A kapacitás alapértelmezett értéke 2. Sku-név az Standard_Small, Standard_Medium és a standard_large lehetőségek közül választhat.

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>Application Gateway létrehozása a New-AzureApplicationGateway használatával

Létrehoz egy application gateway az előző lépések konfigurációs elemeivel. Ebben a példában az Application Gateway neve „appgwtest”.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

Ebben a lépésben létrehoz egy application gateway az előző lépések konfigurációs elemeivel. Ebben a példában az Application Gateway neve „appgwtest”.

## <a name="delete-an-application-gateway"></a>Application Gateway törlése

Egy application gateway törléséhez kövesse sorrendben az alábbi lépéseket kell:

1. Állítsa le az átjárót a `Stop-AzureRmApplicationGateway` parancsmaggal.
2. Távolítsa el az átjárót a `Remove-AzureRmApplicationGateway` parancsmaggal.
3. A `Get-AzureApplicationGateway` parancsmaggal győződjön meg arról, hogy az átjáró el lett távolítva.

### <a name="step-1"></a>1. lépés

Társítsa az Application Gateway objektumot a „$getgw” változóhoz.

```powershell
$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

### <a name="step-2"></a>2. lépés

Állítsa le az Application Gatewayt a `Stop-AzureRmApplicationGateway` parancsmaggal. Ez a példa bemutatja a `Stop-AzureRmApplicationGateway` parancsmag első sorát, a kimenet követ.

```powershell
Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  
```

```
VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error
----       ----------------     ------------                             ----
Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8
```

Amint az Application Gateway leállított állapotba kerül, távolítsa el a szolgáltatást a `Remove-AzureRmApplicationGateway` parancsmaggal.

```powershell
Remove-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Force
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

A szolgáltatás eltávolításának ellenőrzéséhez használhatja a `Get-AzureRmApplicationGateway` parancsmagot. Ez a lépés nem kötelező.

```powershell
Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

```
VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
```

## <a name="next-steps"></a>További lépések

Ha SSL-alapú kiszervezést szeretne konfigurálni: [Application Gateway konfigurálása SSL-alapú kiszervezéshez](application-gateway-ssl.md).

Ha konfigurálni szeretne egy ILB-vel használni kívánt Application Gateway-t: [Application Gateway létrehozása belső terheléselosztóval (ILB)](application-gateway-ilb.md).

Ha további általános információra van szüksége a terheléselosztás beállításaival kapcsolatban:

* [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

