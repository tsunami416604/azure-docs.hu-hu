---
title: "Aktív-aktív S2S VPN-kapcsolatokat a VPN-átjárók konfigurálása: Azure Resource Manager: PowerShell |} Microsoft Docs"
description: "Ez a cikk bemutatja, hogyan aktív-aktív kapcsolatok konfigurálása az Azure VPN Gatewayek Azure Resource Manager és a PowerShell használatával."
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: azure-resource-manager
ms.assetid: 238cd9b3-f1ce-4341-b18e-7390935604fa
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/24/2018
ms.author: yushwang
ms.openlocfilehash: 41cca764335f21bed60fe968288bc8b8274f3215
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2018
---
# <a name="configure-active-active-s2s-vpn-connections-with-azure-vpn-gateways"></a>Az Azure VPN Gatewayek aktív-aktív S2S VPN-kapcsolatok konfigurálása

Ez a cikk végigvezeti a aktív-aktív létesítmények közötti és VNet – VNet kapcsolatokhoz a Resource Manager üzembe helyezési modellben és a PowerShell használatával létrehozni.

## <a name="about-highly-available-cross-premises-connections"></a>Magas rendelkezésre állású létesítmények közötti kapcsolatok
A létesítmények közötti és VNet – VNet-kapcsolatot a magas rendelkezésre állás eléréséhez kell több VPN-átjáró telepítése, és a hálózatok és az Azure közötti több párhuzamos kapcsolatok létesítéséhez. Lásd: [magas rendelkezésre álló létesítmények közötti és VNet – VNet-kapcsolatot](vpn-gateway-highlyavailable.md) kapcsolati lehetőségek és topológiájának áttekintését.

Ez a cikk bemutatja az állíthat be egy aktív-aktív létesítmények közötti VPN-kapcsolat és két virtuális hálózatok közötti aktív-aktív kapcsolat.

* [1. rész – létrehozása és konfigurálása az Azure VPN gateway aktív-aktív módban](#aagateway)
* [2. rész – aktív-aktív létesítmények közötti kapcsolatot.](#aacrossprem)
* [3. rész – aktív-aktív VNet – VNet-kapcsolatot létrehozni](#aav2v)

Ha már rendelkezik egy VPN-átjárót, akkor a következőket teheti:
* [Egy meglévő VPN-átjárót aktív-készenléti állapotban lévő frissíteni az aktív-aktív, vagy fordítva](#aaupdate)

Ezek együtt egy összetettebb, magas rendelkezésre állású hálózati topológia az igényeinek megfelelő létrehozásához kombinálhatja.

> [!IMPORTANT]
> Az aktív-aktív mód csak a következő termékváltozatok használja: 
  * VpnGw1, VpnGw2, VpnGw3
  * (A régi örökölt SKU) HighPerformance
> 
> 

## <a name ="aagateway"></a>1. rész – létrehozása és aktív-aktív VPN-átjárók konfigurálása
Az alábbi lépéseket az Azure VPN gateway állít be aktív-aktív módot. Az aktív-aktív és az aktív-készenléti állapotban lévő átjárók közötti legfőbb különbségek:

* Két nyilvános IP-címekkel rendelkező két átjáró IP-konfigurációk létrehozásához szükséges
* Be kell állítania a EnableActiveActiveFeature jelzőt
* Az átjáró-Termékváltozat VpnGw1, VpnGw2, VpnGw3 vagy HighPerformance (örökölt SKU) kell lennie.

A többi tulajdonság ugyanazok, mint a nem aktív-aktív átjárókat. 

### <a name="before-you-begin"></a>Előkészületek
* Győződjön meg arról, hogy rendelkezik Azure-előfizetéssel. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).
* Előfordulhat, hogy telepítenie kell az Azure Resource Manager PowerShell-parancsmagjait. Lásd: [áttekintés az Azure PowerShell](/powershell/azure/overview) a PowerShell-parancsmagok telepítéséről további információt.

### <a name="step-1---create-and-configure-vnet1"></a>1. lépés – létrehozása és VNet1 konfigurálása
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
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-testvnet1"></a>3. A TestVNet1 létrehozása
Az alábbi minta létrehoz egy TestVNet1 nevű virtuális hálózatot és három alhálózatot, amelyek neve a következő: GatewaySubnet, FrontEnd és Backend. Az értékek behelyettesítésekor fontos, hogy az átjáróalhálózat neve mindenképp GatewaySubnet legyen. Ha ezt másként nevezi el, az átjáró létrehozása meghiúsul.

```powershell
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-active-active-mode"></a>2. lépés – a VPN-átjáró létrehozása TestVNet1 aktív-aktív üzemmódban
#### <a name="1-create-the-public-ip-addresses-and-gateway-ip-configurations"></a>1. A nyilvános IP-címek és átjáró IP-konfigurációk létrehozása
Kérelem osztható ki a Vnethez tartozó létrehozhat az átjáró két nyilvános IP-címeket. Az alhálózat és a szükséges IP-konfigurációk is fogja definiálni.

```powershell
$gw1pip1 = New-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$gw1pip2 = New-AzureRmPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1
$gw1ipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf2 -Subnet $subnet1 -PublicIpAddress $gw1pip2
```

#### <a name="2-create-the-vpn-gateway-with-active-active-configuration"></a>2. A VPN-átjáró aktív-aktív konfiguráció létrehozása
Hozza létre a TestVNet1 virtuális hálózati átjáróját. Vegye figyelembe, hogy két GatewayIpConfig bejegyzést is tartalmaz, és a EnableActiveActiveFeature jelző be van állítva. Az átjáró létrehozása akár 45 percet vagy hosszabb időt is igénybe vehet.

```powershell
New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1,$gw1ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN -EnableActiveActiveFeature -Debug
```

#### <a name="3-obtain-the-gateway-public-ip-addresses-and-the-bgp-peer-ip-address"></a>3. Az átjáró nyilvános IP-címek és a BGP-Társgép IP-cím beszerzése
Az átjáró létrehozása után szüksége lesz az Azure VPN Gateway a BGP-Társgép IP-cím beszerzése. Ez a cím konfigurálása az Azure VPN Gateway a BGP-partner a helyszíni VPN-eszközök esetében van szükség.

```powershell
$gw1pip1 = Get-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1
$gw1pip2 = Get-AzureRmPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
```

Az alábbi parancsmagok segítségével a VPN-átjáró és az egyes átjárópéldány megfelelő BGP-Társgép IP-címeit számára lefoglalt két nyilvános IP-címe:

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

A nyilvános IP sorrendjének szünteti meg a példány, és a megfelelő BGP társviszony-létesítés címek megegyeznek. Ebben a példában az átjáró nyilvános IP-címe 40.112.190.5 rendelkező virtuális gépet fog használni a BGP társviszony-létesítés cím 10.12.255.4, és az átjáró 138.91.156.129 10.12.255.5 fogja használni. Ezek az információk szükségesek a az aktív-aktív átjáró csatlakozik a helyi VPN-eszközök beállításához. Az átjáró összes címre alatt az ábrán látható:

![aktív-aktív átjáró](./media/vpn-gateway-activeactive-rm-powershell/active-active-gw.png)

Az átjáró létrehozása után az átjáró segítségével aktív-aktív létesítmények közötti és VNet – VNet-kapcsolatot létesíteni. A következő szakaszok végezze el a szükséges lépések a gyakorlatban.

## <a name ="aacrossprem"></a>2. rész – egy aktív-aktív létesítmények közötti kapcsolat
A létesítmények közötti kapcsolatot szüksége a helyszíni VPN-eszköz képviselő helyi hálózati átjáró és a kapcsolat az Azure VPN gateway kapcsolódni a helyi hálózati átjáró létrehozásához. Ebben a példában az Azure VPN gateway aktív-aktív módban van. Ennek eredményeképpen ellenére, hogy csak az egyiket a helyszíni VPN-eszközön (helyi hálózati átjáró) és egy kapcsolati erőforrást, mindkét Azure VPN gateway példányok fog létrehozni a S2S VPN-alagutat a helyszíni eszközök.

A folytatás előtt ellenőrizze, hogy befejeződött [1. rész](#aagateway) ebben a gyakorlatban.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>1. lépés – létrehozása és a helyi hálózati átjáró konfigurálása
#### <a name="1-declare-your-variables"></a>1. A változók deklarálása
Ebben a gyakorlatban továbbra is a konfiguráció az ábrán is látható. Ne felejtse el az értékeket olyanokra cserélni, amelyeket a saját konfigurációjához kíván használni.

```powershell
$RG5 = "TestAARG5"
$Location5 = "West US"
$LNGName51 = "Site5_1"
$LNGPrefix51 = "10.52.255.253/32"
$LNGIP51 = "131.107.72.22"
$LNGASN5 = 65050
$BGPPeerIP51 = "10.52.255.253"
```

Több helyi hálózati átjáró paraméterek kapcsolatban ügyeljen a következőkre:

* A helyi hálózati átjáró lehet az ugyanazon vagy másik helyen és az erőforráscsoport a VPN-átjáróként. Ez a példa bemutatja azokat a különböző erőforráscsoportokra, de az azonos Azure-hely.
* Ha csak egy helyszíni VPN-eszköz a fentiek szerint, az aktív-aktív kapcsolat együttműködhet, függetlenül a BGP protokollt. A példa BGP a létesítmények közötti kapcsolathoz.
* A BGP engedélyezve van, a előtagot kell deklarálni helyi hálózati átjáró esetén az állomás címe a VPN-eszköz a BGP-Társgép IP-cím. Egy /32 ebben az esetben a "10.52.255.253/32" előtaggal.
* Ne feledje a helyszíni hálózatokhoz és az Azure VNet közötti különböző BGP ASN kell használnia. Ha a egyeznek, a virtuális hálózat ASN módosításához, ha a helyszíni VPN-eszköz már használja az ASN a más BGP szomszédok egyenrangú szüksége.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. A helyi hálózati átjáró létrehozása Site5
Mielőtt folytatja, győződjön meg arról, hogy továbbra is csatlakozik az 1. előfizetéshez. Az erőforráscsoport létrehozása, ha az még nem jött létre.

```powershell
New-AzureRmResourceGroup -Name $RG5 -Location $Location5
New-AzureRmLocalNetworkGateway -Name $LNGName51 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP51 -AddressPrefix $LNGPrefix51 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP51
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>2. lépés – a virtuális hálózat átjáró és a helyi hálózati átjáró
#### <a name="1-get-the-two-gateways"></a>1. A két átjáró beolvasása

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw1 = Get-AzureRmLocalNetworkGateway  -Name $LNGName51 -ResourceGroupName $RG5
```

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. A TestVNet1 Site5 kapcsolat létrehozása
Ebben a lépésben hoz létre a kapcsolat TestVNet1 való Site5_1 a "EnableBGP" $True értékre.

```powershell
New-AzureRmVirtualNetworkGatewayConnection -Name $Connection151 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw1 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-on-premises-vpn-device"></a>3. A helyszíni VPN-eszköz VPN és BGP paraméterei
Az alábbi példa az ebben a gyakorlatban a helyszíni VPN-eszköz a BGP konfigurációs szakaszba módba lép paramétereket tartalmazza:

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

A kapcsolatot kell kialakítani, néhány perc múlva, és a BGP társviszony-létesítési munkamenetet IPsec-kapcsolat létrejötte után indul el. Ebben a példában, amennyiben csak egy helyszíni VPN-eszköz, ami azt eredményezi, az alábbi ábrán van beállítva:

![active-active-crossprem](./media/vpn-gateway-activeactive-rm-powershell/active-active.png)

### <a name="step-3---connect-two-on-premises-vpn-devices-to-the-active-active-vpn-gateway"></a>3. lépés - a két helyszíni VPN-eszközök csatlakozni az aktív-aktív VPN-átjáró
Ha két VPN-eszközök, a helyi hálózaton, kettős redundancia érhet el, a második VPN-eszköz az Azure VPN gatewayhez csatlakozó.

#### <a name="1-create-the-second-local-network-gateway-for-site5"></a>1. A második helyi hálózati átjáró létrehozása Site5
Az átjáró IP-címe, a címelőtagot és a BGP társviszony-létesítési címét, a második helyi hálózati átjáró nem fedhetik az előző helyi hálózati átjáró ugyanabban a helyszíni hálózat.

```powershell
$LNGName52 = "Site5_2"
$LNGPrefix52 = "10.52.255.254/32"
$LNGIP52 = "131.107.72.23"
$BGPPeerIP52 = "10.52.255.254"
```

```powershell
New-AzureRmLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP52 -AddressPrefix $LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP52
```

#### <a name="2-connect-the-vnet-gateway-and-the-second-local-network-gateway"></a>2. Csatlakoztassa a virtuális hálózat átjáró, a második helyi hálózati átjáró
Hozza létre a kapcsolatot TestVNet1 Site5_2 a "EnableBGP" $True értékre

```powershell
$lng5gw2 = Get-AzureRmLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5
```

```powershell
New-AzureRmVirtualNetworkGatewayConnection -Name $Connection152 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw2 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-second-on-premises-vpn-device"></a>3. A második a helyszíni VPN-eszköz VPN és BGP paraméterei
Ehhez hasonlóan listája alább a paraméterek megadja azokat a második VPN-eszköz:

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

Miután létrejött a kapcsolat (alagutak) is meg kell kettős redundáns VPN-eszközök és a helyszíni hálózat és az Azure csatlakozás alagutak:

![dual-redundancy-crossprem](./media/vpn-gateway-activeactive-rm-powershell/dual-redundancy.png)

## <a name ="aav2v"></a>3. rész – egy aktív-aktív VNet – VNet-kapcsolatot létesíteni
Ez a szakasz egy aktív-aktív VNet – VNet-kapcsolatot hoz létre a BGP. 

Az alábbi útmutató a fent leírt lépések folytatása. Meg kell adnia a [1. rész](#aagateway) létrehozása és konfigurálása a TestVNet1 és a VPN-átjáró BGP-hez. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>1. lépés – TestVNet2 és a VPN-átjáró létrehozása
Győződjön meg arról, hogy az új virtuális hálózat, TestVNet2, az IP-címtér nem fedi át a virtuális hálózat tartomány egyik fontos.

Ebben a példában a virtuális hálózatok ugyanahhoz az előfizetéshez tartozik. Beállíthatja a VNet – VNet kapcsolatokhoz különböző előfizetések; Tekintse meg [VNet – VNet-kapcsolatot konfiguráló](vpn-gateway-vnet-vnet-rm-ps.md) többet is megtudhat. Mindenképpen adja hozzá a "-EnableBgp $True" a BGP engedélyezéséhez kapcsolatok létrehozásakor.

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
New-AzureRmResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2
```

#### <a name="3-create-the-active-active-vpn-gateway-for-testvnet2"></a>3. Az aktív-aktív VPN-átjáró TestVNet2 létrehozása
Kérelem osztható ki a Vnethez tartozó létrehozhat az átjáró két nyilvános IP-címeket. Az alhálózat és a szükséges IP-konfigurációk is fogja definiálni.

```powershell
$gw2pip1 = New-AzureRmPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
$gw2pip2 = New-AzureRmPublicIpAddress -Name $GW2IPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2 = Get-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gw2ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1
$gw2ipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW2IPconf2 -Subnet $subnet2 -PublicIpAddress $gw2pip2
```

Hozzon létre a VPN-átjáró AS számát és a "EnableActiveActiveFeature" jelzőt. Vegye figyelembe, hogy az Azure VPN gatewayek kell bírálja felül az alapértelmezett ASN. Az ASN-eket a csatlakoztatott Vnetek a BGP és a tranzit Útválasztás engedélyezése különbözőnek kell lennie.

```powershell
New-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1,$gw2ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet2ASN -EnableActiveActiveFeature
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
Ebben a lépésben hoz létre a kapcsolat TestVNet1 és TestVNet2, és a kapcsolat a TestVNet2 TestVNet1 számára.

```powershell
New-AzureRmVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True
```

> [!IMPORTANT]
> Győződjön meg arról, a BGP engedélyezéséhez mindkét kapcsolatokhoz.
> 
> 

Társviszony-létesítési munkamenetet a kapcsolat néhány percet, és a BGP kell meghatározzák ezeket a lépéseket befejezését követően lesz mentése után a VNet – VNet kapcsolódást kettős redundanciával:

![active-active-v2v](./media/vpn-gateway-activeactive-rm-powershell/vnet-to-vnet.png)

## <a name ="aaupdate"></a>Egy meglévő VPN-átjáró frissítése

Ez a szakasz segítségével módosíthatja egy meglévő Azure VPN gateway aktív-készenléti mód aktív-aktív, vagy fordítva.

### <a name="change-an-active-standby-gateway-to-an-active-active-gateway"></a>Egy aktív-készenléti állapotban lévő átjárót egy aktív-aktív átjáró módosítása

A következő példa egy aktív-készenléti állapotban lévő átjáró alakít át egy aktív-aktív átjárót. Ha megváltoztatja egy aktív-aktív aktív-készenléti állapotban lévő átjárót, hozzon létre egy másik nyilvános IP-címet, majd adja hozzá a második átjáró IP-konfigurációja.

#### <a name="1-declare-your-variables"></a>1. A változók deklarálása

Cserélje le a példák a beállításokkal, hogy a saját konfigurációjához szükséges, majd deklarálja ezeket az értékeket a következő paramétereket.

```powershell
$GWName = "TestVNetAA1GW"
$VNetName = "TestVNetAA1"
$RG = "TestVPNActiveActive01"
$GWIPName2 = "gwpip2"
$GWIPconf2 = "gw1ipconf2"
```

Után deklaráló a változókat, másolja, és illessze be az ebben a példában a PowerShell-konzolba.

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gw = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$location = $gw.Location
```

#### <a name="2-create-the-public-ip-address-then-add-the-second-gateway-ip-configuration"></a>2. A nyilvános IP-cím létrehozása, majd adja hozzá a második átjáró IP-konfiguráció

```powershell
$gwpip2 = New-AzureRmPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG -Location $location -AllocationMethod Dynamic
Add-AzureRmVirtualNetworkGatewayIpConfig -VirtualNetworkGateway $gw -Name $GWIPconf2 -Subnet $subnet -PublicIpAddress $gwpip2
```

#### <a name="3-enable-active-active-mode-and-update-the-gateway"></a>3. Aktív-aktív mód engedélyezése és az átjáró frissítése

Ebben a lépésben aktív-aktív mód engedélyezése, és frissítheti az átjárót. A példában a VPN-átjáró jelenleg használja egy örökölt Standard Termékváltozat. Aktív-aktív azonban nem támogatja a Standard Termékváltozat. (Ebben az esetben az HighPerformance) támogat a hagyományos SKU átméretezéséhez, egyszerűen adja meg a támogatott régi SKU használni kívánt.

* Az új SKU használata ebben a lépésben egy örökölt Termékváltozat nem módosítható. Csak egy örökölt SKU támogatott örökölt egy másikra is átméretezhetők. Például nem módosíthatja a Termékváltozat szabványos VpnGw1 való (annak ellenére, hogy aktív-aktív támogatott VpnGw1) mert Standard örökölt SKU, és VpnGw1 aktuális Termékváltozat. Átméretezési és áttelepítése termékváltozatok kapcsolatos további információkért lásd: [Gateway SKU-n](vpn-gateway-about-vpngateways.md#gwsku).

* Ha azt szeretné, méretezze át a jelenlegi SKU, például a VpnGw3, VpnGw1 ezt teheti használatával ezt a lépést, mert a azonos SKU-család szerepelnek a termékváltozat. Ehhez használja az értéket:```-GatewaySku VpnGw3```

Használata esetén ez a környezetben, ha nincs szüksége az átjáró átméretezése, nincs szükség a - GatewaySku adja meg. Figyelje meg, hogy ebben a lépésben meg kell adnia az átjáróobjektum PowerShell, a tényleges frissítés indításához. A frissítés eltarthat 30 – 45 perc, akkor is, ha nem átméretezni az átjáró.

```powershell
Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -EnableActiveActiveFeature -GatewaySku HighPerformance
```

### <a name="change-an-active-active-gateway-to-an-active-standby-gateway"></a>Egy aktív-aktív átjárót egy aktív-készenléti állapotban lévő átjáró módosítása
#### <a name="1-declare-your-variables"></a>1. A változók deklarálása

Cserélje le a példák a beállításokkal, hogy a saját konfigurációjához szükséges, majd deklarálja ezeket az értékeket a következő paramétereket.

```powershell
$GWName = "TestVNetAA1GW"
$RG = "TestVPNActiveActive01"
```

A változók deklaráló, után el az eltávolítani kívánt IP-konfiguráció neve.

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$ipconfname = $gw.IpConfigurations[1].Name
```

#### <a name="2-remove-the-gateway-ip-configuration-and-disable-the-active-active-mode"></a>2. Távolítsa el az átjáró IP-konfiguráció és az aktív-aktív mód letiltása

Ez a példa segítségével távolítsa el az átjáró IP-konfiguráció, és aktív-aktív mód letiltása. Figyelje meg, hogy meg kell adnia az átjáróobjektum PowerShell, a tényleges frissítés indításához.

```powershell
Remove-AzureRmVirtualNetworkGatewayIpConfig -Name $ipconfname -VirtualNetworkGateway $gw
Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -DisableActiveActiveFeature
```

A frissítés 45 percig is eltarthat 30-ig.

## <a name="next-steps"></a>További lépések
Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. A lépésekért lásd: [Virtuális gép létrehozása](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).