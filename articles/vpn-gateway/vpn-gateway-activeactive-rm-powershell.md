---
title: 'S2S VPN-kapcsolatot az aktív-aktív VPN-átjárók konfigurálása: Az Azure Resource Manager: PowerShell |} A Microsoft Docs'
description: Ez a cikk végigvezeti az aktív-aktív kapcsolatok konfigurálása az Azure Resource Manager és a PowerShell használatával az Azure VPN-átjárókkal.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 07/24/2018
ms.author: yushwang, cherylmc
ms.openlocfilehash: 4c5a7a138a2b491867c5c4ba7234415036c8ba0e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58100836"
---
# <a name="configure-active-active-s2s-vpn-connections-with-azure-vpn-gateways"></a>Aktív-aktív S2S VPN-kapcsolatok konfigurálása az Azure VPN-átjárókkal

Ez a cikk végigvezeti az aktív-aktív létesítmények közötti és VNet – VNet kapcsolat a Resource Manager üzemi modell és a PowerShell használatával létrehozásához szükséges lépéseken.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="about-highly-available-cross-premises-connections"></a>Tudnivalók a magas rendelkezésre állású létesítmények közötti kapcsolatokról
A létesítmények közötti és VNet – VNet kapcsolat magas rendelkezésre állás elérése érdekében kell több VPN-átjárók üzembe helyezése és a hálózatok és az Azure között több párhuzamos kapcsolatot. Lásd: [magas elérhető létesítmények közötti és VNet – VNet kapcsolat](vpn-gateway-highlyavailable.md) kapcsolati lehetőségek és topológiájának áttekintését.

Ez a cikk útmutatást egy aktív-aktív létesítmények közötti VPN-kapcsolatot, és a virtuális hálózatok közötti aktív-aktív kapcsolat beállításához.

* [1. rész – létrehozása és konfigurálása az Azure VPN-átjáró aktív-aktív módban](#aagateway)
* [2. rész – az aktív-aktív létesítmények közötti kapcsolatokat hozhat létre](#aacrossprem)
* [3. rész – a aktív-aktív VNet – VNet kapcsolat létrehozása](#aav2v)

Ha már rendelkezik egy VPN-átjárót, akkor a következőket teheti:
* [Egy meglévő VPN-átjáró aktív-készenléti frissíteni az aktív-aktív, vagy fordítva](#aaupdate)

Kombinálhatja ezeket együtt hozhat létre egy összetettebb, magas rendelkezésre állású hálózati topológia, amely megfelel az igényeinek.

> [!IMPORTANT]
> Az aktív – aktív üzemmód csak a következő termékváltozatok használja: 
>   * VpnGw1, VpnGw2, VpnGw3
>   * Nagy teljesítményű (a régi örökölt termékváltozatok)

## <a name ="aagateway"></a>1. rész – létrehozása és aktív-aktív VPN-átjárók konfigurálása
Az alábbi lépéseket az Azure VPN-átjáró aktív-aktív módban konfigurálja. Az aktív-aktív és az aktív-készenléti átjárók közötti fő különbségeket:

* Két nyilvános IP-címekkel rendelkező két átjáró IP-konfigurációk létrehozásához szükséges
* Be kell állítania a EnableActiveActiveFeature jelzőt
* Az átjáró-Termékváltozat a VpnGw1, VpnGw2, VpnGw3 vagy nagy teljesítményű (örökölt Termékváltozat) kell lennie.

A többi tulajdonság ugyanazok, mint a nem aktív-aktív átjárót. 

### <a name="before-you-begin"></a>Előkészületek
* Győződjön meg arról, hogy rendelkezik Azure-előfizetéssel. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).
* Előfordulhat, hogy telepítenie kell az Azure Resource Manager PowerShell-parancsmagjait. Lásd: [áttekintése az Azure PowerShell](/powershell/azure/overview) a PowerShell-parancsmagok telepítéséről további információt.

### <a name="step-1---create-and-configure-vnet1"></a>1. lépés – létrehozása és konfigurálása a VNet1
#### <a name="1-declare-your-variables"></a>1. A változók deklarálása
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

#### <a name="3-create-testvnet1"></a>3. A TestVNet1 létrehozása
Az alábbi minta létrehoz egy TestVNet1 nevű virtuális hálózatot és három alhálózatot, amelyek neve a következő: GatewaySubnet, FrontEnd és Backend. Az értékek behelyettesítésekor fontos, hogy az átjáróalhálózat neve mindenképp GatewaySubnet legyen. Ha ezt másként nevezi el, az átjáró létrehozása meghiúsul.

```powershell
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-active-active-mode"></a>2. lépés – a TestVNet1 létrehozása a VPN-átjáró aktív – aktív üzemmód
#### <a name="1-create-the-public-ip-addresses-and-gateway-ip-configurations"></a>1. A nyilvános IP-címek és az átjáró IP-konfiguráció létrehozása
Kérelem két nyilvános IP-cím kiosztását a virtuális hálózat számára létrehozni kívánt átjáróhoz. Az alhálózat és a szükséges IP-konfigurációk is meghatározhatja.

```powershell
$gw1pip1 = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$gw1pip2 = New-AzPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1
$gw1ipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf2 -Subnet $subnet1 -PublicIpAddress $gw1pip2
```

#### <a name="2-create-the-vpn-gateway-with-active-active-configuration"></a>2. Hozzon létre a VPN-átjáró aktív-aktív konfigurációban
Hozza létre a TestVNet1 virtuális hálózati átjáróját. Vegye figyelembe, hogy van két GatewayIpConfig bejegyzést, és a EnableActiveActiveFeature jelző be van állítva. Az átjáró létrehozása akár 45 percet vagy hosszabb időt is igénybe vehet.

```powershell
New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1,$gw1ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN -EnableActiveActiveFeature -Debug
```

#### <a name="3-obtain-the-gateway-public-ip-addresses-and-the-bgp-peer-ip-address"></a>3. Az átjáró nyilvános IP-címek és a BGP társ IP-cím beszerzése
Az átjáró létrehozása után kell szerezze be az Azure VPN Gateway a BGP-Társgép IP-címe. Ez a cím az Azure VPN Gateway a BGP-társ számára a helyszíni VPN-eszközök konfigurálása van szükség.

```powershell
$gw1pip1 = Get-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1
$gw1pip2 = Get-AzPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
```

A VPN-átjáró és a megfelelő BGP-Társgép IP-címek az egyes átjárópéldányok lefoglalt két nyilvános IP-cím megjelenítéséhez használja a következő parancsmagokat:

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

Az átjárópéldányok tartozó sorrendjét, a nyilvános IP-címek és a megfelelő BGP társviszony-létesítés címek megegyeznek. Ebben a példában az átjáró virtuális gép nyilvános IP-címét 40.112.190.5 10.12.255.4 fogja használni a BGP társviszony-létesítés címként, és az átjáró 138.91.156.129 10.12.255.5 fogja használni. Ezek az információk szükségesek a a helyszíni VPN-eszközök az aktív-aktív átjáróhoz való csatlakozáskor beállításakor. Az átjáró a diagram az összes cím alatt jelenik meg:

![aktív-aktív átjáró](./media/vpn-gateway-activeactive-rm-powershell/active-active-gw.png)

Az átjáró létrehozása után használhatja az átjáró aktív-aktív létesítmények közötti és VNet – VNet kapcsolat létrehozásához. A következő részek lépésről lépésre bemutatjuk a gyakorlatban végrehajtásához.

## <a name ="aacrossprem"></a>2. rész – egy aktív-aktív létesítmények közötti kapcsolat
Létesítmények közötti kapcsolatot létesíteni, szüksége a helyszíni VPN-eszköz képviselő helyi hálózati átjáró és a egy kapcsolatot az Azure VPN gateway kapcsolódni a helyi hálózati átjáró létrehozásához. Ebben a példában az Azure VPN-átjáró aktív-aktív módban van. Ennek eredményeképpen annak ellenére, hogy csak egy helyszíni VPN-eszköz (helyi hálózati átjáró) és a egy kapcsolati erőforrás van, az Azure VPN gateway-példány is fog létrehozni a S2S VPN-alagutat a helyszíni eszköz.

A folytatás előtt ellenőrizze, hogy befejezte [1. rész](#aagateway) ebben a gyakorlatban.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>1. lépés – létrehozása és a helyi hálózati átjáró konfigurálása
#### <a name="1-declare-your-variables"></a>1. A változók deklarálása
Ebben a gyakorlatban továbbra is össze az ábrán látható. Ne felejtse el az értékeket olyanokra cserélni, amelyeket a saját konfigurációjához kíván használni.

```powershell
$RG5 = "TestAARG5"
$Location5 = "West US"
$LNGName51 = "Site5_1"
$LNGPrefix51 = "10.52.255.253/32"
$LNGIP51 = "131.107.72.22"
$LNGASN5 = 65050
$BGPPeerIP51 = "10.52.255.253"
```

Néhány dolgot a helyi hálózati átjáró paraméterek kapcsolatban vegye figyelembe:

* A helyi hálózati átjárót, a VPN-átjáró lehet az azonos vagy eltérő helyen és erőforráscsoportban. Ez a példa bemutatja azokat a különböző erőforráscsoportokban, de ugyanazon Azure-helyen.
* Csak egy helyszíni VPN-eszköz van, ahogyan fentebb, az aktív-aktív kapcsolat vagy a BGP protokoll nélkül is működni. Ebben a példában a létesítmények közötti kapcsolathoz használja a BGP.
* Ha BGP engedélyezve van, az előtagot kell deklarálnia a helyi hálózati átjáróhoz a BGP-Társgép IP-cím a VPN-eszköz a gazdagép-címét. Ebben az esetben egy tulajdonságot/32 előtag "10.52.255.253/32".
* Ne feledje a helyszíni hálózat és az Azure VNet közötti különböző BGP ASN kell használnia. Ha azonos, módosítsa a virtuális hálózat ASN-t, ha a helyszíni VPN-eszköz már használja az ASN-t más BGP-szomszédok társviszonyt szeretne.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. A helyi hálózati átjáró létrehozása Site5
Mielőtt folytatja, győződjön meg arról, hogy továbbra is csatlakozik az 1. előfizetéshez. Hozza létre az erőforráscsoportot, ha, van még nem hozott létre.

```powershell
New-AzResourceGroup -Name $RG5 -Location $Location5
New-AzLocalNetworkGateway -Name $LNGName51 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP51 -AddressPrefix $LNGPrefix51 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP51
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>2. lépés – a virtuális hálózati átjáró és a helyi hálózati átjáró csatlakoztatása
#### <a name="1-get-the-two-gateways"></a>1. A két átjáró beolvasása

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw1 = Get-AzLocalNetworkGateway  -Name $LNGName51 -ResourceGroupName $RG5
```

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. A TestVNet1 – Site5 kapcsolat létrehozása
Ebben a lépésben, a TestVNet1 felől a létre Site5_1 az "EnableBGP" $True értékűre állítani.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection151 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw1 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-on-premises-vpn-device"></a>3. VPN és BGP paramétereit a helyszíni VPN-eszköz
Az alábbi példa felsorolja a paramétereket kell megadnia a BGP-konfigurációs szakasz azokat a helyszíni VPN-eszköz ehhez a gyakorlathoz az:

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

A kapcsolat néhány perc alatt létrejön, és a BGP társviszony-létesítési munkamenet indul, IPsec-kapcsolat létrejöttét követően. Ebben a példában eddig csak egy helyszíni VPN-eszköz, ami az alábbi ábrán van konfigurálva:

![active-active-crossprem](./media/vpn-gateway-activeactive-rm-powershell/active-active.png)

### <a name="step-3---connect-two-on-premises-vpn-devices-to-the-active-active-vpn-gateway"></a>3. lépés – két helyszíni VPN-eszköz csatlakoztatása az aktív-aktív VPN gatewayhez
Ha két VPN-eszközök ugyanazon a helyszíni hálózaton található, kettős redundancia érheti el az Azure VPN gateway a második VPN-eszközhöz való csatlakozással.

#### <a name="1-create-the-second-local-network-gateway-for-site5"></a>1. A második helyi hálózati átjáró létrehozása az Site5
Az átjáró IP-cím, cím előtagja és a második helyi hálózati átjáró BGP-társviszony-létesítési címét nem lehet átfedésben a korábbi helyi hálózati átjáró ugyanahhoz a helyszíni hálózathoz.

```powershell
$LNGName52 = "Site5_2"
$LNGPrefix52 = "10.52.255.254/32"
$LNGIP52 = "131.107.72.23"
$BGPPeerIP52 = "10.52.255.254"
```

```powershell
New-AzLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP52 -AddressPrefix $LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP52
```

#### <a name="2-connect-the-vnet-gateway-and-the-second-local-network-gateway"></a>2. A VNet-átjáró és a második helyi hálózati átjáró csatlakoztatása
A TestVNet1 felől a létre Site5_2 "EnableBGP" $True értékűre állítani a

```powershell
$lng5gw2 = Get-AzLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5
```

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection152 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw2 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-second-on-premises-vpn-device"></a>3. A második a helyszíni VPN-eszköz VPN és BGP paraméterei
Ehhez hasonlóan listákkal alább a paramétereket kell megadnia, a második VPN-eszköz:

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

Miután létrejött a kapcsolat (alagutak) vannak kettős redundáns VPN-eszközök és csatlakoztatása a helyszíni hálózat és az Azure alagutak fog rendelkezni:

![dual-redundancy-crossprem](./media/vpn-gateway-activeactive-rm-powershell/dual-redundancy.png)

## <a name ="aav2v"></a>3. rész – egy aktív-aktív VNet – VNet kapcsolat
Ez a szakasz egy aktív-aktív VNet – VNet kapcsolat BGP-vel hoz létre. 

Az alábbi útmutató a fent leírt lépések folytatása. Meg kell adnia a [1. rész](#aagateway) létrehozása és konfigurálása a TestVNet1 és a VPN Gateway a BGP. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>1. lépés – TestVNet2 és a VPN-átjáró létrehozása
Fontos győződjön meg arról, hogy az IP-címtér az új TestVNet2, virtuális hálózat nem átfedésben a VNet-címtartományok.

Ebben a példában a virtuális hálózatok ugyanahhoz az előfizetéshez tartozik. Beállíthatja a VNet – VNet kapcsolatokhoz különböző előfizetésekben; között Tekintse meg [VNet – VNet kapcsolat konfigurálása](vpn-gateway-vnet-vnet-rm-ps.md) további részleteket. Mindenképpen adja hozzá a "-enablebgp paramétert $True" BGP engedélyezése a kapcsolatok létrehozásakor.

#### <a name="1-declare-your-variables"></a>1. A változók deklarálása
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

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. TestVNet2 az új erőforráscsoport létrehozása

```powershell
New-AzResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2
```

#### <a name="3-create-the-active-active-vpn-gateway-for-testvnet2"></a>3. TestVNet2 az aktív-aktív VPN-átjáró létrehozása
Kérelem két nyilvános IP-cím kiosztását a virtuális hálózat számára létrehozni kívánt átjáróhoz. Az alhálózat és a szükséges IP-konfigurációk is meghatározhatja.

```powershell
$gw2pip1 = New-AzPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
$gw2pip2 = New-AzPublicIpAddress -Name $GW2IPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2 = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gw2ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1
$gw2ipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf2 -Subnet $subnet2 -PublicIpAddress $gw2pip2
```

A VPN-átjáró létrehozása az AS-számát és a "EnableActiveActiveFeature" jelző. Vegye figyelembe, hogy az Azure VPN gatewayek kell bírálja felül az alapértelmezett ASN-t. Az ASN-eket a csatlakoztatott virtuális hálózat számára ahhoz, hogy a BGP és a tranzit útválasztást különbözőnek kell lennie.

```powershell
New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1,$gw2ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet2ASN -EnableActiveActiveFeature
```

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>2. lépés – a TestVNet1 és TestVNet2 átjárók csatlakoztatása
Ebben a példában mindkét átjáró ugyanabban az előfizetésben vannak. Ebben a lépésben a PowerShell-munkamenetben hajthatja végre.

#### <a name="1-get-both-gateways"></a>1. Mindkét átjárótípus beolvasása
Jelentkezzen be, és kapcsolódjon az 1. előfizetéshez.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet2gw = Get-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
```

#### <a name="2-create-both-connections"></a>2. Kapcsolatok létrehozása
Ebben a lépésben létrehozza a TestVNet1 kapcsolat TestVNet2 és, és a kapcsolat TestVNet2 származó a testvnet1 felé.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True
```

> [!IMPORTANT]
> Győződjön meg arról, hogy mindkét kapcsolat – BGP engedélyezve.
> 
> 

Társviszony-létesítési munkamenetet néhány percet, és a BGP-t fog lehet létrehozni a kapcsolatot az alábbi lépések elvégzése után lesz be kettős redundancia és a VNet – VNet kapcsolat befejeződése után:

![active-active-v2v](./media/vpn-gateway-activeactive-rm-powershell/vnet-to-vnet.png)

## <a name ="aaupdate"></a>Egy meglévő VPN-átjáró frissítése

Ez a szakasz segítséget nyújt módosítani egy meglévő Azure VPN-átjáró aktív-készenléti aktív – aktív üzemmód, vagy fordítva.

### <a name="change-an-active-standby-gateway-to-an-active-active-gateway"></a>Egy aktív-készenléti átjárót egy aktív-aktív átjáróhoz módosítása

Az alábbi példa egy aktív-aktív átjárót egy aktív-készenléti átjáró alakítja át. Amikor módosítja egy aktív-készenléti átjáró aktív-aktív, akkor egy másik nyilvános IP-cím létrehozása, majd adjon hozzá egy második átjáró IP-konfigurációt.

#### <a name="1-declare-your-variables"></a>1. A változók deklarálása

Cserélje le a példák a beállításokkal, hogy a saját konfigurációjához szükséges, majd deklarálja ezeket az értékeket használja a következő paraméterekkel.

```powershell
$GWName = "TestVNetAA1GW"
$VNetName = "TestVNetAA1"
$RG = "TestVPNActiveActive01"
$GWIPName2 = "gwpip2"
$GWIPconf2 = "gw1ipconf2"
```

Deklaráló a változókat, után másolja, és illessze be a PowerShell-konzolt, ebben a példában.

```powershell
$vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$location = $gw.Location
```

#### <a name="2-create-the-public-ip-address-then-add-the-second-gateway-ip-configuration"></a>2. A nyilvános IP-cím létrehozása, majd adja hozzá a második átjáró IP-konfiguráció

```powershell
$gwpip2 = New-AzPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG -Location $location -AllocationMethod Dynamic
Add-AzVirtualNetworkGatewayIpConfig -VirtualNetworkGateway $gw -Name $GWIPconf2 -Subnet $subnet -PublicIpAddress $gwpip2
```

#### <a name="3-enable-active-active-mode-and-update-the-gateway"></a>3. Aktív – aktív üzemmód engedélyezése és az átjáró frissítése

Ebben a lépésben aktív – aktív üzemmód engedélyezése és az átjáró frissítése. A példában a VPN-átjáró jelenleg használja a korábbi a Standard Termékváltozat. Aktív-aktív azonban nem támogatja a Standard Termékváltozat. Méretezze át a régi Termékváltozat, amely (ebben az esetben az HighPerformance) támogatott, egyszerűen adja meg a támogatott régi Termékváltozat, amelyet használni szeretne.

* Az új termékváltozatokra használata ebben a lépésben egy örökölt a Termékváltozat nem módosítható. Méretezze át a támogatott régi termékváltozatáról egy másikra egy örökölt Termékváltozat csak. Például nem módosítható a Termékváltozat a Standard a VpnGw1 (annak ellenére, hogy aktív-aktív támogatott VpnGw1) mert aktuális Termékváltozat a standard csomag az örökölt a Termékváltozat és VpnGw1. Átméretezési és áttelepítése termékváltozatok kapcsolatos további információkért lásd: [átjáró-termékváltozatok](vpn-gateway-about-vpngateways.md#gwsku).

* Ha azt szeretné, méretezze át az aktuális Termékváltozat, például a VpnGw1, VpnGw3, megteheti ezt a lépést használatával, mert a termékváltozatok a következők a ugyanazon Termékváltozat-család. Ehhez használja az értéket: ```-GatewaySku VpnGw3```

Használata esetén ez a környezetben, ha nem szeretne méretezze át az átjárót, nem kell a - gatewaysku paraméterrel adja meg. Figyelje meg, hogy ebben a lépésben meg kell adnia a átjáróobjektum PowerShell, a tényleges frissítés indításához. Ez a frissítés eltarthat 30-45 perc, akkor is, ha nem átméretezni kívánt az átjáró.

```powershell
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -EnableActiveActiveFeature -GatewaySku HighPerformance
```

### <a name="change-an-active-active-gateway-to-an-active-standby-gateway"></a>Egy aktív-aktív átjárót egy aktív-készenléti átjáróra módosítása
#### <a name="1-declare-your-variables"></a>1. A változók deklarálása

Cserélje le a példák a beállításokkal, hogy a saját konfigurációjához szükséges, majd deklarálja ezeket az értékeket használja a következő paraméterekkel.

```powershell
$GWName = "TestVNetAA1GW"
$RG = "TestVPNActiveActive01"
```

Után a változók deklaráló, el szeretné eltávolítani az IP-konfiguráció neve.

```powershell
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$ipconfname = $gw.IpConfigurations[1].Name
```

#### <a name="2-remove-the-gateway-ip-configuration-and-disable-the-active-active-mode"></a>2. Az átjáró IP-konfiguráció eltávolítása és az aktív – aktív üzemmód letiltása

Ez a példa használatával az átjáró IP-konfiguráció eltávolítása és aktív – aktív üzemmód letiltása. Figyelje meg, hogy meg kell adnia a átjáróobjektum PowerShell, a tényleges frissítés indításához.

```powershell
Remove-AzVirtualNetworkGatewayIpConfig -Name $ipconfname -VirtualNetworkGateway $gw
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -DisableActiveActiveFeature
```

Ez a frissítés 30-ig 45 percig is eltarthat.

## <a name="next-steps"></a>További lépések
Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. A lépésekért lásd: [Virtuális gép létrehozása](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
