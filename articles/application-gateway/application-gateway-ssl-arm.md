---
title: Application Gateway konfigurálása SSL-alapú kiszervezéshez az Azure Resource Manager használatával | Microsoft Docs
description: Ez az oldal utasításokat tartalmaz egy SSL-alapú kiszervezéssel rendelkező Application Gateway létrehozásához az Azure Resource Manager használatával
documentationcenter: na
services: application-gateway
author: georgewallace
manager: carmonm
editor: tysonn

ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/09/2016
ms.author: gwallace

---
# Application Gateway konfigurálása SSL-alapú kiszervezéshez az Azure Resource Manager használatával
> [!div class="op_single_selector"]
> -[Azure Portal](application-gateway-ssl-portal.md)
> -[Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)
> -[Klasszikus Azure PowerShell](application-gateway-ssl.md)
> 
> 

 Az Azure Application Gateway konfigurálható úgy, hogy leállítsa a Secure Sockets Layer (SSL) munkamenetét az átjárónál, így elkerülhetők a költséges SSL visszafejtési feladatok a webfarmon. Az SSL-alapú kiszervezés emellett leegyszerűsíti az előtér-kiszolgáló számára webalkalmazás telepítését és kezelését.

## Előkészületek
1. Telepítse az Azure PowerShell-parancsmagok legújabb verzióját a Webplatform-telepítővel. A [Letöltések lap](https://azure.microsoft.com/downloads/) **Windows PowerShell** szakaszából letöltheti és telepítheti a legújabb verziót.
2. Létre kell hozni egy virtuális hálózatot és alhálózatot az Application Gateway számára. Győződjön meg arról, hogy egy virtuális gép vagy felhőalapú telepítés sem használja az alhálózatot. Az Application Gateway-nek egyedül kell lennie a virtuális hálózat alhálózatán.
3. A kiszolgálóknak, amelyeket az Application Gateway használatára konfigurál, már létezniük kell, illetve a virtuális hálózatban vagy hozzárendelt nyilvános/virtuális IP-címmel létrehozott végpontokkal kell rendelkezniük.

## Mire van szükség egy Application Gateway létrehozásához?
* **Háttér-kiszolgálókészlet:** A háttérkiszolgálók IP-címeinek listája. A listán szereplő IP-címeknek a virtuális hálózat alhálózatához kell tartozniuk, vagy nyilvános/virtuális IP-címnek kell lenniük.
* **Háttér-kiszolgálókészlet beállításai:** Minden készletnek vannak beállításai, például port, protokoll vagy cookie-alapú affinitás. Ezek a beállítások egy adott készlethez kapcsolódnak, és a készlet minden kiszolgálójára érvényesek.
* **Előtérbeli port:** Az Application Gateway-en megnyitott nyilvános port. Amikor a forgalom eléri ezt a portot, a port átirányítja az egyik háttérkiszolgálóra.
* **Figyelő:** A figyelő egy előtérbeli porttal, egy protokollal (Http vagy Https, ezek kis- és nagybetűt megkülönböztető beállítások) és SSL tanúsítványnévvel rendelkezik.
* **Szabály:** A szabály összeköti a figyelőt és a háttérkiszolgáló-készletet, és meghatározza, hogy mely háttérkiszolgáló-készletre legyen átirányítva a forgalom, ha elér egy adott figyelőt. Jelenleg csak a *basic* szabály támogatott. A *basic* szabály a ciklikus időszeleteléses terheléselosztás.

**További konfigurációs megjegyzések**

Az SSL-tanúsítványok konfigurálásához *Https*-re kell módosítani a **HttpListener** protokollját (megkülönböztetve a kis- és nagybetűket). Az **SslCertificate** elemet hozzá kell adni a **HttpListener** elemhez az SSL-tanúsítvány számára konfigurált változóértékkel. Az előtérbeli portot frissíteni kell 443-ra.

**A cookie-alapú affinitás engedélyezése**: Egy Application Gateway konfigurálható úgy, hogy egy ügyfélmunkamenetből érkező kérelmet mindig a webfarm ugyanazon virtuális gépére irányítsa. Ez a forgatókönyv egy munkameneti cookie beszúrásával lesz végrehajtva, amely lehetővé teszi az átjáró számára a forgalom megfelelő irányítását. A cookie-alapú affinitás engedélyezéséhez a **CookieBasedAffinity** paraméter beállítása legyen *Enabled* a **BackendHttpSettings** elemen belül.

## Application Gateway létrehozása
A klasszikus Azure üzembe helyezési modell és az Azure Resource Manager használata abban tér el egymástól, hogy más sorrendben kell létrehoznia az Application Gateway-t és a konfigurálást igénylő elemeket.

A Resource Managerrel az Application Gateway összes összetevőjét külön kell konfigurálni, és csak utána kell őket összeállítani az Application Gateway-erőforrás létrehozásához.

Egy Application Gateway létrehozásához a következő lépéseket kell végrehajtania:

1. Erőforráscsoport létrehozása a Resource Managerhez
2. Virtuális hálózat, alhálózat és nyilvános IP-cím létrehozása az Application Gateway számára
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
Hozzon létre egy erőforráscsoportot (hagyja ki ezt a lépést, ha egy meglévő erőforráscsoportot használ).

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

Az Azure Resource Manager megköveteli, hogy minden erőforráscsoport megadjon egy helyet. Ez a beállítás szolgál az erőforráscsoport erőforrásainak alapértelmezett helyeként. Győződjön meg arról, hogy az Application Gateway létrehozására irányuló összes parancs ugyanazt az erőforráscsoportot használja.

A fenti példában létrehoztunk egy „appgw-RG” nevű erőforráscsoportot, amelynek a helye az USA nyugati régiója, „West US”.

## Virtuális hálózat és alhálózat létrehozása az Application Gateway számára
Az alábbi példa bemutatja, hogyan hozhat létre egy virtuális hálózatot a Resource Manager használatával:

### 1. lépés
    $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

Ez a példa hozzárendeli a 10.0.0.0/24 címtartományt a virtuális hálózat létrehozásához használni kívánt egyik alhálózati változóhoz.

### 2. lépés
    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

Ez létrehoz egy „appgwvnet” nevű virtuális hálózatot az „appgw-rg” erőforráscsoportban az USA nyugati régiója számára, amely a 10.0.0.0/16 előtagot használja a 10.0.0.0/24 alhálózattal.

### 3. lépés
    $subnet = $vnet.Subnets[0]

Ez a példa hozzárendeli az alhálózati objektumot a $subnet változóhoz a következő lépésekhez.

## Nyilvános IP-cím létrehozása az előtérbeli konfigurációhoz
    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic

Ez a példa egy „publicIP01” nevű, nyilvános IP-címhez tartozó erőforrást hoz létre az „appgw-rg” nevű erőforráscsoportban az USA nyugati régiója számára.

## Hozzon létre egy Application Gateway konfigurációs objektumot
### 1. lépés
    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

Ez a példa egy „gatewayIP01” nevű Application Gateway IP-konfigurációt hoz létre. Amikor az Application Gateway elindul, a konfigurált alhálózatból felvesz egy IP-címet, és a hálózati forgalmat a háttérbeli IP-készlet IP-címeihez irányítja. Ne feledje, hogy minden példány egy IP-címet vesz fel.

### 2. lépés
    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

Ez a példa konfigurálja a „pool01” nevű háttérbeli IP-címkészletet az alábbi IP-címekkel: „134.170.185.46, 134.170.188.221, 134.170.185.50”. Ezek az értékek azok az IP-címek, amelyek fogadják majd az előtérbeli IP-végpontból érkező hálózati forgalmat. Cserélje le az előző példában szereplő IP-címeket a webalkalmazás végpontjainak IP-címeire.

### 3. lépés
    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Enabled

Ez a „poolsetting01” nevű Application Gateway-beállítást konfigurálja a háttérkészlet elosztott terhelésű hálózati forgalmához.

### 4. lépés
    $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 443

Ez a példa konfigurálja a „frontendport01” nevű előtérbeli IP-portot a nyilvános IP-címvégponthoz.

### 5. lépés
    $cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path for certificate file> -Password ‘<password>’

Ez a példa az SSL-kapcsolathoz használt tanúsítványt konfigurálja. A tanúsítványnak .pfx formátumúnak, a jelszónak pedig 4 és 12 karakter közötti hosszúságúnak kell lennie.

### 6. lépés
    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

Ez létrehozza a „fipconfig01” nevű előtérbeli IP-konfigurációt, és társítja a nyilvános IP-címet az előtérbeli IP-konfigurációhoz.

### 7. lépés
    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert


Ez létrehozza a „listener01” nevű figyelőt, és társítja az előtérbeli portot az előtérbeli IP-konfigurációhoz és tanúsítványhoz.

### 8. lépés
    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

Ez létrehozza a „rule01” nevű terheléselosztási útválasztási szabályt, amely a terheléselosztó viselkedését konfigurálja.

### 9. lépés
    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

Ez az Application Gateway példányméretét konfigurálja.

> [!NOTE]
> Az *InstanceCount* alapértelmezett értéke 2, a maximális értéke pedig 10. A *GatewaySize* alapértelmezett értéke Közepes. A Standard_Small, Standard_Medium és a Standard_Large lehetőségek közül választhat.
> 
> 

## Application Gateway létrehozása a New-AzureApplicationGateway használatával
    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslCertificates $cert

Ez létrehoz egy Application Gateway-t az előző lépések konfigurációs elemeivel. Ebben a példában az Application Gateway neve „appgwtest”.

## Következő lépések
Ha konfigurálni szeretne egy ILB-vel használni kívánt Application Gateway-t: [Application Gateway létrehozása belső terheléselosztóval (ILB)](application-gateway-ilb.md).

Ha további általános információra van szüksége a terheléselosztás beállításaival kapcsolatban:

* [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!--HONumber=Sep16_HO4-->


