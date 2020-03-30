---
title: IPsec/IKE-házirend S2S VPN-& virtuális hálózat között létesítő kapcsolatokhoz
titleSuffix: Azure VPN Gateway
description: Konfigurálja az IPsec/IKE-házirendet S2S- vagy VNet-to-VNet-kapcsolatokhoz az Azure VPN-átjárókkal az Azure Resource Manager és a PowerShell használatával.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/14/2018
ms.author: yushwang
ms.openlocfilehash: eaca48fc354f1cf37635e9729b04eaaaa882ba1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77161902"
---
# <a name="configure-ipsecike-policy-for-s2s-vpn-or-vnet-to-vnet-connections"></a>IPsec/IKE-szabályzat S2S VPN- vagy VNet–VNet-kapcsolatokhoz történő konfigurálása

Ez a cikk bemutatja az IPsec/IKE-házirend konfigurálásának lépéseit a helyek közötti VPN- vagy vnet-ről vnet-kapcsolatokra az Erőforrás-kezelő telepítési modell és a PowerShell használatával.



## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a><a name="about"></a>Az Azure VPN-átjárók IPsec- és IKE-házirendparamétereinek
Az IPsec és az IKE protokollszabvány a kriptográfiai algoritmusok széles skáláját támogatja különböző kombinációkban. A [Kriptográfiai követelmények és az Azure VPN-átjárók témakörben megtudhatja,](vpn-gateway-about-compliance-crypto.md) hogy ez hogyan segíthet ily mértékben biztosítani a létesítmények közötti és a virtuális hálózat közötti kapcsolatot a megfelelőségi vagy biztonsági követelményeknek.

Ez a cikk az IPsec/IKE-házirend ek létrehozásához és konfigurálásához, valamint egy új vagy meglévő kapcsolatra vonatkozó utasításokat tartalmaz:

* [1. rész – Munkafolyamat az IPsec/IKE-házirend létrehozásához és beállításához](#workflow)
* [2. rész - Támogatott kriptográfiai algoritmusok és kulcserősségek](#params)
* [3. rész – Új S2S VPN-kapcsolat létrehozása IPsec/IKE-házirenddel](#crossprem)
* [4. rész – Új virtuális hálózat és virtuális hálózat között létesített kapcsolat IPsec/IKE-házirenddel](#vnet2vnet)
* [5. rész – IPsec/IKE-házirend kezelése (létrehozása, hozzáadása, eltávolítása)](#managepolicy)

> [!IMPORTANT]
> 1. Ne feledje, hogy az IPsec/IKE házirend csak a következő átjáró-adatbázisokkal működik:
>    * ***VpnGw1, VpnGw2, VpnGw3*** (útvonal-alapú)
>    * ***Standard*** és ***HighPerformance*** (útvonal-alapú)
> 2. Egy adott kapcsolathoz csak ***egy*** házirendet adhat meg.
> 3. Az IKE (alapmódú) és az IPsec (gyorsmód) összes algoritmusát és paraméterét meg kell adnia. A részleges házirend-megadás nem engedélyezett.
> 4. A helyszíni VPN-eszközök en a vpn-eszköz gyártójának specifikációival kapcsolatban győződjön meg arról, hogy a házirend támogatott a helyszíni VPN-eszközökön. Az S2S- vagy vnet-es kapcsolatok nem tudják megállapítani, hogy a házirendek nem kompatibilisek-e.

## <a name="part-1---workflow-to-create-and-set-ipsecike-policy"></a><a name ="workflow"></a>1. rész – Munkafolyamat az IPsec/IKE-házirend létrehozásához és beállításához
Ez a szakasz az IPsec/IKE-házirend S2S VPN- vagy VNet-vnet-kapcsolaton való létrehozásához és frissítéséhez vezető munkafolyamatot ismerteti:
1. Virtuális hálózat és VPN-átjáró létrehozása
2. Helyi hálózati átjáró létrehozása a telephelyek közötti kapcsolathoz, vagy egy másik virtuális hálózat és átjáró a virtuális hálózat és a virtuális hálózat közötti kapcsolathoz
3. IPsec/IKE-házirend létrehozása kijelölt algoritmusokkal és paraméterekkel
4. Kapcsolat (IPsec vagy VNet2VNet) létrehozása az IPsec/IKE-házirenddel
5. IPsec/IKE-házirend hozzáadása/frissítése/eltávolítása meglévő kapcsolathoz

Az ebben a cikkben található utasítások segítségével az ábrán látható IPsec/IKE-házirendek beállítását és konfigurálását segíti:

![ipsec-ike-politika](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)

## <a name="part-2---supported-cryptographic-algorithms--key-strengths"></a><a name ="params"></a>2. rész - Támogatott kriptográfiai algoritmusok & kulcserősségek

Az alábbi táblázat az ügyfelek által konfigurálható támogatott kriptográfiai algoritmusokat és kulcserősségeket sorolja fel:

| **IPsec/IKEv2**  | **Beállítások**    |
| ---  | --- 
| IKEv2-titkosítás | AES256, AES192, AES128, DES3, DES  
| IKEv2-integritás  | SHA384, MD5, SHA1, SHA256  |
| DH-csoport         | DHGroup24, ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, None |
| IPsec-titkosítás | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, Nincs    |
| IPsec-integritás  | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5 |
| PFS-csoport        | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, Nincs 
| Gyorsmódú biztonsági társítás élettartama   | (**Nem kötelező**: az alapértelmezett értékeket használja, ha nincs megadva)<br>Másodperc (egész szám; **min. 300**/alapértelmezett érték: 27000 másodperc)<br>KB (egész szám; **min. 1024**/alapértelmezett érték: 102400000 KB)   |
| Forgalomválasztó | UsePolicyBasedTrafficSelectors** ($True/$False; **Nem kötelező**, alapértelmezett $False ha nincs megadva)    |
|  |  |

> [!IMPORTANT]
> 1. **A helyszíni VPN-eszköz konfigurációjának meg kell egyezniük velük, vagy tartalmazniuk kell az alábbi, az Azure IPsec/IKE-házirendben megadott algoritmusokat és paramétereket:**
>    * IKE titkosítási algoritmus (alapmód / 1. fázis)
>    * IKE integritásalgoritmus (alapmód / 1. fázis)
>    * DH-csoport (alapmód / 1. fázis)
>    * IPsec titkosítási algoritmus (gyors mód / 2. fázis)
>    * IPsec integritásalgoritmus (gyors mód / 2. fázis)
>    * PFS-csoport (gyorsmód / 2. fázis)
>    * Forgalomválasztó (usePolicyBasedTrafficselectors használata esetén)
>    * AZ SA-k élettartamai helyi szinten definiáltak, így azoknak nem kell megegyezniük.
>
> 2. **Ha a GCMAES-t az IPsec titkosítási algoritmushoz használja, ugyanazt a GCMAES algoritmust és kulcshosszt kell választania az IPsec-integritáshoz; például a GCMAES128 használata mindkét**
> 3. A fenti táblázatban:
>    * Az IKEv2 megfelel az alapmódnak vagy az 1.
>    * Az IPsec megfelel a gyorsmódnak vagy a 2.
>    * A DH-csoport meghatározza az alapmódban vagy az 1.
>    * A PFS Group a Quick Mode vagy a Phase 2
> 4. Az IKEv2 fő módú biztonsági hozzárendelés élettartama 28 800 másodpercen van rögzítve az Azure VPN-átjárókon
> 5. Ha a "UsePolicyBasedTrafficSelectors" beállítást $True egy kapcsolatra, az Azure VPN-átjárót úgy állítja be, hogy a helyszínen a házirendalapú VPN-tűzfalhoz csatlakozzon. Ha engedélyezi a PolicyBasedTrafficSelectors, meg kell győződnie arról, hogy a VPN-eszköz rendelkezik a megfelelő forgalom választók a helyszíni hálózat (helyi hálózati átjáró) előtagok összes kombinációi az Azure virtuális hálózati előtagok, ahelyett, hogy minden-hoz-any. Például ha a helyszíni hálózati előtagok a 10.1.0.0/16 és a 10.2.0.0/16, a virtuális hálózati előtagok pedig 192.168.0.0/16 és 172.16.0.0/16, az alábbi forgalomválasztókat kell megadnia:
>    * 10.1.0.0/16 <====> 192.168.0.0/16
>    * 10.1.0.0/16 <====> 172.16.0.0/16
>    * 10.2.0.0/16 <====> 192.168.0.0/16
>    * 10.2.0.0/16 <====> 172.16.0.0/16

A házirend-alapú forgalomválasztókkal kapcsolatos további információkért [lásd: Több helyszíni házirend-alapú VPN-eszköz csatlakoztatása.](vpn-gateway-connect-multiple-policybased-rm-ps.md)

Az alábbi táblázat az egyéni házirend által támogatott diffie-hellman csoportokat sorolja fel:

| **Diffie-Hellman Group**  | **DH-csoport**              | **PFS-csoport** | **A kulcs hossza** |
| --- | --- | --- | --- |
| 1                         | DHGroup1                 | PFS1         | 768 bites MODP   |
| 2                         | DHGroup2                 | PFS2         | 1024 bites MODP  |
| 14                        | DHGroup14<br>DHGroup2048 | PFS2048      | 2048 bites MODP  |
| 19                        | ECP256                   | ECP256       | 256 bites ECP    |
| 20                        | ECP384                   | ECP384       | 384 bites ECP    |
| 24                        | DHGroup24                | PFS24        | 2048 bites MODP  |

További részletekért lásd: [RFC3526](https://tools.ietf.org/html/rfc3526) és [RFC5114](https://tools.ietf.org/html/rfc5114).

## <a name="part-3---create-a-new-s2s-vpn-connection-with-ipsecike-policy"></a><a name ="crossprem"></a>3. rész – Új S2S VPN-kapcsolat létrehozása IPsec/IKE-házirenddel

Ez a szakasz végigvezeti az S2S VPN-kapcsolat IPsec/IKE-házirenddel történő létrehozásának lépésein. A következő lépések az ábrán látható módon hozzák létre a kapcsolatot:

![s2s-politika](./media/vpn-gateway-ipsecikepolicy-rm-powershell/s2spolicy.png)

Az [S2S VPN-kapcsolat](vpn-gateway-create-site-to-site-rm-powershell.md) létrehozásáról további részletes útmutatást az S2S VPN-kapcsolat létrehozásáról című S2S VPN-kapcsolat című témakörben talál.

### <a name="before-you-begin"></a><a name="before"></a>Mielőtt elkezdené

* Győződjön meg arról, hogy rendelkezik Azure-előfizetéssel. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).
* Telepítse az Azure Resource Manager PowerShell-parancsmagjait. A PowerShell-parancsmagok telepítéséről az [Azure PowerShell áttekintése című](/powershell/azure/overview) témakörben olvashat bővebben.

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a><a name="createvnet1"></a>1. lépés - A virtuális hálózat, a VPN-átjáró és a helyi hálózati átjáró létrehozása

#### <a name="1-declare-your-variables"></a>1. Deklarálja a változókat

Ehhez a gyakorlathoz kezdjük a változóink deklarálásával. Az éles konfigurációhoz ne felejtse el ezeket az értékeket a saját értékeire cserélni.

```powershell
$Sub1          = "<YourSubscriptionName>"
$RG1           = "TestPolicyRG1"
$Location1     = "East US 2"
$VNetName1     = "TestVNet1"
$FESubName1    = "FrontEnd"
$BESubName1    = "Backend"
$GWSubName1    = "GatewaySubnet"
$VNetPrefix11  = "10.11.0.0/16"
$VNetPrefix12  = "10.12.0.0/16"
$FESubPrefix1  = "10.11.0.0/24"
$BESubPrefix1  = "10.12.0.0/24"
$GWSubPrefix1  = "10.12.255.0/27"
$DNS1          = "8.8.8.8"
$GWName1       = "VNet1GW"
$GW1IPName1    = "VNet1GWIP1"
$GW1IPconf1    = "gw1ipconf1"
$Connection16  = "VNet1toSite6"

$LNGName6      = "Site6"
$LNGPrefix61   = "10.61.0.0/16"
$LNGPrefix62   = "10.62.0.0/16"
$LNGIP6        = "131.107.72.22"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Csatlakozás az előfizetéshez, és hozzon létre egy új erőforráscsoportot

A Resource Manager parancsmagjainak használatához váltson át PowerShell módba. További információ: [A Windows PowerShell használata a Resource Managerrel](../powershell-azure-resource-manager.md).

Nyissa meg a PowerShell konzolt, és csatlakozzon a fiókjához. A következő minta segíthet a kapcsolódásban:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>3. Hozza létre a virtuális hálózatot, a VPN-átjárót és a helyi hálózati átjárót

A következő minta létrehozza a virtuális hálózatot, a TestVNet1-et három alhálózattal és a VPN-átjáróval. Az értékek behelyettesítésekor fontos, hogy az átjáróalhálózat neve mindenképp GatewaySubnet legyen. Ha ezt másként nevezi el, az átjáró létrehozása meghiúsul.

```powershell
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1    = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1      = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1    = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1

New-AzLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix $LNGPrefix61,$LNGPrefix62
```

### <a name="step-2---create-a-s2s-vpn-connection-with-an-ipsecike-policy"></a><a name="s2sconnection"></a>2. lépés – S2S VPN-kapcsolat létrehozása IPsec/IKE-házirenddel

#### <a name="1-create-an-ipsecike-policy"></a>1. IPsec/IKE-házirend létrehozása

A következő mintaparancsfájl ipsec/IKE-házirendet hoz létre a következő algoritmusokkal és paraméterekkel:

* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA256, PFS None, SA Élettartam 14400 másodperc & 102400000KB

```powershell
$ipsecpolicy6 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

Ha GCMAES-t használ az IPsec-hez, ugyanazt a GCMAES algoritmust és kulcshosszt kell használnia az IPsec titkosításához és integritásához. A fenti érték például a GCMAES256 használatakor a megfelelő paraméterek az "-IpsecEncryption GCMAES256 -IpsecIntegrity GCMAES256" lesz.

#### <a name="2-create-the-s2s-vpn-connection-with-the-ipsecike-policy"></a>2. Az S2S VPN-kapcsolat létrehozása az IPsec/IKE házirenddel

Hozzon létre egy S2S VPN-kapcsolatot, és alkalmazza a korábban létrehozott IPsec/IKE házirendet.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng6 = Get-AzLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
```

Tetszés szerint hozzáadhatja a "-UsePolicyBasedTrafficSelectors $True" parancsot a létrehozási csatlakozási parancsmaghoz, hogy az Azure VPN-átjáró a fent leírtak szerint a helyszínen lévő házirendalapú VPN-eszközökhöz csatlakozzon.

> [!IMPORTANT]
> Miután egy IPsec/IKE-szabályzat meg van adva egy kapcsolaton, az Azure VPN-átjáró csak az IPsec/IKE-javaslatot küldi el vagy fogadja el a megadott kriptográfiai algoritmusokkal és az adott kapcsolat kulcserősségeivel. Győződjön meg arról, hogy a helyszíni VPN-eszköz a kapcsolat használja, vagy elfogadja a pontos házirend-kombináció, ellenkező esetben az S2S VPN-alagút nem hoz létre.


## <a name="part-4---create-a-new-vnet-to-vnet-connection-with-ipsecike-policy"></a><a name ="vnet2vnet"></a>4. rész – Új virtuális hálózat és virtuális hálózat között létesített kapcsolat IPsec/IKE-házirenddel

Az IPsec/IKE-házirenddel létesített virtuális hálózat és virtuális kapcsolat létrehozásának lépései hasonlóak az S2S VPN-kapcsolathoz. A következő mintaparancsfájlok hozzák létre a kapcsolatot az ábrán látható módon:

![v2v-politika](./media/vpn-gateway-ipsecikepolicy-rm-powershell/v2vpolicy.png)

Virtuális [hálózat és virtuális hálózat között kapcsolat létrehozása](vpn-gateway-vnet-vnet-rm-ps.md) című témakörben további lépéseket tartalmaz a virtuális hálózat és virtuális hálózat között lévő kapcsolatok létrehozásához. A TestVNet1 és a VPN-átjáró létrehozásához és konfigurálásához ki kell töltenie a [3.](#crossprem)

### <a name="step-1---create-the-second-virtual-network-and-vpn-gateway"></a><a name="createvnet2"></a>1. lépés - A második virtuális hálózat és VPN-átjáró létrehozása

#### <a name="1-declare-your-variables"></a>1. Deklarálja a változókat

Ne felejtse el az értékeket olyanokra cserélni, amelyeket a saját konfigurációjához kíván használni.

```powershell
$RG2          = "TestPolicyRG2"
$Location2    = "East US 2"
$VNetName2    = "TestVNet2"
$FESubName2   = "FrontEnd"
$BESubName2   = "Backend"
$GWSubName2   = "GatewaySubnet"
$VNetPrefix21 = "10.21.0.0/16"
$VNetPrefix22 = "10.22.0.0/16"
$FESubPrefix2 = "10.21.0.0/24"
$BESubPrefix2 = "10.22.0.0/24"
$GWSubPrefix2 = "10.22.255.0/27"
$DNS2         = "8.8.8.8"
$GWName2      = "VNet2GW"
$GW2IPName1   = "VNet2GWIP1"
$GW2IPconf1   = "gw2ipconf1"
$Connection21 = "VNet2toVNet1"
$Connection12 = "VNet1toVNet2"
```

#### <a name="2-create-the-second-virtual-network-and-vpn-gateway-in-the-new-resource-group"></a>2. A második virtuális hálózat és VPN-átjáró létrehozása az új erőforráscsoportban

```powershell
New-AzResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2

$gw2pip1    = New-AzPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
$vnet2      = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2    = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gw2ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1

New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance
```

### <a name="step-2---create-a-vnet-tovnet-connection-with-the-ipsecike-policy"></a>2. lépés – Virtuális hálózat-toVNet-kapcsolat létrehozása az IPsec/IKE-házirenddel

Az S2S VPN-kapcsolathoz hasonlóan hozzon létre egy IPsec/IKE-házirendet, majd alkalmazza a házirendet az új kapcsolatra.

#### <a name="1-create-an-ipsecike-policy"></a>1. IPsec/IKE-házirend létrehozása

Az alábbi mintaszkript egy eltérő IPsec/IKE-szabályzatot hoz létre a következő algoritmusokkal és paraméterekkel:
* IKEv2: AES128, SHA1, DHGroup14
* IPsec: GCMAES128, GCMAES128, PFS14, SA Élettartam 14400 másodperc & 10240000KB

```powershell
$ipsecpolicy2 = New-AzIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption GCMAES128 -IpsecIntegrity GCMAES128 -PfsGroup PFS14 -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

#### <a name="2-create-vnet-to-vnet-connections-with-the-ipsecike-policy"></a>2. Virtuálishálózat és virtuális hálózat között létesítő kapcsolatok létrehozása az IPsec/IKE házirenddel

Hozzon létre egy virtuális hálózat-vnet-kapcsolat, és alkalmazza a létrehozott IPsec/IKE házirendet. Ebben a példában mindkét átjáró ugyanabban az előfizetésben van. Így lehetőség van mindkét kapcsolat létrehozása és konfigurálása ugyanazzal a PowerShell-házirenddel ugyanabban a PowerShell-munkamenetben.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$vnet2gw = Get-AzVirtualNetworkGateway -Name $GWName2  -ResourceGroupName $RG2

New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'
```

> [!IMPORTANT]
> Miután egy IPsec/IKE-szabályzat meg van adva egy kapcsolaton, az Azure VPN-átjáró csak az IPsec/IKE-javaslatot küldi el vagy fogadja el a megadott kriptográfiai algoritmusokkal és az adott kapcsolat kulcserősségeivel. Győződjön meg arról, hogy mindkét kapcsolat IPsec-házirendjei azonosak, ellenkező esetben a virtuális hálózat és a virtuális hálózat kapcsolata nem fog létre.

A lépések végrehajtása után a kapcsolat néhány perc alatt létrejön, és a következő hálózati topológiája lesz, ahogy az az elején látható:

![ipsec-ike-politika](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)


## <a name="part-5---update-ipsecike-policy-for-a-connection"></a><a name ="managepolicy"></a>5. rész – Az IPsec/IKE házirend frissítése egy kapcsolathoz

Az utolsó szakasz bemutatja, hogyan kezelheti az IPsec/IKE-házirendet egy meglévő S2S vagy VNet-vnet kapcsolathoz. Az alábbi gyakorlat végigvezeti a következő műveleteken egy kapcsolaton:

1. Kapcsolat IPsec/IKE házirendjének megjelenítése
2. Az IPsec/IKE-házirend hozzáadása vagy frissítése kapcsolathoz
3. Az IPsec/IKE házirend eltávolítása a kapcsolatból

Ugyanezek a lépések vonatkoznak az S2S és a virtuális hálózat-vnet kapcsolatok ra is.

> [!IMPORTANT]
> Az IPsec/IKE-házirend csak *a Standard* és *HighPerformance* útvonalalapú VPN-átjárókon támogatott. Nem működik az alapszintű átjáró termékváltozaton vagy a házirend-alapú VPN-átjárón.

#### <a name="1-show-the-ipsecike-policy-of-a-connection"></a>1. Kapcsolat IPsec/IKE házirendjének megjelenítése

A következő példa bemutatja, hogyan lehet az IPsec/IKE-házirend konfigurálása egy kapcsolaton. A szkriptek is folytatódik a fenti gyakorlatokat.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Az utolsó parancs felsorolja a kapcsolaton konfigurált aktuális IPsec/IKE házirendet, ha van ilyen. A következő egy mintakimenet a kapcsolathoz:

```powershell
SALifeTimeSeconds   : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption     : AES256
IpsecIntegrity      : SHA256
IkeEncryption       : AES256
IkeIntegrity        : SHA384
DhGroup             : DHGroup24
PfsGroup            : PFS24
```

Ha nincs konfigurálva IPsec/IKE házirend, a parancs (PS> $connection6. IpsecPolicies) üres visszatérést kap. Ez nem jelenti azt, hogy az IPsec/IKE nincs konfigurálva a kapcsolaton, de nincs egyéni IPsec/IKE-házirend. A tényleges kapcsolat a helyszíni VPN-eszköz és az Azure VPN-átjáró között egyeztetett alapértelmezett szabályzatot használja.

#### <a name="2-add-or-update-an-ipsecike-policy-for-a-connection"></a>2. IPsec/IKE-házirend hozzáadása vagy frissítése kapcsolathoz

Az új házirend hozzáadásának vagy egy meglévő házirend frissítésének lépései megegyeznek: hozzon létre egy új házirendet, majd alkalmazza az új házirendet a kapcsolatra.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$newpolicy6   = New-AzIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6
```

Ha engedélyezni szeretné a "UsePolicyBasedTrafficSelectors" paraméteres házirend-alapú VPN-eszközhöz való csatlakozást, adja hozzá a "-UsePolicyBaseTrafficSelectors" paramétert a parancsmaghoz, vagy állítsa $False a beállítás letiltásához:

```powershell
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6 -UsePolicyBasedTrafficSelectors $True
```

A kapcsolat újra leellenőrizheti, hogy a házirend frissült-e.

```powershell
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Az utolsó sor kimenetét az alábbi példában látható módon kell látnia:

```powershell
SALifeTimeSeconds   : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption     : AES256
IpsecIntegrity      : SHA256
IkeEncryption       : AES128
IkeIntegrity        : SHA1
DhGroup             : DHGroup14
PfsGroup            : None
```

#### <a name="3-remove-an-ipsecike-policy-from-a-connection"></a>3. IPsec/IKE-házirend eltávolítása a kapcsolatból

Miután eltávolította az egyéni szabályzatot egy kapcsolatból, az Azure VPN-átjáró visszaáll az [IPsec/IKE-javaslatok alapértelmezett listájára,](vpn-gateway-about-vpn-devices.md) és újratárgyalja újra a helyszíni VPN-eszközzel.

```powershell
$RG1           = "TestPolicyRG1"
$Connection16  = "VNet1toSite6"
$connection6   = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$currentpolicy = $connection6.IpsecPolicies[0]
$connection6.IpsecPolicies.Remove($currentpolicy)

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6
```

Ugyanazzal a parancsfájllal ellenőrizheti, hogy a házirendet eltávolították-e a kapcsolatból.

## <a name="next-steps"></a>További lépések

A házirend-alapú forgalomválasztókkal kapcsolatos további részletekért tekintse [meg a Több helyszíni házirend-alapú VPN-eszközök csatlakoztatása](vpn-gateway-connect-multiple-policybased-rm-ps.md) című témakört.

Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. A lépésekért lásd: [Virtuális gép létrehozása](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
