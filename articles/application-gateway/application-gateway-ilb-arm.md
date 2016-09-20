<properties
   pageTitle="Belső terheléselosztóval (ILB) rendelkező Application Gateway létrehozása és konfigurálása az Azure Resource Manager használatával | Microsoft Azure"
   description="Ez az oldal utasításokat tartalmaz egy belső terheléselosztóval (ILB) rendelkező Azure Application Gateway létrehozásához, konfigurálásához, indításához és törléséhez az Azure Resource Manager számára"
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/19/2016"
   ms.author="gwallace"/>


# Belső terheléselosztóval (ILB) rendelkező Application Gateway létrehozása az Azure Resource Manager használatával

> [AZURE.SELECTOR]
- [Klasszikus Azure-lépések](application-gateway-ilb.md)
- [Resource Manager PowerShell-lépések](application-gateway-ilb-arm.md)

Az Azure Application Gateway konfigurálható egy internetre irányuló virtuális IP-címhez vagy egy internettel nem érintkező belső végponthoz, más néven egy belső terheléselosztó (ILB) végponthoz. Az átjáró ILB-vel történő konfigurálása a belső üzletági alkalmazásoknál hasznos, amelyek nem érintkeznek az internettel. Emellett a többrétegű alkalmazások szolgáltatásai és rétegei számára is hasznos, amelyek egy internettel nem érintkező biztonsági korláton belül vannak, de attól még igényelik az időszeleteléses terheléselosztást, a munkamenet tartós használatát vagy a Secure Sockets Layer (SSL) lezárását.

Ez a cikk részletesen ismerteti egy Application Gateway ILB-hez történő konfigurálásának lépéseit.

## Előkészületek

1. Telepítse az Azure PowerShell-parancsmagok legújabb verzióját a Webplatform-telepítővel. A [Letöltések lap](https://azure.microsoft.com/downloads/) **Windows PowerShell** szakaszából letöltheti és telepítheti a legújabb verziót.
2. Létre kell hozni egy virtuális hálózatot és alhálózatot az Application Gateway számára. Győződjön meg arról, hogy egy virtuális gép vagy felhőalapú telepítés sem használja az alhálózatot. Az Application Gateway-nek egyedül kell lennie a virtuális hálózat alhálózatán.
3. A kiszolgálóknak, amelyeket az Application Gateway használatára konfigurál, már létezniük kell, illetve a virtuális hálózatban vagy hozzárendelt nyilvános/virtuális IP-címmel létrehozott végpontokkal kell rendelkezniük.

## Mire van szükség egy Application Gateway létrehozásához?


- **Háttér-kiszolgálókészlet:** A háttérkiszolgálók IP-címeinek listája. A lenti listán szereplő IP-címeknek a virtuális hálózathoz kell tartozniuk, egy Application Gateway számára fenntartott másik alhálózatban, vagy nyilvános/virtuális IP-címnek kell lenniük.
- **Háttér-kiszolgálókészlet beállításai:** Minden készletnek vannak beállításai, például port, protokoll vagy cookie-alapú affinitás. Ezek a beállítások egy adott készlethez kapcsolódnak, és a készlet minden kiszolgálójára érvényesek.
- **Előtérbeli port:** Az Application Gateway-en megnyitott nyilvános port. Amikor a forgalom eléri ezt a portot, a port átirányítja az egyik háttérkiszolgálóra.
- **Figyelő:** A figyelő egy előtérbeli porttal, egy protokollal (Http vagy Https, kis- és a nagybetűk megkülönböztetésével) és az SSL tanúsítványnévvel rendelkezik.
- **Szabály:** A szabály összeköti a figyelőt és a háttérkiszolgáló-készletet, és meghatározza, hogy mely háttérkiszolgáló-készletre legyen átirányítva a forgalom, ha elér egy adott figyelőt. Jelenleg csak a *basic* szabály támogatott. A *basic* szabály a ciklikus időszeleteléses terheléselosztás.



## Application Gateway létrehozása

A klasszikus Azure és az Azure Resource Manager használata abban tér el egymástól, hogy más sorrendben kell létrehoznia az Application Gateway-t és a konfigurálást igénylő elemeket.
A Resource Managerrel az Application Gateway összes alkotóelemét külön kell konfigurálni, és csak utána kell őket összeállítani az Application Gateway-erőforrás létrehozásához.


A következő lépések szükségesek egy Application Gateway létrehozásához:

1. Erőforráscsoport létrehozása a Resource Managerhez
2. Virtuális hálózat és alhálózat létrehozása az Application Gateway számára
3. Hozzon létre egy Application Gateway konfigurációs objektumot
4. Application Gateway erőforrás létrehozása


## Erőforráscsoport létrehozása a Resource Managerhez

Az Azure Resource Manager parancsmagjainak használatához váltson át PowerShell módba. További információ: [A Windows PowerShell használata a Resource Managerrel](../powershell-azure-resource-manager.md).

### 1. lépés

    Login-AzureRmAccount

### 2. lépés

Keresse meg a fiókot az előfizetésekben.

    Get-AzureRmSubscription

A rendszer kérni fogja a hitelesítő adatokkal történő hitelesítést.<BR>

### 3. lépés

Válassza ki, hogy melyik Azure előfizetést fogja használni. <BR>


    Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### 4. lépés

Hozzon létre egy új erőforráscsoportot (hagyja ki ezt a lépést, ha egy meglévő erőforráscsoportot használ).

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

Az Azure Resource Manager megköveteli, hogy minden erőforráscsoport adjon meg egy helyet. Ez szolgál az erőforráscsoport erőforrásainak alapértelmezett helyeként. Győződjön meg arról, hogy az Application Gateway létrehozására irányuló összes parancs ugyanazt az erőforráscsoportot használja.

A fenti példában létrehoztunk egy „appgw-rg” nevű erőforráscsoportot, amelynek az USA nyugati régiója, West US” a helye.

## Virtuális hálózat és alhálózat létrehozása az Application Gateway számára

Az alábbi példa bemutatja, hogyan hozhat létre egy virtuális hálózatot a Resource Manager használatával:

### 1. lépés

    $subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

Ez hozzárendeli a 10.0.0.0/24 címtartományt a virtuális hálózat létrehozásához használni kívánt egyik alhálózati változóhoz.

### 2. lépés

    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnetconfig

Ez létrehoz egy „appgwvnet” nevű virtuális hálózatot az „appgw-rg” erőforráscsoportban az USA nyugati régiója számára, amely a 10.0.0.0/16 előtagot használja a 10.0.0.0/24 alhálózattal.

### 3. lépés

    $subnet = $vnet.subnets[0]

Ez hozzárendeli az alhálózat objektumot a $subnet változóhoz a következő lépésekre.

## Hozzon létre egy Application Gateway konfigurációs objektumot

### 1. lépés

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

Ez létrehoz egy „gatewayIP01” nevű Application Gateway IP-konfigurációt. Amikor az Application Gateway elindul, a konfigurált alhálózatból felvesz egy IP-címet, és a hálózati forgalmat a háttérbeli IP-készlet IP-címeihez irányítja. Ne feledje, hogy minden példány egy IP-címet vesz fel.


### 2. lépés

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

Ez a „pool01” nevű háttérbeli IP-címkészletet konfigurálja az alábbi IP-címekkel: „134.170.185.46, 134.170.188.221,134.170.185.50”. Ezek az IP-címek fogadják az előtérbeli IP-végpontból érkező hálózati forgalmat. A fenti IP-címeket később lecseréli a saját alkalmazása IP-cím végpontjaira.

### 3. lépés

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled

Ez a „poolsetting01” nevű Application Gateway beállítást konfigurálja a háttérkészlet elosztott terhelésű hálózati forgalmához.

### 4. lépés

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

Ez a „frontendport01” nevű előtérbeli IP-portot konfigurálja az ILB-hez.

### 5. lépés

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -Subnet $subnet

Ez létrehozza a „fipconfig01” nevű előtérbeli IP-konfigurációt, és hozzárendeli a jelenlegi virtuális hálózat alhálózatának egyik privát IP-címéhez.

### 6. lépés

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

Ez létrehozza a „listener01” nevű figyelőt, és társítja az előtérbeli portot az előtérbeli IP-konfigurációhoz.

### 7. lépés

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

Ez létrehozza a „rule01” nevű terheléselosztási útválasztási szabályt, amely a terheléselosztó viselkedését konfigurálja.

### 8. lépés

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

Ez konfigurálja az Application Gateway példányméretét.

>[AZURE.NOTE]  Az *InstanceCount* alapértelmezett értéke 2, a maximális értéke pedig 10. A *GatewaySize* alapértelmezett értéke Közepes. A Standard_Small, Standard_Medium és a Standard_Large lehetőségek közül választhat.

## Application Gateway létrehozása a New-AzureApplicationGateway használatával

Létrehoz egy Application Gateway-t a fenti lépések konfigurációs elemeivel. Ebben a példában az Application Gateway neve „appgwtest”.


    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

Ez létrehoz egy Application Gateway-t a fenti lépések konfigurációs elemeivel. Ebben a példában az Application Gateway neve „appgwtest”.


## Application Gateway törlése

Egy Application Gateway törléséhez az alábbi lépéseket kell sorrendben végrehajtania:

1. Állítsa le az átjárót a **Stop-AzureRmApplicationGateway** parancsmaggal.
2. Távolítsa el az átjárót a **Remove-AzureRmApplicationGateway** parancsmaggal.
3. A **Get-AzureApplicationGateway** parancsmaggal győződjön meg arról, hogy az átjáró el lett távolítva.


### 1. lépés

Társítsa az Application Gateway objektumot a „$getgw” változóhoz.

    $getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### 2. lépés

Állítsa le az Application Gateway-t a **Stop-AzureRmApplicationGateway** parancsmaggal. Ez a minta a **Stop-AzureRmApplicationGateway** parancsmag első sorát mutatja, amelyet a kimenet követ.

    PS C:\> Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  

    VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
    VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8

Amint az Application Gateway leállított állapotba kerül, távolítsa el a szolgáltatást a **Remove-AzureRmApplicationGateway** parancsmaggal.


    PS C:\> Remove-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Force

    VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
    VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301

>[AZURE.NOTE] A **-force** kapcsolóval le lehet tiltani az eltávolítás jóváhagyása üzenetet.


A szolgáltatás eltávolításának ellenőrzéséhez használhatja a **Get-AzureRmApplicationGateway** parancsmagot. Ez a lépés nem kötelező.


    PS C:\>Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

    VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

    Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
    .....

## Következő lépések

Ha SSL-alapú kiszervezést szeretne konfigurálni: [Application Gateway konfigurálása SSL-alapú kiszervezéshez](application-gateway-ssl.md).

Ha konfigurálni szeretne egy ILB-vel használni kívánt Application Gateway-t: [Application Gateway létrehozása belső terheléselosztóval (ILB)](application-gateway-ilb.md).

Ha további általános információra van szüksége a terheléselosztás beállításaival kapcsolatban:

- [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)



<!--HONumber=sep16_HO1-->


