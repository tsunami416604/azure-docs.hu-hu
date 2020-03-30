---
title: Aktív-aktív S2S Azure VPN-átjárókapcsolatok konfigurálása
description: Ez a cikk bemutatja az aktív és aktív kapcsolatok konfigurálását az Azure VPN-átjárókkal az Azure Resource Manager és a PowerShell használatával.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 07/24/2018
ms.author: yushwang
ms.reviewer: cherylmc
ms.openlocfilehash: ec3697208434eb971e47136416f2c2cc541b5cea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244640"
---
# <a name="configure-active-active-s2s-vpn-connections-with-azure-vpn-gateways"></a>Aktív-aktív S2S VPN-kapcsolatok konfigurálása az Azure VPN-átjárókkal

Ez a cikk bemutatja az aktív és a virtuális hálózat közötti kapcsolatok létrehozásának lépéseit az Erőforrás-kezelő telepítési modelljével és a PowerShellhasználatával.



## <a name="about-highly-available-cross-premises-connections"></a>Magas rendelkezésre állású, telephelyközi kapcsolatok –
A létesítmények közötti és a virtuális hálózat közötti kapcsolat magas rendelkezésre állásának elérése érdekében több VPN-átjárót kell üzembe helyeznie, és több párhuzamos kapcsolatot kell létesítenie a hálózatok és az Azure között. A kapcsolati lehetőségek és a topológia áttekintése [a magas rendelkezésre álló, létesítmények közötti és virtuális hálózatközötti kapcsolat című](vpn-gateway-highlyavailable.md) témakörben található.

Ez a cikk utasításokat tartalmaz egy aktív-aktív létesítmények közötti VPN-kapcsolat és két virtuális hálózat közötti aktív-aktív kapcsolat beállításához.

* [1. rész – Az Azure VPN-átjáró létrehozása és konfigurálása aktív-aktív módban](#aagateway)
* [2. rész - Aktív-aktív telephelyközi kapcsolatok létrehozása](#aacrossprem)
* [3. rész - Aktív-aktív virtuális hálózat és virtuális hálózat kapcsolatok létrehozása](#aav2v)

Ha már rendelkezik VPN-átjáróval, a következőket teheti:
* [Meglévő VPN-átjáró frissítése aktív készenlétről aktív-aktív ra, vagy fordítva](#aaupdate)

Ezeket kombinálva összetettebb, magas rendelkezésre állású hálózati topológiát hozhat létre, amely megfelel az igényeinek.

> [!IMPORTANT]
> Az aktív-aktív mód csak a következő szereket használja: 
>   * VpnGw1, VpnGw2, VpnGw3
>   * HighPerformance (régi örökölt suk-ok esetén)

## <a name="part-1---create-and-configure-active-active-vpn-gateways"></a><a name ="aagateway"></a>1. rész – Aktív-aktív VPN-átjárók létrehozása és konfigurálása
A következő lépések az Azure VPN-átjáróaktív-aktív módban konfigurálása. Az aktív és az aktív és az aktív készenléti átjárók közötti legfontosabb különbségek:

* Két átjáró IP-konfigurációt kell létrehoznia két nyilvános IP-címmel
* Be kell állítania az EnableActiveActiveFeature jelzőt
* Az átjáró termékváltozatának VpnGw1, VpnGw2, VpnGw3 vagy HighPerformance (örökölt Termékváltozat) kell lennie.

A többi tulajdonság megegyezik a nem aktív átjárókkal. 

### <a name="before-you-begin"></a>Előkészületek
* Győződjön meg arról, hogy rendelkezik Azure-előfizetéssel. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).
* Előfordulhat, hogy telepítenie kell az Azure Resource Manager PowerShell-parancsmagjait. A PowerShell-parancsmagok telepítéséről az [Azure PowerShell áttekintése című](/powershell/azure/overview) témakörben olvashat bővebben.

### <a name="step-1---create-and-configure-vnet1"></a>1. lépés - Virtuális hálózat létrehozása és konfigurálása1
#### <a name="1-declare-your-variables"></a>1. Deklarálja a változókat
Ezt a gyakorlatot a változók deklarálásával kezdjük. Az alábbi példa a gyakorlathoz használt értékekkel deklarálja a változókat. Az éles konfigurációhoz ne felejtse el ezeket az értékeket a saját értékeire cserélni. Ezeket a változókat akkor használhatja, ha azért hajtja végre a lépéseket, hogy megismerje ezt a konfigurációtípust. Módosítsa a változókat, majd másolja és illessze be őket a PowerShell-konzolra.

```powershell
$Sub1 = "Ross"
$RG1 = "TestAARG1"
$Location1 = "West US"
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
$GW1IPName1 = "VNet1GWIP1"
$GW1IPName2 = "VNet1GWIP2"
$GW1IPconf1 = "gw1ipconf1"
$GW1IPconf2 = "gw1ipconf2"
$Connection12 = "VNet1toVNet2"
$Connection151 = "VNet1toSite5_1"
$Connection152 = "VNet1toSite5_2"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Csatlakozás az előfizetéshez, és hozzon létre egy új erőforráscsoportot
A Resource Manager parancsmagjainak használatához váltson át PowerShell módba. További információ: [A Windows PowerShell használata a Resource Managerrel](../powershell-azure-resource-manager.md).

Nyissa meg a PowerShell konzolt, és csatlakozzon a fiókjához. A következő minta segíthet a kapcsolódásban:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-testvnet1"></a>3. Hozzon létre TestVNet1
Az alábbi minta létrehoz egy TestVNet1 nevű virtuális hálózatot és három alhálózatot, amelyek neve a következő: GatewaySubnet, FrontEnd és Backend. Az értékek behelyettesítésekor fontos, hogy az átjáróalhálózat neve mindenképp GatewaySubnet legyen. Ha ezt másként nevezi el, az átjáró létrehozása meghiúsul.

```powershell
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-active-active-mode"></a>2. lépés - A VPN-átjáró létrehozása a TestVNet1 számára aktív-aktív módban
#### <a name="1-create-the-public-ip-addresses-and-gateway-ip-configurations"></a>1. Nyilvános IP-címek és átjáró IP-konfigurációk létrehozása
Két nyilvános IP-cím kiosztása a virtuális hálózathoz létrehozandó átjáróhoz. Azt is meg kell határoznia az alhálózati és IP-konfigurációk szükséges.

```powershell
$gw1pip1 = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$gw1pip2 = New-AzPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1
$gw1ipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf2 -Subnet $subnet1 -PublicIpAddress $gw1pip2
```

#### <a name="2-create-the-vpn-gateway-with-active-active-configuration"></a>2. A VPN-átjáró létrehozása aktív-aktív konfigurációval
Hozza létre a TestVNet1 virtuális hálózati átjáróját. Vegye figyelembe, hogy két GatewayIpConfig bejegyzés van, és az EnableActiveActiveFeature jelző be van állítva. Az átjáró létrehozása akár 45 percet vagy hosszabb időt is igénybe vehet.

```powershell
New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1,$gw1ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN -EnableActiveActiveFeature -Debug
```

#### <a name="3-obtain-the-gateway-public-ip-addresses-and-the-bgp-peer-ip-address"></a>3. Szerezze be az átjáró nyilvános IP-címeit és a BGP-társ IP-címét
Az átjáró létrehozása után be kell szereznie a BGP-társ IP-címét az Azure VPN-átjárón. Ez a cím szükséges az Azure VPN-átjáró konfigurálásához bGP-társként a helyszíni VPN-eszközök.

```powershell
$gw1pip1 = Get-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1
$gw1pip2 = Get-AzPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
```

A következő parancsmagok segítségével jelenítse meg a VPN-átjárószámára lefoglalt két nyilvános IP-címet és a megfelelő BGP-társ IP-címeit az egyes átjárópéldányokhoz:

```powershell
PS D:\> $gw1pip1.IpAddress
40.112.190.5

PS D:\> $gw1pip2.IpAddress
138.91.156.129

PS D:\> $vnet1gw.BgpSettingsText
{
  "Asn": 65010,
  "BgpPeeringAddress": "10.12.255.4,10.12.255.5",
  "PeerWeight": 0
}
```

Az átjárópéldányok nyilvános IP-címeinek sorrendje és a megfelelő BGP-társviszony-létesítési címek azonosak. Ebben a példában a 40.112.190.5 nyilvános IP-című átjáró virtuális gép a 10.12.255.4-et fogja használni BGP társviszony-létesítési címként, és a 138.91.156.129-es átjáró 10.12.255.5 lesz. Erre az információra akkor van szükség, amikor beállítja az aktív-aktív átjáróhoz csatlakozó helyszíni VPN-eszközöket. Az átjáró az alábbi ábrán látható az összes címmel:

![aktív-aktív átjáró](./media/vpn-gateway-activeactive-rm-powershell/active-active-gw.png)

Az átjáró létrehozása után ezzel az átjáróval aktív-aktív létesítmények közötti vagy virtuális hálózat-vnet-kapcsolat hozható létre. A következő szakaszok végigvezetik a gyakorlat elvégzéséhez szükséges lépéseken.

## <a name="part-2---establish-an-active-active-cross-premises-connection"></a><a name ="aacrossprem"></a>2. rész - Aktív-aktív telephelyközi kapcsolat létrehozása
A létesítmények közötti kapcsolat létrehozásához létre kell hoznia egy helyi hálózati átjárót a helyszíni VPN-eszköz képviseletéhez, és egy kapcsolatot az Azure VPN-átjáró és a helyi hálózati átjáró csatlakoztatásához. Ebben a példában az Azure VPN-átjáró aktív-aktív módban van. Ennek eredményeképpen annak ellenére, hogy csak egy helyszíni VPN-eszköz (helyi hálózati átjáró) és egy kapcsolati erőforrás van, mindkét Azure VPN-átjárópéldány s2S VPN-bújtatások létesítése a helyszíni eszközzel.

Mielőtt folytatná, kérjük, győződjön meg róla, hogy befejezte a gyakorlat [1.](#aagateway)

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>1. lépés - A helyi hálózati átjáró létrehozása és konfigurálása
#### <a name="1-declare-your-variables"></a>1. Deklarálja a változókat
Ez a gyakorlat folytatja az ábrán látható konfiguráció létrehozását. Ne felejtse el az értékeket olyanokra cserélni, amelyeket a saját konfigurációjához kíván használni.

```powershell
$RG5 = "TestAARG5"
$Location5 = "West US"
$LNGName51 = "Site5_1"
$LNGPrefix51 = "10.52.255.253/32"
$LNGIP51 = "131.107.72.22"
$LNGASN5 = 65050
$BGPPeerIP51 = "10.52.255.253"
```

Néhány dolog, amit érdemes megjegyezni a helyi hálózati átjáró paramétereivel kapcsolatban:

* A helyi hálózati átjáró lehet a VPN-átjáróval azonos vagy eltérő helyen és erőforráscsoportban. Ebben a példában jeleníti meg őket a különböző erőforráscsoportokban, de ugyanabban az Azure-ban helyen.
* Ha csak egy helyszíni VPN-eszköz van a fentiek szerint, az aktív-aktív kapcsolat bgp protokollal vagy anélkül is működhet. Ez a példa bgp-t használ a telephelyek közötti kapcsolathoz.
* Ha a BGP engedélyezve van, a helyi hálózati átjáróhoz deklarálandó előtag a VpN-eszközön lévő BGP-társ IP-címének állomáscíme. Ebben az esetben ez a /32 előtag a "10.52.255.253/32".
* Emlékeztetőül különböző BGP ASN-eket kell használnia a helyszíni hálózatok és az Azure virtuális hálózat között. Ha megegyeznek, módosítania kell a virtuális hálózat ASN-jét, ha a helyszíni VPN-eszköz már használja az ASN-t más BGP-szomszédok társviszony-lefolyására.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. Helyi hálózati átjáró létrehozása az 5.
Mielőtt folytatja, győződjön meg arról, hogy továbbra is csatlakozik az 1. előfizetéshez. Hozza létre az erőforráscsoportot, ha még nem jött létre.

```powershell
New-AzResourceGroup -Name $RG5 -Location $Location5
New-AzLocalNetworkGateway -Name $LNGName51 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP51 -AddressPrefix $LNGPrefix51 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP51
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>2. lépés – A virtuális hálózati átjáró és a helyi hálózati átjáró csatlakoztatása
#### <a name="1-get-the-two-gateways"></a>1. Szerezd meg a két átjáró

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw1 = Get-AzLocalNetworkGateway  -Name $LNGName51 -ResourceGroupName $RG5
```

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. A TestVNet1 és a Site5 közötti kapcsolat létrehozása
Ebben a lépésben hozza létre a kapcsolatot TestVNet1 Site5_1 az "EnableBGP" $True.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection151 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw1 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-on-premises-vpn-device"></a>3. VPN és BGP paraméterek a helyszíni VPN-eszközhöz
Az alábbi példa azokat a paramétereket sorolja fel, amelyeket a helyszíni VPN-eszköz BGP-konfigurációs szakaszába netsző paramétereket fog megadni ehhez a gyakorlathoz:

```
- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.253
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP 1  : 10.12.255.4 for tunnel to 40.112.190.5
- Azure VNet BGP IP 2  : 10.12.255.5 for tunnel to 138.91.156.129
- Static routes        : Destination 10.12.255.4/32, nexthop the VPN tunnel interface to 40.112.190.5
                         Destination 10.12.255.5/32, nexthop the VPN tunnel interface to 138.91.156.129
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

A kapcsolatot néhány perc múlva létre kell hozni, és a BGP-társviszony-létesítési munkamenet az IPsec-kapcsolat létrejötte után indul el. Ez a példa eddig csak egy helyszíni VPN-eszközt konfigurált, így az alábbi ábra:

![aktív-aktív-crossprem](./media/vpn-gateway-activeactive-rm-powershell/active-active.png)

### <a name="step-3---connect-two-on-premises-vpn-devices-to-the-active-active-vpn-gateway"></a>3. lépés – Két helyszíni VPN-eszköz csatlakoztatása az aktív-aktív VPN-átjáróhoz
Ha két VPN-eszközzel rendelkezik ugyanazon a helyszíni hálózaton, az Azure VPN-átjáró nak a második VPN-eszközhöz való csatlakoztatásával kettős redundanciát érhet el.

#### <a name="1-create-the-second-local-network-gateway-for-site5"></a>1. A második helyi hálózati átjáró létrehozása az 5.
A második helyi hálózati átjáró átjáró IP-címe, címelőtagja és BGP-társviszony-létesítési címe nem fedheti át az előző helyi hálózati átjárót ugyanahhoz a helyszíni hálózathoz.

```powershell
$LNGName52 = "Site5_2"
$LNGPrefix52 = "10.52.255.254/32"
$LNGIP52 = "131.107.72.23"
$BGPPeerIP52 = "10.52.255.254"
```

```powershell
New-AzLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP52 -AddressPrefix $LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP52
```

#### <a name="2-connect-the-vnet-gateway-and-the-second-local-network-gateway"></a>2. Csatlakoztassa a virtuális hálózat átjáróját és a második helyi hálózati átjárót
Hozza létre a kapcsolatot testvnet1-Site5_2 az "EnableBGP" beállítással $True

```powershell
$lng5gw2 = Get-AzLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5
```

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection152 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw2 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-second-on-premises-vpn-device"></a>3. VPN és BGP paraméterek a második helyszíni VPN-eszközhöz
Hasonlóképpen, az alábbiakban felsorolja a paramétereket fog beírni a második VPN-eszköz:

```
- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.254
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP 1  : 10.12.255.4 for tunnel to 40.112.190.5
- Azure VNet BGP IP 2  : 10.12.255.5 for tunnel to 138.91.156.129
- Static routes        : Destination 10.12.255.4/32, nexthop the VPN tunnel interface to 40.112.190.5
                         Destination 10.12.255.5/32, nexthop the VPN tunnel interface to 138.91.156.129
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

A kapcsolat (alagutak) létrehozása után a helyszíni hálózatot és az Azure-t összekötő kettős redundáns VPN-eszközök és alagutak lesznek:

![kettős redundancia-keresztprem](./media/vpn-gateway-activeactive-rm-powershell/dual-redundancy.png)

## <a name="part-3---establish-an-active-active-vnet-to-vnet-connection"></a><a name ="aav2v"></a>3. rész – Aktív-aktív virtuális hálózat és virtuális hálózat kapcsolat létrehozása
Ez a szakasz egy aktív-aktív virtuális hálózat-vnet kapcsolatot hoz létre a BGP-vel. 

Az alábbi útmutató a fent leírt lépések folytatása. A TestVNet1 és a VPN-átjáró BGP-vel való létrehozásához és konfigurálásához ki kell töltenie az [1.](#aagateway) 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>1. lépés - TestVNet2 és a VPN-átjáró létrehozása
Fontos, hogy győződjön meg arról, hogy az új virtuális hálózat, a TestVNet2 IP-címtere nem fedi át a virtuális hálózat egyik tartományát sem.

Ebben a példában a virtuális hálózatok ugyanahhoz az előfizetéshez tartoznak. Virtuális hálózat és virtuális hálózat közötti kapcsolatokat állíthat be a különböző előfizetések között; További részletekért tekintse meg [a Virtuális hálózat és virtuális hálózat kapcsolatkonfigurálása](vpn-gateway-vnet-vnet-rm-ps.md) című területet. Győződjön meg arról, hogy a BGP engedélyezéséhez a kapcsolatok létrehozásakor hozzáadja a "-EnableBgp $True".Make sure you add the "-EnableBgp $True" when creating the connections to enable BGP.

#### <a name="1-declare-your-variables"></a>1. Deklarálja a változókat
Ne felejtse el az értékeket olyanokra cserélni, amelyeket a saját konfigurációjához kíván használni.

```powershell
$RG2 = "TestAARG2"
$Location2 = "East US"
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
$GW2IPName1 = "VNet2GWIP1"
$GW2IPconf1 = "gw2ipconf1"
$GW2IPName2 = "VNet2GWIP2"
$GW2IPconf2 = "gw2ipconf2"
$Connection21 = "VNet2toVNet1"
$Connection12 = "VNet1toVNet2"
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. Hozzon létre TestVNet2 az új erőforráscsoportban

```powershell
New-AzResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2
```

#### <a name="3-create-the-active-active-vpn-gateway-for-testvnet2"></a>3. Az aktív-aktív VPN-átjáró létrehozása a TestVNet2-hez
Két nyilvános IP-cím kiosztása a virtuális hálózathoz létrehozandó átjáróhoz. Azt is meg kell határoznia az alhálózati és IP-konfigurációk szükséges.

```powershell
$gw2pip1 = New-AzPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
$gw2pip2 = New-AzPublicIpAddress -Name $GW2IPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2 = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gw2ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1
$gw2ipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf2 -Subnet $subnet2 -PublicIpAddress $gw2pip2
```

Hozza létre a VPN-átjárót az AS-számmal és az "EnableActiveActiveFeature" jelzővel. Vegye figyelembe, hogy felül kell bírnod az alapértelmezett ASN-t az Azure VPN-átjárókon. A csatlakoztatott virtuális hálózatok ASN-ek eltérőnek kell lennie a BGP és a tranzit útválasztás engedélyezéséhez.

```powershell
New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1,$gw2ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet2ASN -EnableActiveActiveFeature
```

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>2. lépés - A TestVNet1 és a TestVNet2 átjárók csatlakoztatása
Ebben a példában mindkét átjáró ugyanabban az előfizetésben van. Ezt a lépést ugyanabban a PowerShell-munkamenetben hajthatja végre.

#### <a name="1-get-both-gateways"></a>1. Mindkét átjáró beszerezni
Jelentkezzen be, és kapcsolódjon az 1. előfizetéshez.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet2gw = Get-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
```

#### <a name="2-create-both-connections"></a>2. Mindkét kapcsolat létrehozása
Ebben a lépésben hozza létre a kapcsolatot testvnet1 a TestVNet2, és a kapcsolat TestVNet2 a TestVNet1.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True
```

> [!IMPORTANT]
> Ügyeljen arra, hogy mindkét kapcsolathoz engedélyezze a BGP-t.
> 
> 

A lépések végrehajtása után a kapcsolat néhány percen belül létrejön, és a BGP-társviszony-létesítési munkamenet akkor lesz elérhető, ha a virtuális hálózat és a virtuális hálózat között létesített kapcsolat kettős redundanciával befejeződik:

![aktív-aktív-v2v](./media/vpn-gateway-activeactive-rm-powershell/vnet-to-vnet.png)

## <a name="update-an-existing-vpn-gateway"></a><a name ="aaupdate"></a>Meglévő VPN-átjáró frissítése

Ez a szakasz segít egy meglévő Azure VPN-átjáró aktív-készenléti állapotról aktív-aktív módra történő módosításában, vagy fordítva.

### <a name="change-an-active-standby-gateway-to-an-active-active-gateway"></a>Aktív készenléti átjáró módosítása aktív-aktív átjáróvá

A következő példa egy aktív készenléti átjárót aktív-aktív átjáróvá alakít át. Ha egy aktív készenléti átjárót aktív-aktívra módosít, létrehoz egy másik nyilvános IP-címet, majd hozzáad egy második átjáró IP-konfigurációt.

#### <a name="1-declare-your-variables"></a>1. Deklarálja a változókat

Cserélje le a példákhoz használt következő paramétereket a saját konfigurációjához szükséges beállításokkal, majd deklarálja ezeket a változókat.

```powershell
$GWName = "TestVNetAA1GW"
$VNetName = "TestVNetAA1"
$RG = "TestVPNActiveActive01"
$GWIPName2 = "gwpip2"
$GWIPconf2 = "gw1ipconf2"
```

A változók deklarálása után másolhatja és beillesztheti ezt a példát a PowerShell-konzolra.

```powershell
$vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$location = $gw.Location
```

#### <a name="2-create-the-public-ip-address-then-add-the-second-gateway-ip-configuration"></a>2. Hozza létre a nyilvános IP-címet, majd adja hozzá a második átjáró IP-konfigurációját

```powershell
$gwpip2 = New-AzPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG -Location $location -AllocationMethod Dynamic
Add-AzVirtualNetworkGatewayIpConfig -VirtualNetworkGateway $gw -Name $GWIPconf2 -Subnet $subnet -PublicIpAddress $gwpip2
```

#### <a name="3-enable-active-active-mode-and-update-the-gateway"></a>3. Aktív-aktív mód engedélyezése és az átjáró frissítése

Ebben a lépésben engedélyezi az aktív-aktív módot, és frissíti az átjárót. A példában a VPN-átjáró jelenleg egy örökölt standard termékváltozatot használ. Az aktív-aktív azonban nem támogatja a standard termékváltozatot. Az örökölt termékváltozat átméretezéséhez egy támogatott (ebben az esetben highperformance), egyszerűen adja meg a támogatott örökölt termékváltozat, amely et szeretne használni.

* Ezzel a lépéssel nem módosíthatja az örökölt termékváltozatokat az új termékváltozatok egyikére. Csak egy örökölt termékváltozat átméretezése egy másik támogatott örökölt termékváltozat. Például nem módosíthatja a termékváltozatot Standardról VpnGw1-re (bár a VpnGw1 aktív-aktív), mert a Standard egy örökölt termékváltozat, a VpnGw1 pedig egy aktuális termékváltozat. A sk-ek átméretezéséről és áttelepítéséről az [átjáró-szakosodott akin](vpn-gateway-about-vpngateways.md#gwsku)című témakörben talál további információt.

* Ha egy aktuális termékváltozatot szeretne átméretezni, például vpnGw1-et VpnGw3-ra, ezt a lépést megteheti, mert a termékváltozatok ugyanabban a termékváltozat-családban vannak. Ehhez a következő értéket kell használnia:```-GatewaySku VpnGw3```

Ha ezt a környezetben használja, ha nem kell átméretezni az átjárót, nem kell megadnia a -GatewaySku. Figyelje meg, hogy ebben a lépésben be kell állítania az átjáróobjektumot a PowerShellben a tényleges frissítés aktiválásához. A frissítés 30–45 percet is igénybe vehet, még akkor is, ha nem az átjárót méretezi át.

```powershell
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -EnableActiveActiveFeature -GatewaySku HighPerformance
```

### <a name="change-an-active-active-gateway-to-an-active-standby-gateway"></a>Aktív-aktív átjáró módosítása aktív készenléti átjáróvá
#### <a name="1-declare-your-variables"></a>1. Deklarálja a változókat

Cserélje le a példákhoz használt következő paramétereket a saját konfigurációjához szükséges beállításokkal, majd deklarálja ezeket a változókat.

```powershell
$GWName = "TestVNetAA1GW"
$RG = "TestVPNActiveActive01"
```

A változók deklarálása után kapjuk meg az eltávolítani kívánt IP-konfiguráció nevét.

```powershell
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$ipconfname = $gw.IpConfigurations[1].Name
```

#### <a name="2-remove-the-gateway-ip-configuration-and-disable-the-active-active-mode"></a>2. Távolítsa el az átjáró IP-konfigurációját, és tiltsa le az aktív-aktív módot

Ebben a példában eltávolíthatja az átjáró IP-konfigurációját, és letilthatja az aktív-aktív módot. Figyelje meg, hogy be kell állítania az átjáróobjektumot a PowerShellben a tényleges frissítés aktiválásához.

```powershell
Remove-AzVirtualNetworkGatewayIpConfig -Name $ipconfname -VirtualNetworkGateway $gw
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -DisableActiveActiveFeature
```

A frissítés akár 30–45 percet is igénybe vehet.

## <a name="next-steps"></a>További lépések
Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. A lépésekért lásd: [Virtuális gép létrehozása](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
