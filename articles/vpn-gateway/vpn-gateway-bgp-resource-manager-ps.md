---
title: 'Azure VPN-átjáró: BGP konfigurálása: PowerShell'
description: Ez a cikk bemutatja a BGP konfigurálását az Azure VPN-átjárókkal az Azure Resource Manager és a PowerShell használatával.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 04/12/2017
ms.author: yushwang
ms.openlocfilehash: 78147a96d6d9e92c2602b6a83cbed743cf2abf37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152040"
---
# <a name="how-to-configure-bgp-on-azure-vpn-gateways-using-powershell"></a>A BGP konfigurálása az Azure VPN-átjáróin a PowerShell használatával
Ez a cikk bemutatja a BGP engedélyezésének lépéseit egy telephelyközi helyek közötti (S2S) VPN-kapcsolaton és egy virtuális hálózatközötti kapcsolaton az Erőforrás-kezelő telepítési modell és a PowerShell használatával.



## <a name="about-bgp"></a>A BGP ismertetése
A BGP az interneten gyakran használt szabványos útválasztási protokoll az útválasztási és elérhetőségi információcserére két vagy több hálózat között. A BGP lehetővé teszi az Azure VPN Gateway átjárók és a helyszíni VPN-eszközök (más néven BGP-társak vagy -szomszédok) számára az információcserét az „útvonalakat” illetően. Ezáltal mindkét átjáró ismerni fogja az érintett átjárókon és útválasztókon áthaladó előtagok rendelkezésre állási és elérhetőségi információit. A BGP lehetővé teszi a több hálózat közötti tranzit útválasztást azon útvonalak propagálásával az összes többi BGP-társ számára, amelyeket a BGP-átjáró az egyik BGP-társtól vesz át.

A [BGP és az Azure VPN-átjárók áttekintése című témakörben](vpn-gateway-bgp-overview.md) olvashat bővebben a BGP előnyeiről, valamint a BGP használatának technikai követelményeiről és szempontjairól.

## <a name="getting-started-with-bgp-on-azure-vpn-gateways"></a>A BGP első lépései az Azure VPN-átjáróin

Ez a cikk végigvezeti a következő feladatok elvégzésének lépésein:

* [1. rész – BGP engedélyezése az Azure VPN-átjárón](#enablebgp)
* 2. rész - Telephelyközi kapcsolat létesítése a BGP-vel
* [3. rész - Virtuális hálózat és virtuális hálózat kapcsolat létrehozása a BGP-vel](#v2vbgp)

Az utasítások minden egyes része alapvető építőelemként szolgáló építőelem a BGP hálózati kapcsolatban való engedélyezéséhez. Ha mindhárom részt elvégzi, a topológiát az alábbi ábrán látható módon kell elkészíteni:

![BGP topológia](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

Az alkatrészek kombinálásával összetettebb, többugrásos, tranzithálózatot hozhat létre, amely megfelel az igényeinek.

## <a name="part-1---configure-bgp-on-the-azure-vpn-gateway"></a><a name ="enablebgp"></a>1. rész - A BGP konfigurálása az Azure VPN-átjárón
A konfigurációs lépések az Azure VPN-átjáró BGP-paramétereit állították be az alábbi ábrán látható módon:

![BGP-átjáró](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Előkészületek
* Győződjön meg arról, hogy rendelkezik Azure-előfizetéssel. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).
* Telepítse az Azure Resource Manager PowerShell-parancsmagjait. További információ a PowerShell-parancsmagok telepítéséről: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview). 

### <a name="step-1---create-and-configure-vnet1"></a>1. lépés - Virtuális hálózat létrehozása és konfigurálása1
#### <a name="1-declare-your-variables"></a>1. Deklarálja a változókat
Ehhez a gyakorlathoz kezdjük a változóink deklarálásával. A következő példa deklarálja a változókat a gyakorlat értékei alapján. Az éles konfigurációhoz ne felejtse el ezeket az értékeket a saját értékeire cserélni. Ezeket a változókat akkor használhatja, ha azért hajtja végre a lépéseket, hogy megismerje ezt a konfigurációtípust. Módosítsa a változókat, majd másolja és illessze be őket a PowerShell-konzolra.

```powershell
$Sub1 = "Replace_With_Your_Subscription_Name"
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
Az Erőforrás-kezelő parancsmagjainak használatához győződjön meg arról, hogy PowerShell-módba vált. További információ: [A Windows PowerShell használata a Resource Managerrel](../powershell-azure-resource-manager.md).

Nyissa meg a PowerShell konzolt, és csatlakozzon a fiókjához. A következő minta segíthet a kapcsolódásban:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-testvnet1"></a>3. Hozzon létre TestVNet1
A következő minta létrehoz egy TestVNet1 nevű virtuális hálózatot és három alhálózatot, az egyiket GatewaySubnet, az egyiket FrontEnd és egy Háttérrendszer. Az értékek behelyettesítésekor fontos, hogy az átjáróalhálózat neve mindenképp GatewaySubnet legyen. Ha ezt másként nevezi el, az átjáró létrehozása meghiúsul.

```powershell
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>2. lépés - A TestVNet1 VPN-átjárójának létrehozása BGP-paraméterekkel
#### <a name="1-create-the-ip-and-subnet-configurations"></a>1. Az IP- és alhálózati konfigurációk létrehozása
Kérje egy nyilvános IP-cím kiosztását a virtuális hálózat számára létrehozni kívánt átjáróhoz. A szükséges alhálózati és IP-konfigurációkat is meg kell határoznia.

```powershell
$gwpip1 = New-AzPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1
```

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. A VPN-átjáró létrehozása az AS-számmal
Hozza létre a TestVNet1 virtuális hálózati átjáróját. A BGP-hez egy útvonalalapú VPN-átjáró, valamint az -Asn összeadási paraméter szükséges a TestVNet1 ASN (AS-szám) beállításához. Ha nem állítja be az ASN paramétert, az ASN 65515 lesz hozzárendelve. Az átjáró létrehozása akár 30 percet vagy hosszabb időt is igénybe vehet.

```powershell
New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN
```

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. Az Azure BGP-társ IP-címének beszerzése
Az átjáró létrehozása után be kell szereznie a BGP-társ IP-címét az Azure VPN-átjárón. Ez a cím szükséges az Azure VPN-átjáró konfigurálásához bGP-társként a helyszíni VPN-eszközök.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet1gw.BgpSettingsText
```

Az utolsó parancs a megfelelő BGP-konfigurációkat jeleníti meg az Azure VPN-átjárón; például:

```powershell
$vnet1gw.BgpSettingsText
{
    "Asn": 65010,
    "BgpPeeringAddress": "10.12.255.30",
    "PeerWeight": 0
}
```

Az átjáró létrehozása után ezzel az átjáróval hozhat létre több telephelyközötti kapcsolatot vagy virtuális hálózatközötti kapcsolatot a BGP-vel. A következő szakaszok végigvezetik a gyakorlat elvégzéséhez szükséges lépéseken.

## <a name="part-2---establish-a-cross-premises-connection-with-bgp"></a><a name ="crossprembbgp"></a>2. rész - Telephelyközi kapcsolat létesítése a BGP-vel

A telephelyek közötti kapcsolat létrehozásához létre kell hoznia egy helyi hálózati átjárót a helyszíni VPN-eszköz ábrázolásához, és egy kapcsolatot a VPN-átjáró és a helyi hálózati átjáró csatlakoztatásához. Bár vannak olyan cikkek, amelyek végigvezetik ezeket a lépéseket, ez a cikk a BGP konfigurációs paraméterek megadásához szükséges további tulajdonságokat tartalmazza.

![BGP több helyiségszámára](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)

Mielőtt folytatná, győződjön meg róla, hogy befejezte a gyakorlat [1.](#enablebgp)

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>1. lépés - A helyi hálózati átjáró létrehozása és konfigurálása

#### <a name="1-declare-your-variables"></a>1. Deklarálja a változókat

Ez a gyakorlat folytatja az ábrán látható konfiguráció létrehozását. Ne felejtse el az értékeket olyanokra cserélni, amelyeket a saját konfigurációjához kíván használni.

```powershell
$RG5 = "TestBGPRG5"
$Location5 = "East US 2"
$LNGName5 = "Site5"
$LNGPrefix50 = "10.52.255.254/32"
$LNGIP5 = "Your_VPN_Device_IP"
$LNGASN5 = 65050
$BGPPeerIP5 = "10.52.255.254"
```

Néhány dolog, amit érdemes megjegyezni a helyi hálózati átjáró paramétereivel kapcsolatban:

* A helyi hálózati átjáró lehet a VPN-átjáróval azonos vagy eltérő helyen és erőforráscsoportban. Ez a példa különböző erőforráscsoportokban jeleníti meg őket különböző helyeken.
* A helyi hálózati átjáróhoz deklarálandó előtag a VPN-eszközön található BGP-társ IP-címének állomáscíme. Ebben az esetben ez a /32 előtag a "10.52.255.254/32".
* Emlékeztetőül különböző BGP ASN-eket kell használnia a helyszíni hálózatok és az Azure virtuális hálózat között. Ha megegyeznek, módosítania kell a virtuális hálózat ASN-jét, ha a helyszíni VPN-eszköz már használja az ASN-t más BGP-szomszédok társviszony-lefolyására.

Mielőtt folytatja, győződjön meg arról, hogy továbbra is csatlakozik az 1. előfizetéshez.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. Helyi hálózati átjáró létrehozása az 5.

A helyi hálózati átjáró létrehozása előtt mindenképpen hozza létre az erőforráscsoportot, ha az nem jön létre. Figyelje meg a helyi hálózati átjáró két további paraméterét: az Asn és a BgpPeerAddress.

```powershell
New-AzResourceGroup -Name $RG5 -Location $Location5

New-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>2. lépés – A virtuális hálózati átjáró és a helyi hálózati átjáró csatlakoztatása

#### <a name="1-get-the-two-gateways"></a>1. Szerezd meg a két átjáró

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5
```

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. A TestVNet1 és a Site5 közötti kapcsolat létrehozása

Ebben a lépésben hozza létre a kapcsolatot testvnet1 a site5. A BGP engedélyezéséhez meg kell adnia a "-EnableBGP $True" értéket. Ahogy azt korábban már tárgyaltuk, lehetőség van bgp- és nem BGP-kapcsolatok ra ugyanazon Azure VPN-átjáróhoz. Ha a BGP nincs engedélyezve a kapcsolat tulajdonság, az Azure nem engedélyezi a BGP-t ehhez a kapcsolathoz, annak ellenére, hogy a BGP-paraméterek már konfigurálva vannak mindkét átjárón.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

A következő példa azokat a paramétereket sorolja fel, amelyeket a helyszíni VPN-eszköz BGP-konfigurációs szakaszába nanervanál ehhez a gyakorlathoz:

```

- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.254
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP    : 10.12.255.30
- Static route         : Add a route for 10.12.255.30/32, with nexthop being the VPN tunnel interface on your device
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

A kapcsolat néhány perc múlva jön létre, és a BGP-társviszony-létesítési munkamenet az IPsec-kapcsolat létrejötte után kezdődik.

## <a name="part-3---establish-a-vnet-to-vnet-connection-with-bgp"></a><a name ="v2vbgp"></a>3. rész - Virtuális hálózat és virtuális hálózat kapcsolat létrehozása a BGP-vel

Ez a szakasz virtuális hálózatról virtuális hálózatra létesítő kapcsolatot ad hozzá a BGP-vel, az alábbi ábrán látható módon:

![BGP a virtuális hálózat-vnet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

Az alábbi utasítások az előző lépésektől folytatódnak. A TestVNet1 és a VPN-átjáró BGP-vel való létrehozásához és konfigurálásához ki kell töltenie az [I. részt.](#enablebgp) 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>1. lépés - TestVNet2 és a VPN-átjáró létrehozása

Fontos, hogy győződjön meg arról, hogy az új virtuális hálózat, a TestVNet2 IP-címtere nem fedi át a virtuális hálózat egyik tartományát sem.

Ebben a példában a virtuális hálózatok ugyanahhoz az előfizetéshez tartoznak. Virtuális hálózat és virtuális hálózat közötti kapcsolatokat állíthat be a különböző előfizetések között. További információt a [Virtuálishálózat és virtuális hálózat között lévő kapcsolat konfigurálása](vpn-gateway-vnet-vnet-rm-ps.md)című témakörben talál. Győződjön meg arról, hogy a BGP engedélyezéséhez a kapcsolatok létrehozásakor hozzáadja a "-EnableBgp $True".Make sure you add the "-EnableBgp $True" when creating the connections to enable BGP.

#### <a name="1-declare-your-variables"></a>1. Deklarálja a változókat

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

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. Hozzon létre TestVNet2 az új erőforráscsoportban

```powershell
New-AzResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2
```

#### <a name="3-create-the-vpn-gateway-for-testvnet2-with-bgp-parameters"></a>3. A TestVNet2 VPN-átjárójának létrehozása BGP-paraméterekkel

Kérjen nyilvános IP-címet a virtuális hálózathoz létrehozandó átjáróhoz, és adja meg a szükséges alhálózati és IP-konfigurációkat.

```powershell
$gwpip2    = New-AzPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2     = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2   = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gwipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName2 -Subnet $subnet2 -PublicIpAddress $gwpip2
```

Hozza létre a VPN-átjárót az AS-számmal. Felül kell bírnod az alapértelmezett ASN-t az Azure VPN-átjáróin. A csatlakoztatott virtuális hálózatok ASN-ek eltérőnek kell lennie a BGP és a tranzit útválasztás engedélyezéséhez.

```powershell
New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gwipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet2ASN
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

A lépések végrehajtása után a kapcsolat néhány perc múlva létrejön. A BGP-társviszony-létesítési munkamenet a virtuális hálózat és a virtuális hálózat között létesítési kapcsolat befejezése után feláll.

Ha a gyakorlat mindhárom részét elvégezte, a következő hálózati topológiát hozta létre:

![BGP a virtuális hálózat-vnet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

## <a name="next-steps"></a>További lépések

Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. A lépésekért lásd: [Virtuális gép létrehozása](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
