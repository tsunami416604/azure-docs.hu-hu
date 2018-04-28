---
title: 'Az Azure VPN Gatewayek beállítani a BGP Protokollt: erőforrás-kezelő: PowerShell |} Microsoft Docs'
description: Ez a cikk bemutatja, hogyan BGP konfigurálása az Azure VPN Gatewayek Azure Resource Manager és a PowerShell használatával.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: 905b11a7-1333-482c-820b-0fd0f44238e5
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/12/2017
ms.author: yushwang
ms.openlocfilehash: fc9337188fd439082c4aa34f0cbebe3eb2da5d99
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="how-to-configure-bgp-on-azure-vpn-gateways-using-powershell"></a>A BGP konfigurálása az Azure VPN Gatewayek PowerShell használatával
Ez a cikk végigvezeti a BGP engedélyezéséhez a létesítmények közötti pont-pont (S2S) VPN-kapcsolat és egy VNet – VNet-kapcsolatot a Resource Manager üzembe helyezési modellben és a PowerShell használatával.

## <a name="about-bgp"></a>A BGP ismertetése
A BGP az interneten gyakran használt szabványos útválasztási protokoll az útválasztási és elérhetőségi információcserére két vagy több hálózat között. A BGP lehetővé teszi, hogy az Azure VPN-átjáróként és BGP-társakat vagy szomszédok, az exchange-"irányítja a", amely tájékoztatja a rendelkezésre állási és az adott előtagok haladhat végig az átjárók és az útválasztók érintett reachability mindkét átjárók a helyszíni VPN-eszközök. A BGP lehetővé teszi a több hálózat közötti tranzit útválasztást azon útvonalak propagálásával az összes többi BGP-társ számára, amelyeket a BGP-átjáró az egyik BGP-társtól vesz át.

Lásd: [áttekintése a BGP az Azure VPN Gatewayek](vpn-gateway-bgp-overview.md) további vitafórum előnyeit a BGP, valamint a műszaki követelményeiben és a BGP használatával kapcsolatos szempontok megismerése.

## <a name="getting-started-with-bgp-on-azure-vpn-gateways"></a>Az Azure VPN gatewayek a BGP-első lépések

Ez a cikk végigvezeti a lépéseit a következő feladatokat:

* [Engedélyezze az Azure VPN gateway a BGP, 1 -. rész](#enablebgp)
* [2. rész – a BGP-létesítmények közötti kapcsolat létrehozása](#crossprembgp)
* [3. rész - BGP VNet – VNet-kapcsolatot létrehozni](#v2vbgp)

Az utasítások részét képező összes a BGP engedélyezve van a hálózati kapcsolatot az alapvető építőelemre képezi. Ha befejezte az összes három részből, a topológia elkészítette a következő ábrán látható módon:

![BGP-topológiákkal](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

Egyesítheti a kijelzők együtt egy összetettebb, Többugrásos, átmenő hálózatról az igényeinek megfelelő létrehozásához.

## <a name ="enablebgp"></a>1. rész – az Azure VPN Gateway a BGP konfigurálása
A konfigurációs lépéseket az Azure VPN gateway a BGP paramétereinek beállítása, a következő ábrán látható módon:

![BGP-átjáró](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Előkészületek
* Győződjön meg arról, hogy rendelkezik Azure-előfizetéssel. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).
* Az Azure Resource Manager PowerShell-parancsmagjainak telepítése. További információ a PowerShell-parancsmagok telepítéséről: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview). 

### <a name="step-1---create-and-configure-vnet1"></a>1. lépés – létrehozása és VNet1 konfigurálása
#### <a name="1-declare-your-variables"></a>1. A változók deklarálása
Ehhez a gyakorlathoz először is deklarálni kell a változókat. Az alábbi példa azt deklarálja a változókat, az értékek a ehhez a gyakorlathoz. Az éles konfigurációhoz ne felejtse el ezeket az értékeket a saját értékeire cserélni. Ezeket a változókat akkor használhatja, ha azért hajtja végre a lépéseket, hogy megismerje ezt a konfigurációtípust. Módosítsa a változókat, majd másolja és illessze be őket a PowerShell-konzolra.

```powershell
$Sub1 = "Replace_With_Your_Subcription_Name"
$RG1 = "TestBGPRG1"
$Location1 = "East US"
$VNetName1 = "TestVNet1"
$FESubName1 = "FrontEnd"
$BESubName1 = "Backend"
$GWSubName1 = "GatewaySubnet"
$VNetPrefix11 = "10.11.0.0/16"
$VNetPrefix12 = "10.12.0.0/16"
$FESubPrefix1 = "10.11.0.0/24"
$BESubPrefix1 = "10.12.0.0/24"
$GWSubPrefix1 = "10.12.255.0/27"
$VNet1ASN = 65010
$DNS1 = "8.8.8.8"
$GWName1 = "VNet1GW"
$GWIPName1 = "VNet1GWIP"
$GWIPconfName1 = "gwipconf1"
$Connection12 = "VNet1toVNet2"
$Connection15 = "VNet1toSite5"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Csatlakozás az előfizetéshez, és hozzon létre egy új erőforráscsoportot
A Resource Manager parancsmagjainak használatához győződjön meg arról, hogy a PowerShell üzemmódra vált. További információ: [A Windows PowerShell használata a Resource Managerrel](../powershell-azure-resource-manager.md).

Nyissa meg a PowerShell konzolt, és csatlakozzon a fiókjához. A következő minta segíthet a kapcsolódásban:

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-testvnet1"></a>3. A TestVNet1 létrehozása
Az alábbi minta létrehoz egy TestVNet1 és három alhálózatok, egy hívott GatewaySubnet, egy hívott előtér és egy hívott háttér nevű virtuális hálózatot. Az értékek behelyettesítésekor fontos, hogy az átjáróalhálózat neve mindenképp GatewaySubnet legyen. Ha ezt másként nevezi el, az átjáró létrehozása meghiúsul.

```powershell
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1 $besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>2. lépés – a VPN-átjáró BGP-paraméterekkel rendelkező TestVNet1 létrehozása
#### <a name="1-create-the-ip-and-subnet-configurations"></a>1. Az IP-cím és alhálózat-konfigurációk létrehozása
Kérje egy nyilvános IP-cím kiosztását a virtuális hálózat számára létrehozni kívánt átjáróhoz. A szükséges alhálózatot és IP-konfigurációk is fogja definiálni.

```powershell
$gwpip1 = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1
```

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. Hozzon létre a VPN-átjáró AS száma
Hozza létre a TestVNet1 virtuális hálózati átjáróját. Útvonalalapú VPN-átjáró, valamint is a hozzáadása paraméter - ASN-t, az az ASN (AS-szám) beállítását TestVNet1 BGP kell rendelkeznie. Ha a ASN paraméter nincs megadva, a ASN 65515 hozzá van rendelve. Az átjáró létrehozása akár 30 percet vagy hosszabb időt is igénybe vehet.

```powershell
New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance -Asn $VNet1ASN
```

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. Az Azure BGP-Társgép IP-cím beszerzése
Az átjáró létrehozása után be kell szereznie az Azure VPN Gateway a BGP társ IP-címe. Ez a cím konfigurálása az Azure VPN Gateway a BGP-partner a helyszíni VPN-eszközök esetében van szükség.

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet1gw.BgpSettingsText
```

Az utolsó parancs a megfelelő BGP-konfigurációk jeleníti meg az Azure VPN Gateway; Példa:

```powershell
$vnet1gw.BgpSettingsText
{
    "Asn": 65010,
    "BgpPeeringAddress": "10.12.255.30",
    "PeerWeight": 0
}
```

Az átjáró létrehozása után a létesítmények közötti vagy a BGP-VNet – VNet-kapcsolatot létesíteni használhatja ezt az átjárót. A következő szakaszok végezze el a szükséges lépések a gyakorlatban.

## <a name ="crossprembbgp"></a>2. rész – a BGP-létesítmények közötti kapcsolat létrehozása

A létesítmények közötti kapcsolatot szüksége a helyszíni VPN-eszköz képviselő helyi hálózati átjáró és a kapcsolatot a VPN-átjárót, csatlakozzon a helyi hálózati átjáró létrehozásához. Nincsenek cikkek, amely ismerteti a fenti lépéseket, amíg ez a cikk a BGP-konfigurációs paraméterek megadása szükséges további tulajdonságait tartalmazza.

![A létesítmények közötti BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)

A továbblépés előtt ellenőrizze, hogy befejeződött [1. rész](#enablebgp) ebben a gyakorlatban.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>1. lépés – létrehozása és a helyi hálózati átjáró konfigurálása

#### <a name="1-declare-your-variables"></a>1. A változók deklarálása

Ebben a gyakorlatban továbbra is a konfiguráció az ábrán is látható. Ne felejtse el az értékeket olyanokra cserélni, amelyeket a saját konfigurációjához kíván használni.

```powershell
$RG5 = "TestBGPRG5"
$Location5 = "East US 2"
$LNGName5 = "Site5"
$LNGPrefix50 = "10.52.255.254/32"
$LNGIP5 = "Your_VPN_Device_IP"
$LNGASN5 = 65050
$BGPPeerIP5 = "10.52.255.254"
```

Több helyi hálózati átjáró paraméterek kapcsolatban ügyeljen a következőkre:

* A helyi hálózati átjáró lehet az ugyanazon vagy másik helyen és az erőforráscsoport a VPN-átjáróként. Ez a példa bemutatja azokat a különböző erőforráscsoportokra különböző helyeken.
* A minimális előtagot kell deklarálni helyi hálózati átjáró pedig a BGP-Társgép IP-cím, a VPN-eszköz állomás címe. Egy /32 ebben az esetben a "10.52.255.254/32" előtaggal.
* Ne feledje a helyszíni hálózatokhoz és az Azure VNet közötti különböző BGP ASN kell használnia. Ha a egyeznek, a virtuális hálózat ASN módosításához, ha a helyszíni VPN-eszköz már használja az ASN a más BGP szomszédok egyenrangú szüksége.

Mielőtt folytatja, győződjön meg arról, hogy továbbra is csatlakozik az 1. előfizetéshez.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. A helyi hálózati átjáró létrehozása Site5

Győződjön meg arról, ha ez nem jön létre, a helyi hálózati átjáró létrehozása előtt az erőforráscsoport létrehozásához. Figyelje meg a helyi hálózati átjáró két további paraméterek: Asn és BgpPeerAddress.

```powershell
New-AzureRmResourceGroup -Name $RG5 -Location $Location5

New-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>2. lépés – a virtuális hálózat átjáró és a helyi hálózati átjáró

#### <a name="1-get-the-two-gateways"></a>1. A két átjáró beolvasása

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5
```

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. A TestVNet1 Site5 kapcsolat létrehozása

Ebben a lépésben hoz létre a kapcsolat TestVNet1 Site5 számára. Adjon meg "-EnableBGP $True" BGP engedélyezése ehhez a kapcsolathoz. A fentiekben taglaltak, akkor lehetséges, hogy az ugyanazon Azure VPN Gateway a BGP- és -BGP kapcsolatokat. A BGP engedélyezve van a connection tulajdonság, ha Azure teszik lehetővé a BGP ehhez a kapcsolathoz annak ellenére, hogy a BGP-paraméterek már be van állítva mindkét átjárókon.

```powershell
New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

A következő példa az ebben a gyakorlatban a helyszíni VPN-eszköz a BGP konfigurációs szakasz kezdenek paramétereket tartalmazza:

```

- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.254
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP    : 10.12.255.30
- Static route         : Add a route for 10.12.255.30/32, with nexthop being the VPN tunnel interface on your device
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

A kapcsolat létrejötte után néhány percet, és a BGP társviszony-létesítési munkamenetet egyszer elindul az IPsec-kapcsolat létrejön.

## <a name ="v2vbgp"></a>3. rész - BGP VNet – VNet-kapcsolatot létrehozni

Ez a szakasz ad hozzá egy VNet – VNet-kapcsolatot, a BGP-vel, az alábbi ábrán látható módon:

![A VNet – VNet BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

Az alábbi utasítások alapján az előző lépésekben továbbra is. Meg kell adnia a [i. rész](#enablebgp) létrehozása és konfigurálása a TestVNet1 és a VPN-átjáró BGP-hez. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>1. lépés – TestVNet2 és a VPN-átjáró létrehozása

Győződjön meg arról, hogy az új virtuális hálózat, TestVNet2, az IP-címtér nem fedi át a virtuális hálózat tartomány egyik fontos.

Ebben a példában a virtuális hálózatok ugyanahhoz az előfizetéshez tartozik. Beállíthatja a VNet – VNet kapcsolatokhoz különböző előfizetések között. További információkért lásd: [VNet – VNet-kapcsolatot konfiguráló](vpn-gateway-vnet-vnet-rm-ps.md). Mindenképpen adja hozzá a "-EnableBgp $True" a BGP engedélyezéséhez kapcsolatok létrehozásakor.

#### <a name="1-declare-your-variables"></a>1. A változók deklarálása

Ne felejtse el az értékeket olyanokra cserélni, amelyeket a saját konfigurációjához kíván használni.

```powershell
$RG2 = "TestBGPRG2"
$Location2 = "West US"
$VNetName2 = "TestVNet2"
$FESubName2 = "FrontEnd"
$BESubName2 = "Backend"
$GWSubName2 = "GatewaySubnet"
$VNetPrefix21 = "10.21.0.0/16"
$VNetPrefix22 = "10.22.0.0/16"
$FESubPrefix2 = "10.21.0.0/24"
$BESubPrefix2 = "10.22.0.0/24"
$GWSubPrefix2 = "10.22.255.0/27"
$VNet2ASN = 65020
$DNS2 = "8.8.8.8"
$GWName2 = "VNet2GW"
$GWIPName2 = "VNet2GWIP"
$GWIPconfName2 = "gwipconf2"
$Connection21 = "VNet2toVNet1"
$Connection12 = "VNet1toVNet2"
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. TestVNet2 az új erőforráscsoport létrehozása

```powershell
New-AzureRmResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2
```

#### <a name="3-create-the-vpn-gateway-for-testvnet2-with-bgp-parameters"></a>3. A VPN-átjáró BGP-paraméterekkel rendelkező TestVNet2 létrehozása

Igényeljen egy osztható ki a Vnethez tartozó létrehoz, és adja meg a szükséges alhálózatot és IP-konfigurációk az átjáró nyilvános IP-címet.

```powershell
$gwpip2    = New-AzureRmPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2     = Get-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gwipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName2 -Subnet $subnet2 -PublicIpAddress $gwpip2
```

Hozzon létre a VPN-átjáró a AS számot. Az Azure VPN gatewayek a felül kell bírálnia az alapértelmezett ASN. Az ASN-eket a csatlakoztatott Vnetek a BGP és a tranzit Útválasztás engedélyezése különbözőnek kell lennie.

```powershell
New-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gwipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard -Asn $VNet2ASN
```

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>2. lépés - a TestVNet1 és TestVNet2 átjárók csatlakozás

Ebben a példában két átjáró ugyanahhoz az előfizetéshez vannak. Ebben a lépésben a PowerShell-munkamenetben hajthatja végre.

#### <a name="1-get-both-gateways"></a>1. Mindkét átjárók beolvasása

Jelentkezzen be, és kapcsolódjon az 1. előfizetéshez.

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet2gw = Get-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
```

#### <a name="2-create-both-connections"></a>2. Mindkét kapcsolatok létrehozása

Ebben a lépésben hoz létre a kapcsolat TestVNet1 és TestVNet2, és a kapcsolat TestVNet2 TestVNet1 számára.

```powershell
New-AzureRmVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True
```

> [!IMPORTANT]
> Győződjön meg arról, a BGP engedélyezéséhez mindkét kapcsolatokhoz.
> 
> 

A lépések elvégzése után létrejön a kapcsolat néhány perc múlva. A BGP társviszony-létesítési munkamenetet működik, amikor befejeződött a VNet – VNet-kapcsolatot.

Ha befejezte az ebben a gyakorlatban három része, a következő hálózati topológia hozott létre:

![A VNet – VNet BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

## <a name="next-steps"></a>További lépések

Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. A lépésekért lásd: [Virtuális gép létrehozása](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
