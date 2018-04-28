---
title: Azure Alkalmazásátjáró használata belső elosztott terhelésű - PowerShell |} Microsoft Docs
description: Ez az oldal utasításokat tartalmaz egy belső terheléselosztóval (ILB) rendelkező Azure Application Gateway létrehozásához, konfigurálásához, indításához és törléséhez az Azure Resource Manager számára
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 75cfd5a2-e378-4365-99ee-a2b2abda2e0d
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: davidmu
ms.openlocfilehash: dd6455622a7fecd99c23aef1b181035ffe6061dd
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="create-an-application-gateway-with-an-internal-load-balancer-ilb-by-using-azure-resource-manager"></a>Belső terheléselosztóval (ILB) rendelkező Application Gateway létrehozása az Azure Resource Manager használatával

> [!div class="op_single_selector"]
> * [Klasszikus Azure PowerShell](application-gateway-ilb.md)
> * [Azure Resource Manager PowerShell](application-gateway-ilb-arm.md)

Az Azure Application Gateway konfigurálható egy internetre irányuló virtuális IP-címhez vagy egy internettel nem érintkező belső végponthoz, más néven egy belső terheléselosztó (ILB) végponthoz. Az átjáró ILB-vel történő konfigurálása a belső üzletági alkalmazásoknál hasznos, amelyek nem érintkeznek az internettel. Emellett a többrétegű alkalmazások szolgáltatásai és rétegei számára is hasznos, amelyek egy internettel nem érintkező biztonsági korláton belül vannak, de attól még igényelik az időszeleteléses terheléselosztást, a munkamenet tartós használatát vagy a Secure Sockets Layer (SSL) lezárását.

Ez a cikk részletesen ismerteti egy Application Gateway ILB-hez történő konfigurálásának lépéseit.

## <a name="before-you-begin"></a>Előkészületek

1. Telepítse az Azure PowerShell-parancsmagok legújabb verzióját a Webplatform-telepítővel. A [Letöltések lap](https://azure.microsoft.com/downloads/) **Windows PowerShell** szakaszából letöltheti és telepítheti a legújabb verziót.
2. Létre kell hozni egy virtuális hálózatot és alhálózatot az Application Gateway számára. Győződjön meg arról, hogy egy virtuális gép vagy felhőalapú telepítés sem használja az alhálózatot. Az Application Gateway-nek egyedül kell lennie a virtuális hálózat alhálózatán.
3. A kiszolgálóknak, amelyeket az Application Gateway használatára konfigurál, már létezniük kell, illetve a virtuális hálózatban vagy hozzárendelt nyilvános/virtuális IP-címmel létrehozott végpontokkal kell rendelkezniük.

## <a name="what-is-required-to-create-an-application-gateway"></a>Mire van szükség egy Application Gateway létrehozásához?

* **Háttér-kiszolgálókészlet:** A háttérkiszolgálók IP-címeinek listája. A lenti listán szereplő IP-címeknek a virtuális hálózathoz kell tartozniuk, egy Application Gateway számára fenntartott másik alhálózatban, vagy nyilvános/virtuális IP-címnek kell lenniük.
* **Háttér-kiszolgálókészlet beállításai:** Minden készletnek vannak beállításai, például port, protokoll vagy cookie-alapú affinitás. Ezek a beállítások egy adott készlethez kapcsolódnak, és a készlet minden kiszolgálójára érvényesek.
* **Előtérbeli port:** Az Application Gateway-en megnyitott nyilvános port. Amikor a forgalom eléri ezt a portot, a port átirányítja az egyik háttérkiszolgálóra.
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

Az előző példában létrehozott egy erőforráscsoport neve "appgw-rg" és "Velünk nyugati" helyen.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Virtuális hálózat és alhálózat létrehozása az Application Gateway számára

Az alábbi példa bemutatja, hogyan hozhat létre egy virtuális hálózatot a Resource Manager használatával:

### <a name="step-1"></a>1. lépés

```powershell
$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

Ebben a lépésben a cím tartomány 10.0.0.0/24 rendel hozzá egy virtuális hálózat létrehozásához használandó alhálózati változó.

### <a name="step-2"></a>2. lépés

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnetconfig
```

Ebben a lépésben egy csoport "appgw-rg erőforrás" a "appgwvnet" nevű az USA nyugati régiója régió a előtag 10.0.0.0/16 használata a 10.0.0.0/24 alhálózat virtuális hálózatot hoz létre.

### <a name="step-3"></a>3. lépés

```powershell
$subnet = $vnet.subnets[0]
```

Ez a lépés rendel $subnet változó a következő lépéseket a alhálózati objektum alhálózatát.

## <a name="create-an-application-gateway-configuration-object"></a>Hozzon létre egy Application Gateway konfigurációs objektumot

### <a name="step-1"></a>1. lépés

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

Ebben a lépésben létrehozza a "gatewayIP01" nevű alkalmazás átjáró IP-konfigurációt. Amikor az Application Gateway elindul, a konfigurált alhálózatból felvesz egy IP-címet, és a hálózati forgalmat a háttérbeli IP-készlet IP-címeihez irányítja. Ne feledje, hogy minden példány egy IP-címet vesz fel.

### <a name="step-2"></a>2. lépés

```powershell
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 10.1.1.8,10.1.1.9,10.1.1.10
```

Ez a lépés konfigurál a háttér IP-címkészlet neve "pool01" IP-címek "10.1.1.8, 10.1.1.9, 10.1.1.10". Ezek az IP-címek fogadják az előtérbeli IP-végpontból érkező hálózati forgalmat. Az előző IP-címeket lecseréli a saját alkalmazása IP-címvégpontjaira.

### <a name="step-3"></a>3. lépés

```powershell
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

Ez a lépés konfigurál alkalmazás átjáró beállítás "poolsetting01" a terhelés eloszlik hálózati forgalmat a háttér-készletben.

### <a name="step-4"></a>4. lépés

```powershell
$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80
```

Ez a lépés konfigurál a "frontendport01" nevű a Példánynak az előtér-IP-port.

### <a name="step-5"></a>5. lépés

```powershell
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -Subnet $subnet
```

Ezzel a lépéssel hoz létre az előtér-IP-konfiguráció "fipconfig01" néven, és társítja azt egy privát IP-cím az az aktuális virtuális hálózati alhálózat.

### <a name="step-6"></a>6. lépés

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp
```

Ebben a lépésben létrehozza a "listener01" nevű figyelő, és hozzárendeli az előtér-portot az előtér-IP-konfiguráció.

### <a name="step-7"></a>7. lépés

```powershell
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

Ebben a lépésben létrehozza a terheléselosztó útválasztási szabályának neve "rule01", amely a terheléselosztó GUID konfigurálja.

### <a name="step-8"></a>8. lépés

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
```

Ez a lépés konfigurál az Alkalmazásátjáró meg.

> [!NOTE]
> Az *InstanceCount* alapértelmezett értéke 2, a maximális értéke pedig 10. A *GatewaySize* alapértelmezett értéke Közepes. A Standard_Small, Standard_Medium és a Standard_Large lehetőségek közül választhat.

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>Application Gateway létrehozása a New-AzureApplicationGateway használatával

Az előző lépéseket az összes konfigurációelemre Alkalmazásátjáró hoz létre. Ebben a példában az Application Gateway neve „appgwtest”.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

Ebben a lépésben Alkalmazásátjáró az előző lépések származó összes konfigurációelemeket hoz létre. Ebben a példában az Application Gateway neve „appgwtest”.

## <a name="delete-an-application-gateway"></a>Application Gateway törlése

Az Alkalmazásátjáró törlése, a következő lépések sorrendben kell:

1. Állítsa le az átjárót a `Stop-AzureRmApplicationGateway` parancsmaggal.
2. Távolítsa el az átjárót a `Remove-AzureRmApplicationGateway` parancsmaggal.
3. A `Get-AzureApplicationGateway` parancsmaggal győződjön meg arról, hogy az átjáró el lett távolítva.

### <a name="step-1"></a>1. lépés

Társítsa az Application Gateway objektumot a „$getgw” változóhoz.

```powershell
$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

### <a name="step-2"></a>2. lépés

Állítsa le az Application Gatewayt a `Stop-AzureRmApplicationGateway` parancsmaggal. Ez a példa bemutatja a `Stop-AzureRmApplicationGateway` az első sorban, a parancsmag kimenete követ.

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

