---
title: "Azure Application Gateway létrehozása és kezelése – PowerShell | Microsoft Docs"
description: "Ez a lap utasításokat tartalmaz egy Azure Application Gateway Azure Resource Manager segítségével történő létrehozásához, konfigurálásához, indításához és törléséhez"
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 866e9b5f-0222-4b6a-a95f-77bc3d31d17b
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/04/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 141270c353d3fe7341dfad890162ed74495d48ac
ms.openlocfilehash: 6d38dd6802a25b147fd014b4d26ca432ca87a07d
ms.contentlocale: hu-hu
ms.lasthandoff: 07/25/2017

---
# <a name="create-start-or-delete-an-application-gateway-by-using-azure-resource-manager"></a>Application Gateway létrehozása, indítása vagy törlése az Azure Resource Manager használatával

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-gateway-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-gateway-arm.md)
> * [Klasszikus Azure PowerShell](application-gateway-create-gateway.md)
> * [Azure Resource Manager-sablon](application-gateway-create-gateway-arm-template.md)
> * [Azure CLI](application-gateway-create-gateway-cli.md)

Az Azure Application Gateway egy 7. rétegbeli terheléselosztó. Feladatátvételt és teljesítményalapú útválasztást biztosít a HTTP-kérelmek számára különböző kiszolgálók között, függetlenül attól, hogy a felhőben vagy a helyszínen vannak.
Az Application Gateway számos alkalmazáskézbesítési vezérlőszolgáltatást (ADC) biztosít, beleértve a HTTP-terheléselosztást, a cookie-alapú munkamenet-affinitást, a Secure Sockets Layer (SSL) alapú kiszervezést, az egyéni állapotteszteket, a többhelyes támogatást és még sok mást.

A támogatott szolgáltatások teljes listájáért látogasson el [Az Application Gateway áttekintése](application-gateway-introduction.md) című oldalra

Ez a cikk részletesen ismerteti a lépéseket, amelyekkel létrehozhat, konfigurálhat, elindíthat és törölhet egy Application Gateway-t.

> [!IMPORTANT]
> Az Azure-erőforrásokkal való munka megkezdése előtt fontos megérteni, hogy az Azure jelenleg két üzembe helyezési modellel rendelkezik, a Resource Managerrel és a klasszikussal. Bizonyosodjon meg arról, hogy megfelelő ismeretekkel rendelkezik az [üzembe helyezési modellekről és eszközökről](../azure-classic-rm.md), mielőtt elkezdene dolgozni az Azure-erőforrásokkal. A különféle eszközök dokumentációit a cikk tetején található fülekre kattintva tekintheti meg. Jelen dokumentum az Application Gateway az Azure Resource Manager használatával történő létrehozását ismerteti. A klasszikus verzió használatához lépjen az [Application Gateway klasszikus üzembe helyezéssel történő létrehozása a PowerShell használatával](application-gateway-create-gateway.md) című szakaszra.

## <a name="before-you-begin"></a>Előkészületek

1. Telepítse az Azure PowerShell-parancsmagok legújabb verzióját a Webplatform-telepítővel. A [Letöltések lap](https://azure.microsoft.com/downloads/) **Windows PowerShell** szakaszából letöltheti és telepítheti a legújabb verziót.
1. Ha rendelkezik meglévő virtuális hálózattal, válasszon ki egy meglévő üres alhálózatot, vagy hozzon létre egyet a meglévő virtuális hálózatban kizárólag az alkalmazásátjáró számára. Nem telepítheti az alkalmazásátjárót egy másik virtuális hálózatra, csak amelyikre az erőforrást szeretné telepíteni az alkalmazásátjáró mögött.
1. A kiszolgálóknak, amelyeket az Application Gateway használatára konfigurál, már létezniük kell, illetve a virtuális hálózatban vagy hozzárendelt nyilvános/virtuális IP-címmel létrehozott végpontokkal kell rendelkezniük.

## <a name="what-is-required-to-create-an-application-gateway"></a>Mire van szükség egy Application Gateway létrehozásához?

* **Háttér-kiszolgálókészlet:** A háttérkiszolgálók IP-címeinek, teljes tartományneveinek vagy hálózati adaptereinek listája. Ha IP-címeket használ, akkor vagy a virtuális hálózat alhálózatához kell tartozniuk, vagy nyilvános/virtuális IP-címnek kell lenniük.
* **Háttér-kiszolgálókészlet beállításai:** Minden készletnek vannak beállításai, például port, protokoll vagy cookie-alapú affinitás. Ezek a beállítások egy adott készlethez kapcsolódnak, és a készlet minden kiszolgálójára érvényesek.
* **Előtérbeli port:** Az Application Gateway-en megnyitott nyilvános port. Amikor a forgalom eléri ezt a portot, a port átirányítja az egyik háttérkiszolgálóra.
* **Figyelő:** A figyelő egy előtérbeli porttal, egy protokollal (Http vagy Https, a kis- és a nagybetűk megkülönböztetésével) és SSL tanúsítványnévvel rendelkezik (SSL-kiszervezés konfigurálásakor).
* **Szabály:** A szabály köti a figyelőt és a háttérkiszolgáló-készletet, és meghatározza, hogy melyik háttérkiszolgáló-készletre legyen átirányítva a forgalom, ha elér egy adott figyelőt.

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

A klasszikus Azure és az Azure Resource Manager használata abban tér el egymástól, hogy más sorrendben kell létrehoznia az Application Gateway-t és a konfigurálást igénylő elemeket.

A Resource Managerrel az Application Gateway összes alkotóelemét külön kell konfigurálni, és csak utána kell őket összeállítani az Application Gateway-erőforrás létrehozásához.

Az Application Gateway létrehozásához az alábbi lépések szükségesek.

## <a name="create-a-resource-group-for-resource-manager"></a>Erőforráscsoport létrehozása a Resource Managerhez

Ügyeljen arra, hogy az Azure PowerShell legújabb verzióját használja. További információ: [A Windows PowerShell használata a Resource Managerrel](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>1. lépés

Jelentkezzen be az Azure-ba

```powershell
Login-AzureRmAccount
```

A rendszer kérni fogja a hitelesítő adatokkal történő hitelesítést.

### <a name="step-2"></a>2. lépés

Keresse meg a fiókot az előfizetésekben.

```powershell
Get-AzureRmSubscription
```

### <a name="step-3"></a>3. lépés

Válassza ki, hogy melyik Azure előfizetést fogja használni.

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>4. lépés

Hozzon létre egy erőforráscsoportot (hagyja ki ezt a lépést, ha egy meglévő erőforráscsoportot használ).

```powershell
New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
```

Az Azure Resource Manager megköveteli, hogy minden erőforráscsoport megadjon egy helyet. Ez a hely lesz az erőforráscsoport erőforrásainak alapértelmezett helye. Győződjön meg arról, hogy az Application Gateway létrehozására irányuló összes parancs ugyanazt az erőforráscsoportot használja.

A fenti példában létrehoztunk egy **appgw-RG** nevű erőforráscsoportot, amelynek a helye az USA nyugati régiója (**West US**).

> [!NOTE]
> Ha egy egyéni mintát kell konfigurálnia az Application Gateway számára: [Application Gateway létrehozása egyéni mintákkal a PowerShell használatával](application-gateway-create-probe-ps.md). További információért lásd: [egyéni minták és állapotfigyelés](application-gateway-probe-overview.md).

## <a name="create-a-virtual-network-and-a-subnet"></a>Hozzon létre egy virtuális hálózatot és egy alhálózatot

Az alábbi példa bemutatja, hogyan hozhat létre virtuális hálózatot a Resource Manager használatával. Ebben a példában egy VNetet hozunk létre az Application Gatewayhez. Az Application Gatewayhez külön alhálózat szükséges, és ebből az okból az Application Gateway számára létrehozott alhálózat kisebb, mint a VNet címtere. A kisebb alhálózat használatával más erőforrások, például webkiszolgálók is konfigurálhatók ugyanazon a VNeten.

### <a name="step-1"></a>1. lépés

Rendelje hozzá a 10.0.0.0/24 címtartományt a virtuális hálózat létrehozásához használni kívánt alhálózati változóhoz. Ebben a lépésben egy alhálózatkonfigurációs objektumot hozunk létre az Application Gatewayhez, amelyet majd a következő példában használunk fel.

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

### <a name="step-2"></a>2. lépés

Hozzon létre egy **appgwvnet** nevű virtuális hálózatot az **appgw-rg** erőforráscsoportban az USA nyugati régiója számára, amely a 10.0.0.0/16 előtagot használja a 10.0.0.0/24 alhálózattal. Ebben a lépésben befejezzük a VNet konfigurálását egyetlen alhálózattal, ahol az Application Gateway fut majd.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
```

### <a name="step-3"></a>3. lépés

Rendelje hozzá az alhálózati változót a következő lépésekhez. A `New-AzureRMApplicationGateway` parancsmag egy későbbi lépésben megörökli a változót.

```powershell
$subnet=$vnet.Subnets[0]
```

## <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet

Hozzon létre egy **publicIP01** nevű, nyilvános IP-címhez tartozó erőforrást az **appgw-rg** nevű erőforráscsoportban, az USA nyugati régiójában. Az Application Gateway nyilvános vagy belső IP-címet, vagy akár mindkettőt is használhatja a terheléselosztási kérések fogadására.  Ebben a példában csak nyilvános IP-címet használunk. A következő példában nem konfigurálunk DNS-nevet a nyilvános IP-cím létrehozásához.  Az Application Gateway nem támogatja az egyéni DNS-neveket a nyilvános IP-címeken.  Ha egyéni név szükséges a nyilvános végponthoz, egy CNAME rekordot kell létrehozni, amely a nyilvános IP-cím automatikusan létrehozott DNS-nevére mutat.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

> [!NOTE]
> Amikor a szolgáltatás elindul, egy IP-cím lesz kiosztva az Application Gatewaynek.

## <a name="create-the-application-gateway-configuration-objects"></a>Hozza létre az Application Gateway konfigurációs objektumokat

Az Application Gateway létrehozása előtt minden konfigurációs elemet be kell állítani. Az alábbi lépések létrehozzák az Application Gateway erőforráshoz szükséges konfigurációs elemeket.

### <a name="step-1"></a>1. lépés

Hozzon létre egy **gatewayIP01** nevű Application Gateway IP-konfigurációt. Amikor az Application Gateway elindul, a konfigurált alhálózatból felvesz egy IP-címet, és a hálózati forgalmat a háttérbeli IP-készlet IP-címeihez irányítja. Ne feledje, hogy minden példány egy IP-címet vesz fel.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

### <a name="step-2"></a>2. lépés

Konfigurálja a **pool01** nevű háttérbeli IP-cím készletet a **pool1** IP-címeivel. Ezek az IP-címek az Application Gateway használatával védeni kívánt webalkalmazást futtató erőforrások IP-címei. A rendszer mintavételekkel (alapszintű vagy egyéni mintavételekkel) ellenőrzi, hogy a háttérkészlet érintett tagjai megfelelő állapotban vannak-e.  Ezek után a rendszer hozzájuk irányítja a forgalmat, amikor kérések érkeznek az Application Gatewayre. A háttérkészleteket az Application Gateway több szabálya is használhatja, ami azt jelenti, hogy egy háttérkészlet több webalkalmazáshoz is használható ugyanazon a gazdagépen.

```powershell
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221, 134.170.185.50
```

Ebben a példában két háttérkészlet végzi a hálózati forgalom irányítását az URL-cím alapján. Az egyik készlet a „/video” URL-útvonalról, a másik az „/image” útvonalról fogadja a forgalmat. Az előző IP-címeket lecseréli a saját alkalmazása IP-címvégpontjaira.

### <a name="step-3"></a>3. lépés

Konfigurálja a **poolsetting** nevű Application Gateway-beállítást a háttérkészlet elosztott terhelésű hálózati forgalmához. Mindegyik háttérkészlet saját háttérkészlet-beállításokkal rendelkezhet.  A szabályok a háttérbeli HTTP-beállítások használatával irányítják a forgalmat a háttérkészlet megfelelő tagjaira. A háttérbeli HTTP-beállítások határozzák meg a forgalom háttérkészlet-tagokra való küldése során használt protokollt és portot. A cookie-alapú munkameneteket szintén a háttérbeli HTTP-beállítások határozzák meg.  Ha engedélyezve van, a cookie-alapú munkamenet-affinitás az egyes csomagokhoz tartozó korábbi kéréseknek megfelelő háttérrendszerekre irányítja a forgalmat.

```powershell
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120
```

### <a name="step-4"></a>4. lépés

Konfigurálja az előtérbeli portot egy Application Gatewayhez. Az előtérbeli port konfigurációs objektumával egy figyelő meghatározza, melyik porton figyeli a forgalmat az Application Gateway a figyelőn.

```powershell
$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80
```

### <a name="step-5"></a>5. lépés

Konfigurálja az előtérbeli IP-portot egy nyilvános IP-címvégponttal. Az előtérbeli IP-konfigurációs objektumot egy figyelő használja arra, hogy a kifelé néző IP-címet a figyelőhöz rendelje.

```powershell
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip
```

### <a name="step-6"></a>6. lépés

Konfigurálja a figyelőt. Ebben a lépésben konfiguráljuk a figyelőt a bejövő hálózati forgalmat fogadó nyilvános IP-címhez és porthoz. A következő példában az előzőekben konfigurált előtérbeli IP-konfiguráció, előtérbeli portkonfiguráció, valamint egy protokoll (http vagy https) használatával konfiguráljuk a figyelőt. A példában a figyelő a 80-as porton figyeli a HTTP-forgalmat a korábban létrehozott nyilvános IP-címen.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp
```

### <a name="step-7"></a>7. lépés

Hozza létre a **rule01** nevű terheléselosztási útválasztási szabályt, amely a terheléselosztó viselkedését konfigurálja. A szabályt az előző lépésekben létrehozott háttérkészlet-beállítások, figyelő és háttérkészlet alkotják. A forgalom a meghatározott feltételek alapján a megfelelő háttérrendszerre lesz irányítva.

```powershell
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

### <a name="step-8"></a>8. lépés

Konfigurálja az Application Gatewayben a példányok számát és a méretet.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
```

> [!NOTE]
> Az **InstanceCount** alapértelmezett értéke 2, a maximális értéke pedig 10. A **GatewaySize** alapértelmezett értéke Közepes. A **Standard_Small**, **Standard_Medium** és a **Standard_Large** lehetőségek közül választhat.

## <a name="create-the-application-gateway"></a>Application Gateway létrehozása

Hozzon létre egy Application Gatewayt az előző lépések konfigurációs elemeivel. Ebben a példában az Application Gateway neve: **appgwtest**.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

Kérje le az alkalmazásátjáró DNS-hez és virtuális IP-címhez kapcsolódó adatait az alkalmazásátjáróhoz csatolt nyilvános IP-erőforrásból.

```powershell
Get-AzureRmPublicIpAddress -Name publicIP01 -ResourceGroupName appgw-rg  
```

## <a name="delete-the-application-gateway"></a>Application Gateway törlése

Egy Application Gateway törléséhez kövesse az alábbi lépéseket:

### <a name="step-1"></a>1. lépés

Társítsa az Application Gateway objektumot a `$getgw` változóhoz.

```powershell
$getgw = Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

### <a name="step-2"></a>2. lépés

Állítsa le az Application Gatewayt a `Stop-AzureRmApplicationGateway` parancsmaggal.

```powershell
Stop-AzureRmApplicationGateway -ApplicationGateway $getgw
```

Amint az Application Gateway leállított állapotba kerül, távolítsa el a szolgáltatást a `Remove-AzureRmApplicationGateway` parancsmaggal.

```powershell
Remove-AzureRmApplicationGateway -Name $appgwtest -ResourceGroupName appgw-rg -Force
```

> [!NOTE]
> A **-force** kapcsolóval le lehet tiltani az eltávolítás jóváhagyása üzenetet.

A szolgáltatás eltávolításának ellenőrzéséhez használhatja a `Get-AzureRmApplicationGateway` parancsmagot. Ez a lépés nem kötelező.

```powershell
Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

## <a name="get-application-gateway-dns-name"></a>Az Application Gateway DNS-nevének beszerzése

Az átjáró létrehozása után a következő lépés a kommunikációra szolgáló előtér konfigurálása. Nyilvános IP-cím esetén az Application Gateway használatához dinamikusan hozzárendelt DNS-névre van szükség, amely nem valódi név. Ha szeretné, hogy a végfelhasználók elérjék az Application Gatewayt, használjon egy Application Gateway nyilvános végpontjára mutató CNAME-rekordot. [Egyéni tartománynév konfigurálása az Azure-ban](../cloud-services/cloud-services-custom-domain-name-portal.md). A dinamikusan létrehozott DNS-név megtalálásához az Application Gateway részleteinek beszerzése és a kapcsolódó IP/DNS-név lekérése szükséges az Application Gatewayhez csatolt PublicIPAddress használatával. Az Application Gateway DNS-nevének használatával létrehozhat egy CNAME rekordot, amely a két webalkalmazást erre a DNS-névre irányítja. Az A-bejegyzések használata nem javasolt, mivel a virtuális IP-cím változhat az Application Gateway újraindításakor.

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

## <a name="delete-all-resources"></a>Az összes erőforrás törlése

A jelen cikkben létrehozott összes erőforrás törléséhez hajtsa végre az alábbi lépéseket:

```powershell
Remove-AzureRmResourceGroup -Name appgw-RG
```

## <a name="next-steps"></a>Következő lépések

Ha SSL-alapú kiszervezést szeretne konfigurálni, tekintse meg a következőt: [Application Gateway konfigurálása SSL-alapú kiszervezéshez](application-gateway-ssl.md).

Ha konfigurálni szeretne egy ILB-vel használni kívánt Application Gateway-t: [Application Gateway létrehozása belső terheléselosztóval (ILB)](application-gateway-ilb.md).

Ha további általános információra van szüksége a terheléselosztás beállításaival kapcsolatban:

* [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)


