<properties
   pageTitle="Application Gateway létrehozása, indítása vagy törlése az Azure Resource Manager használatával | Microsoft Azure"
   description="Ez a lap utasításokat tartalmaz egy Azure Application Gateway Azure Resource Manager segítségével történő létrehozásához, konfigurálásához, indításához és törléséhez"
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
   ms.date="08/09/2016"
   ms.author="gwallace"/>


# Application Gateway létrehozása, indítása vagy törlése az Azure Resource Manager használatával

Az Azure Application Gateway egy 7. rétegbeli terheléselosztó. Feladatátvételt és teljesítményalapú útválasztást biztosít a HTTP-kérelmek számára különböző kiszolgálók között, függetlenül attól, hogy a felhőben vagy a helyszínen vannak. Az Application Gateway az alábbi alkalmazáskézbesítési funkciókkal rendelkezik: HTTP-terheléselosztás, cookie-alapú munkamenet-affinitás és Secure Sockets Layer (SSL) alapú kiszervezés.


> [AZURE.SELECTOR]
- [Azure Portal](application-gateway-create-gateway-portal.md)
- [Azure Resource Manager PowerShell](application-gateway-create-gateway-arm.md)
- [Klasszikus Azure PowerShell](application-gateway-create-gateway.md)
- [Azure Resource Manager-sablon](application-gateway-create-gateway-arm-template.md)
- [Azure CLI](application-gateway-create-gateway-cli.md)

<BR>


Ez a cikk részletesen ismerteti a lépéseket, amelyekkel létrehozhat, konfigurálhat, elindíthat és törölhet egy Application Gateway-t.


>[AZURE.IMPORTANT] Az Azure-erőforrásokkal való munka megkezdése előtt fontos megérteni, hogy az Azure jelenleg két üzembe helyezési modellel rendelkezik, a Resource Managerrel és a klasszikussal. Bizonyosodjon meg arról, hogy megfelelő ismeretekkel rendelkezik az [üzembe helyezési modellekről és eszközökről](../azure-classic-rm.md), mielőtt elkezdene dolgozni az Azure-erőforrásokkal. A különféle eszközök dokumentációit a cikk tetején található fülekre kattintva tekintheti meg. Jelen dokumentum az Application Gateway az Azure Resource Manager használatával történő létrehozását ismerteti. A klasszikus verzió használatához lépjen az [Application Gateway klasszikus üzembe helyezéssel történő létrehozása a PowerShell használatával](application-gateway-create-gateway.md) című szakaszra.



## Előkészületek

1. Telepítse az Azure PowerShell-parancsmagok legújabb verzióját a Webplatform-telepítővel. A [Letöltések lap](https://azure.microsoft.com/downloads/) **Windows PowerShell** szakaszából letöltheti és telepítheti a legújabb verziót.
2. Ha rendelkezik meglévő virtuális hálózattal, válasszon ki egy meglévő üres alhálózatot, vagy hozzon létre egyet a meglévő virtuális hálózatban kizárólag az alkalmazásátjáró számára. Nem telepítheti az alkalmazásátjárót egy másik virtuális hálózatra, csak amelyikre az erőforrást szeretné telepíteni az alkalmazásátjáró mögött. 
3. A kiszolgálóknak, amelyeket az Application Gateway használatára konfigurál, már létezniük kell, illetve a virtuális hálózatban vagy hozzárendelt nyilvános/virtuális IP-címmel létrehozott végpontokkal kell rendelkezniük.

## Mire van szükség egy Application Gateway létrehozásához?


- **Háttér-kiszolgálókészlet:** A háttérkiszolgálók IP-címeinek listája. A listán szereplő IP-címeknek a virtuális hálózat alhálózatához kell tartozniuk, vagy nyilvános/virtuális IP-címnek kell lenniük.
- **Háttér-kiszolgálókészlet beállításai:** Minden készletnek vannak beállításai, például port, protokoll vagy cookie-alapú affinitás. Ezek a beállítások egy adott készlethez kapcsolódnak, és a készlet minden kiszolgálójára érvényesek.
- **Előtérbeli port:** Az Application Gateway-en megnyitott nyilvános port. Amikor a forgalom eléri ezt a portot, a port átirányítja az egyik háttérkiszolgálóra.
- **Figyelő:** A figyelő egy előtérbeli porttal, egy protokollal (Http vagy Https, a kis- és a nagybetűk megkülönböztetésével) és SSL tanúsítványnévvel rendelkezik (SSL-kiszervezés konfigurálásakor).
- **Szabály:** A szabály köti a figyelőt és a háttérkiszolgáló-készletet, és meghatározza, hogy melyik háttérkiszolgáló-készletre legyen átirányítva a forgalom, ha elér egy adott figyelőt. 



## Application Gateway létrehozása

A klasszikus Azure és az Azure Resource Manager használata abban tér el egymástól, hogy más sorrendben kell létrehoznia az Application Gateway-t és a konfigurálást igénylő elemeket.

A Resource Managerrel az Application Gateway összes alkotóelemét külön kell konfigurálni, és csak utána kell őket összeállítani az Application Gateway-erőforrás létrehozásához.


A következő lépések szükségesek egy Application Gateway létrehozásához.

## Erőforráscsoport létrehozása a Resource Managerhez

Ügyeljen arra, hogy az Azure PowerShell legújabb verzióját használja. További információ: [A Windows PowerShell használata a Resource Managerrel](../powershell-azure-resource-manager.md).

### 1. lépés
Jelentkezzen be az Azure-ba a Login-AzureRmAccount paranccsal

A rendszer kérni fogja a hitelesítő adatokkal történő hitelesítést.<BR>
### 2. lépés
Keresse meg a fiókot az előfizetésekben.

        Get-AzureRmSubscription

### 3. lépés
Válassza ki, hogy melyik Azure előfizetést fogja használni. <BR>

        Select-AzureRmSubscription -Subscriptionid "GUID of subscription"

### 4. lépés
Hozzon létre egy új erőforráscsoportot (hagyja ki ezt a lépést, ha egy meglévő erőforráscsoportot használ).

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

Az Azure Resource Manager megköveteli, hogy minden erőforráscsoport megadjon egy helyet. Ez a hely lesz az erőforráscsoport erőforrásainak alapértelmezett helye. Győződjön meg arról, hogy az Application Gateway létrehozására irányuló összes parancs ugyanazt az erőforráscsoportot használja.

A fenti példában létrehoztunk egy „appgw-RG” nevű erőforráscsoportot, amelynek a helye az USA nyugati régiója, „West US”.

>[AZURE.NOTE] Ha egy egyéni mintát kell konfigurálnia az Application Gateway számára: [Application Gateway létrehozása egyéni mintákkal a PowerShell használatával](application-gateway-create-probe-ps.md). További információért lásd: [egyéni minták és állapotfigyelés](application-gateway-probe-overview.md).



## Virtuális hálózat és alhálózat létrehozása az Application Gateway számára

Az alábbi példa bemutatja, hogyan hozhat létre virtuális hálózatot a Resource Manager használatával.

### 1. lépés

Rendelje hozzá a 10.0.0.0/24 címtartományt a virtuális hálózat létrehozásához használni kívánt alhálózati változóhoz.

    $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24


### 2. lépés

Hozzon létre egy „appgwvnet” nevű virtuális hálózatot az „appgw-rg” erőforráscsoportban az USA nyugati régiója számára, amely a 10.0.0.0/16 előtagot használja a 10.0.0.0/24 alhálózattal.

    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### 3. lépés

Rendeljen hozzá egy alhálózati változót a következő lépésekhez, amelyek egy Application Gateway-t hoznak létre.

    $subnet=$vnet.Subnets[0]

## Nyilvános IP-cím létrehozása az előtérbeli konfigurációhoz

Hozzon létre egy „publicIP01” nevű, nyilvános IP-címhez tartozó erőforrást az „appgw-rg” nevű erőforráscsoportban az USA nyugati régiója számára.

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic


## Hozzon létre egy Application Gateway konfigurációs objektumot

Az Application Gateway létrehozása előtt minden konfigurációs elemet be kell állítania. Az alábbi lépések létrehozzák az Application Gateway erőforráshoz szükséges konfigurációs elemeket.

### 1. lépés

Hozzon létre egy „gatewayIP01” nevű Application Gateway IP-konfigurációt. Amikor az Application Gateway elindul, a konfigurált alhálózatból vesz fel egy IP-címet, és a hálózati forgalmat a háttérbeli IP-készlet IP-címeihez irányítja. Ne feledje, hogy minden példány elfoglal egy IP-címet.


    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet


### 2. lépés

Konfigurálja a „pool01” nevű háttérbeli IP-cím készletet az alábbi IP-címekkel: „134.170.185.46, 134.170.188.221,134.170.185.50”. Ezek az IP-címek fogadják majd az előtérbeli IP-cím végpontból érkező hálózati forgalmat. Az előző IP-címeket lecseréli a saját alkalmazása IP-címvégpontjaira.

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50



### 3. lépés

Konfigurálja a „poolsetting01” nevű Application Gateway-beállítást a háttérkészlet elosztott terhelésű hálózati forgalmához.

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled


### 4. lépés

Konfigurálja a „frontendport01” nevű előtérbeli IP-portot a nyilvános IP-cím végponthoz.

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

### 5. lépés

Hozza létre a „fipconfig01” nevű előtérbeli IP-konfigurációt, és társítsa a nyilvános IP-címet az előtérbeli IP-konfigurációhoz.

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip


### 6. lépés

Hozza létre a „listener01” nevű figyelőt, és társítsa az előtérbeli portot az előtérbeli IP-konfigurációhoz.

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

### 7. lépés

Hozza létre a „rule01” nevű terheléselosztási útválasztási szabályt, amely a terheléselosztó viselkedését konfigurálja.

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### 8. lépés

Konfigurálja az Application Gateway példányméretét.

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

>[AZURE.NOTE]  Az *InstanceCount* alapértelmezett értéke 2, a maximális értéke pedig 10. A *GatewaySize* alapértelmezett értéke Közepes. A Standard_Small, Standard_Medium és a Standard_Large lehetőségek közül választhat.

## Application Gateway létrehozása a New-AzureRmApplicationGateway paranccsal

Hozzon létre egy Application Gatewayt az előző lépések konfigurációs elemeivel. Ebben a példában az Application Gateway neve „appgwtest”.

    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

### 9. lépés
Kérje le az alkalmazásátjáró DNS-hez és virtuális IP-címhez kapcsolódó adatait az alkalmazásátjáróhoz csatolt nyilvános IP-erőforrásból.

    Get-AzureRmPublicIpAddress -Name publicIP01 -ResourceGroupName appgw-rg  

    Name                     : publicIP01
    ResourceGroupName        : appgwtest 
    Location                 : westus
    Id                       : /subscriptions/<sub_id>/resourceGroups/appgw-rg/providers/Microsoft.Network/publicIPAddresses/publicIP01
    Etag                     : W/"12302060-78d6-4a33-942b-a494d6323767"
    ResourceGuid             : ee9gd76a-3gf6-4236-aca4-gc1f4gf14171
    ProvisioningState        : Succeeded
    Tags                     : 
    PublicIpAllocationMethod : Dynamic
    IpAddress                : 137.116.26.16
    IdleTimeoutInMinutes     : 4
    IpConfiguration          : {
                                 "Id": "/subscriptions/<sub_id>/resourceGroups/appgw-rg/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIPConfigurations/fipconfig01"
                               }
    DnsSettings              : {
                                 "Fqdn": "ee7aca47-4344-4810-a999-2c631b73e3cd.cloudapp.net"
                               } 



## Application Gateway törlése

Egy Application Gateway törléséhez kövesse az alábbi lépéseket:

### 1. lépés

Társítsa az Application Gateway objektumot a „$getgw” változóhoz.

    $getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### 2. lépés

Állítsa le az Application Gateway-t a **Stop-AzureRmApplicationGateway** parancsmaggal.

    Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  


Amint az Application Gateway leállított állapotba kerül, távolítsa el a szolgáltatást a **Remove-AzureRmApplicationGateway** parancsmaggal.


    Remove-AzureRmApplicationGateway -Name $appgwtest -ResourceGroupName appgw-rg -Force



>[AZURE.NOTE] A **-force** kapcsolóval le lehet tiltani az eltávolítás jóváhagyása üzenetet.


A szolgáltatás eltávolításának ellenőrzéséhez használhatja a **Get-AzureRmApplicationGateway** parancsmagot. Ez a lépés nem kötelező.


    Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg


## Következő lépések

Ha SSL-alapú kiszervezést szeretne konfigurálni: [Application Gateway konfigurálása SSL-alapú kiszervezéshez](application-gateway-ssl.md).

Ha konfigurálni szeretne egy ILB-vel használni kívánt Application Gateway-t: [Application Gateway létrehozása belső terheléselosztóval (ILB)](application-gateway-ilb.md).

Ha további általános információra van szüksége a terheléselosztás beállításaival kapcsolatban:

- [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)



<!--HONumber=sep16_HO1-->


