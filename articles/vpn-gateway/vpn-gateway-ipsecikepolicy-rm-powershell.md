---
title: 'S2S VPN- és VNet – VNet kapcsolatokhoz IPsec/IKE-házirend konfigurálása: Az Azure Resource Manager: PowerShell |} A Microsoft Docs'
description: IPsec/IKE-szabályzat S2S- és VNet – VNet kapcsolatokhoz konfigurálása az Azure Resource Manager és a PowerShell használatával az Azure VPN-átjárókkal.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: 238cd9b3-f1ce-4341-b18e-7390935604fa
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: yushwang
ms.openlocfilehash: 72c597a6258fbe43e718714ab346d3e10cb97463
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2019
ms.locfileid: "56417261"
---
# <a name="configure-ipsecike-policy-for-s2s-vpn-or-vnet-to-vnet-connections"></a>IPsec/IKE-szabályzat S2S VPN- és VNet – VNet kapcsolatokhoz konfigurálása

Ez a cikk végigvezeti a Resource Manager üzemi modell és a PowerShell használatával, a Site-to-Site VPN- vagy VNet – VNet kapcsolatokhoz IPsec/IKE-házirend konfigurálásáról.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="about"></a>Azure VPN gatewayek IPsec és az IKE szabályzat paraméterekről
Szabványos IPsec és az IKE protokoll különböző kombinációival titkosítási algoritmusok széles skáláját támogatja. Tekintse meg [titkosítási követelményeket és az Azure VPN-átjárókkal kapcsolatos](vpn-gateway-about-compliance-crypto.md) megtekintéséhez segítségre biztosítása a létesítmények közötti és VNet – VNet kapcsolat megfelelnek a megfelelőségi és biztonsági követelmények.

Ez a cikk útmutatást hozhat létre és a egy IPsec/IKE-házirend konfigurálása és a egy új vagy meglévő kapcsolatot a alkalmazni:

* [1. rész – a munkafolyamat létrehozása és IPsec/IKE-szabályzat beállítása](#workflow)
* [2. rész – támogatott titkosítási algoritmusokat és kulcserősségeket](#params)
* [3. rész – új S2S VPN-kapcsolat IPsec/IKE-szabályzat létrehozása](#crossprem)
* [4. lépés – az új VNet – VNet kapcsolat IPsec/IKE-szabályzat létrehozása](#vnet2vnet)
* [5. rész – kezelése (létrehozása, hozzáadása, eltávolítása)-kapcsolat IPsec/IKE-szabályzat](#managepolicy)

> [!IMPORTANT]
> 1. Vegye figyelembe, hogy IPsec/IKE-házirend csak a következő átjáró SKU-k a működik:
>    * ***VpnGw1, VpnGw2, VpnGw3*** (útvonalalapú)
>    * ***Standard szintű*** és ***HighPerformance*** (útvonalalapú)
> 2. Egy adott kapcsolathoz csak ***egy*** házirendet adhat meg.
> 3. Meg kell adnia mind az IKE (elsődleges mód), mind az IPsec (gyors mód) minden algoritmust és paramétert. A részleges házirend-megadás nem engedélyezett.
> 4. Tekintse meg a VPN-eszköz szállítói annak érdekében, hogy a helyszíni VPN-eszközök az szabályzat támogatott előírásoknak való. S2S vagy VNet – VNet kapcsolatokhoz a nem létesíthető, ha a házirendek nem kompatibilis.

## <a name ="workflow"></a>1. rész – a munkafolyamat létrehozása és IPsec/IKE-szabályzat beállítása
Ez a szakasz ismerteti a munkafolyamat létrehozása és a egy S2S VPN- vagy VNet – VNet kapcsolaton IPsec/IKE-szabályzat frissítése:
1. Virtuális hálózat és VPN-átjáró létrehozása
2. Hozzon létre egy helyi hálózati átjárója helyi kapcsolat, vagy egy másik virtuális hálózat közötti és VNet – VNet kapcsolat átjáró
3. Hozzon létre egy IPsec/IKE-házirendet a kijelölt algoritmusokkal és paraméterekkel
4. Hozzon létre egy kapcsolatot (IPsec vagy VNet2VNet) az IPsec/IKE-házirend
5. Adjon hozzá/frissítéséhez/eltávolításához egy létező kapcsolat egy IPsec/IKE-szabályzat

A jelen cikkben lévő utasítások segítségével beállíthatja és konfigurálhatja az IPsec/IKE-szabályzatok az ábrán látható módon:

![ipsec-ike-policy](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)

## <a name ="params"></a>2. rész – támogatott titkosítási algoritmusokat és kulcserősségeket

Az alábbi táblázat a támogatott titkosítási algoritmusokat és kulcserősségeket konfigurálható az ügyfelek által:

| **IPsec/IKEv2**  | **Beállítások**    |
| ---  | --- 
| IKEv2-titkosítás | AES256, AES192, AES128, DES3, DES  
| IKEv2-integritás  | SHA384, MD5, SHA1, SHA256  |
| DH-csoport         | DHGroup24, ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, None |
| IPsec-titkosítás | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, Nincs    |
| IPsec-integritás  | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5 |
| PFS-csoport        | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, Nincs 
| Gyorsmódú biztonsági társítás élettartama   | (**Nem kötelező**: alapértelmezett értékek vannak használt Ha nincs megadva)<br>Másodperc (egész szám; **min. 300**/alapértelmezett érték: 27000 másodperc)<br>KB (egész szám; **min. 1024**/alapértelmezett érték: 102400000 KB)   |
| Forgalomválasztó | UsePolicyBasedTrafficSelectors ** ($True vagy $False; **Nem kötelező**, alapértelmezett $False, ha nincs megadva)    |
|  |  |

> [!IMPORTANT]
> 1. **A helyszíni VPN-eszköz konfigurációjának kell egyeznie vagy tartalmazza a következő algoritmusokat és paramétereket, az Azure IPsec/IKE-házirend meg kell adnia:**
>    * IKE titkosítási algoritmus (elsődleges mód / 1. fázis)
>    * IKE integritási algoritmus (elsődleges mód / 1. fázis)
>    * DH-csoport (alapmódú / 1. fázis)
>    * IPsec titkosítási algoritmus (gyors mód / 2. fázis)
>    * IPsec integritási algoritmus (gyors mód / 2. fázis)
>    * PFS-csoport (gyors mód / 2. fázis)
>    * Forgalom-választó (ha UsePolicyBasedTrafficSelectors használatos)
>    * AZ SA-k élettartamai helyi szinten definiáltak, így azoknak nem kell megegyezniük.
>
> 2. **GCMAES IPsec titkosítási algoritmus meghajtóbetűjeleket használja, ha választania kell a ugyanazt a GCMAES-algoritmust és kulcshosszt az IPsec-integritás; például mind a GCMAES128 használatával**
> 3. A fenti táblázatban:
>    * Az IKEv2 fő módú vagy 1. fázis felel meg
>    * IPsec gyors mód vagy a 2. fázis felel meg
>    * DH-csoport megadja a Diffie-Hellmen használt fő módban vagy az 1. fázis
>    * PFS-csoport megadva, a gyors mód vagy a 2. fázis használt Diffie-Hellmen csoport
> 4. Az IKEv2 fő módú biztonsági hozzárendelés élettartama 28 800 másodpercen van rögzítve az Azure VPN-átjárókon
> 5. "UsePolicyBasedTrafficSelectors" beállítás $true kapcsolaton keresztül csatlakozni a helyszíni házirendalapú VPN tűzfal az Azure VPN gateway konfigurálja. Ha engedélyezi az PolicyBasedTrafficSelectors, kell a VPN-eszköz ne legyen a megfelelő forgalomválasztóinak definiált minden kombinációja a helyszíni hálózati és-tárolókról az Azure virtuális hálózati előtagok (helyi hálózati átjáró) előtagok helyett bármely. Például ha a helyszíni hálózati előtagok a 10.1.0.0/16 és a 10.2.0.0/16, a virtuális hálózati előtagok pedig 192.168.0.0/16 és 172.16.0.0/16, az alábbi forgalomválasztókat kell megadnia:
>    * 10.1.0.0/16 <====> 192.168.0.0/16
>    * 10.1.0.0/16 <====> 172.16.0.0/16
>    * 10.2.0.0/16 <====> 192.168.0.0/16
>    * 10.2.0.0/16 <====> 172.16.0.0/16

Szabályzatalapú forgalomválasztóinak kapcsolatos további információkért lásd: [több helyszíni házirendalapú VPN-eszköz csatlakoztatása](vpn-gateway-connect-multiple-policybased-rm-ps.md).

A következő táblázat felsorolja a megfelelő, az egyéni házirend által támogatott Diffie-Hellman csoport:

| **Diffie-Hellman csoport**  | **DH-csoport**              | **PFS-csoport** | **A kulcs hossza** |
| --- | --- | --- | --- |
| 1                         | DHGroup1                 | PFS1         | 768 bites MODP   |
| 2                         | DHGroup2                 | PFS2         | 1024 bites MODP  |
| 14                        | DHGroup14<br>DHGroup2048 | PFS2048      | 2048 bites MODP  |
| 19                        | ECP256                   | ECP256       | 256 bites ECP    |
| 20                        | ECP384                   | ECP284       | 384 bites ECP    |
| 24                        | DHGroup24                | PFS24        | 2048 bites MODP  |

További részletekért lásd: [RFC3526](https://tools.ietf.org/html/rfc3526) és [RFC5114](https://tools.ietf.org/html/rfc5114).

## <a name ="crossprem"></a>3. rész – új S2S VPN-kapcsolat IPsec/IKE-szabályzat létrehozása

Ez a szakasz végigvezeti a lépéseken, egy IPsec/IKE-szabályzat S2S VPN-kapcsolat létrehozásával. Az alábbi lépéseket a kapcsolat létrehozásához, az ábrán látható módon:

![s2s-policy](./media/vpn-gateway-ipsecikepolicy-rm-powershell/s2spolicy.png)

Lásd: [S2S VPN-kapcsolat létrehozása](vpn-gateway-create-site-to-site-rm-powershell.md) részletesebb részletes útmutató egy S2S VPN-kapcsolat létrehozásához.

### <a name="before"></a>Előkészületek

* Győződjön meg arról, hogy rendelkezik Azure-előfizetéssel. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).
* Telepítse az Azure Resource Manager PowerShell-parancsmagokat. Lásd: [áttekintése az Azure PowerShell](/powershell/azure/overview) a PowerShell-parancsmagok telepítéséről további információt.

### <a name="createvnet1"></a>1. lépés – a virtuális hálózat, a VPN-átjáró és a helyi hálózati átjáró létrehozása

#### <a name="1-declare-your-variables"></a>1. A változók deklarálása

Ehhez a gyakorlathoz módszertan változók deklarálásával. Az éles konfigurációhoz ne felejtse el ezeket az értékeket a saját értékeire cserélni.

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

#### <a name="3-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>3. A virtuális hálózat, a VPN-átjáró és a helyi hálózati átjáró létrehozása

Az alábbi minta létrehoz a virtuális hálózat, a testvnet1-et, és három alhálózatot, és a VPN-átjáró. Az értékek behelyettesítésekor fontos, hogy az átjáróalhálózat neve mindenképp GatewaySubnet legyen. Ha ezt másként nevezi el, az átjáró létrehozása meghiúsul.

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

### <a name="s2sconnection"></a>2. lépés – egy IPsec/IKE-szabályzat S2S VPN-kapcsolat létrehozása

#### <a name="1-create-an-ipsecike-policy"></a>1. Egy IPsec/IKE-szabályzat létrehozása

Az alábbi mintaparancsfájl egy IPsec/IKE-házirendet hoz létre a következő algoritmusokat és paramétereket:

* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA256, PFS None, SA élettartama 14400 másodperc és 102400000KB

```powershell
$ipsecpolicy6 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

GCMAES használja az IPsec esetében, ha az IPsec-titkosítás és integritásának a ugyanazt a GCMAES-algoritmust és kulcshosszt kell használnia. Például újabb, a megfelelő paramétereket lesz "-IpsecEncryption GCMAES256 - IpsecIntegrity GCMAES256" GCMAES256 használatakor.

#### <a name="2-create-the-s2s-vpn-connection-with-the-ipsecike-policy"></a>2. Az IPsec/IKE-szabályzat S2S VPN-kapcsolat létrehozása

Hozzon létre S2S VPN-kapcsolatot, és a alkalmazni a korábban létrehozott IPsec/IKE-házirendet.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng6 = Get-AzLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
```

Azt is megteheti "-UsePolicyBasedTrafficSelectors $True" a létrehozás kapcsolat parancsmaghoz szeretne csatlakozni a házirendalapú VPN-eszközök a helyszínen, az Azure VPN gateway engedélyezéséhez a fent leírtak szerint.

> [!IMPORTANT]
> Miután egy IPsec/IKE-házirendet egy kapcsolathoz van megadva, az Azure VPN gateway csak küldjön, vagy elfogadja az IPsec/IKE-megadott titkosítási algoritmusokkal és kulcserősségekkel, hogy a kapcsolat a. Győződjön meg arról, hogy a helyszíni VPN-eszköz kapcsolat használ, vagy fogadja el a pontos szabályzat együttes használata, ellenkező esetben nem jön az S2S VPN-alagúton.


## <a name ="vnet2vnet"></a>4. lépés – az új VNet – VNet kapcsolat IPsec/IKE-szabályzat létrehozása

Az IPsec/IKE szabályzat VNet – VNet kapcsolat létrehozásának lépésein hasonlóak az S2S VPN-kapcsolatot. Az alábbi minta parancsfájlok hozza létre a kapcsolatot, az ábrán látható módon:

![v2v-policy](./media/vpn-gateway-ipsecikepolicy-rm-powershell/v2vpolicy.png)

Lásd: [VNet – VNet kapcsolat létrehozása](vpn-gateway-vnet-vnet-rm-ps.md) részletes lépéseit a virtuális hálózatok közötti kapcsolat létrehozásához. Meg kell adnia a [3. rész](#crossprem) hozhat létre és konfigurálja a TestVNet1 és a VPN-átjárót.

### <a name="createvnet2"></a>1. lépés – a második virtuális hálózat és VPN-átjáró létrehozása

#### <a name="1-declare-your-variables"></a>1. A változók deklarálása

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

#### <a name="2-create-the-second-virtual-network-and-vpn-gateway-in-the-new-resource-group"></a>2. A második virtuális hálózat és VPN-átjáró az új erőforráscsoport létrehozása

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

### <a name="step-2---create-a-vnet-tovnet-connection-with-the-ipsecike-policy"></a>2. lépés: hozzon létre egy VNet-toVNet-kapcsolatot az IPsec/IKE-házirend

Hasonló a S2S VPN-kapcsolatot egy IPsec/IKE-szabályzat létrehozása, majd a alkalmazni a szabályzatot az új kapcsolat.

#### <a name="1-create-an-ipsecike-policy"></a>1. Egy IPsec/IKE-szabályzat létrehozása

Az alábbi mintaszkript egy eltérő IPsec/IKE-szabályzatot hoz létre a következő algoritmusokkal és paraméterekkel:
* IKEv2: AES128, SHA1, DHGroup14
* IPsec: GCMAES128, GCMAES128, PFS14, 102400000KB & SA élettartama 14400 másodperc

```powershell
$ipsecpolicy2 = New-AzIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption GCMAES128 -IpsecIntegrity GCMAES128 -PfsGroup PFS14 -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

#### <a name="2-create-vnet-to-vnet-connections-with-the-ipsecike-policy"></a>2. Az IPsec/IKE-szabályzat létrehozása a VNet – VNet kapcsolatokhoz

Hozzon létre egy virtuális hálózatok közötti kapcsolatot, és a alkalmazni a létrehozott IPsec/IKE-szabályzat. Ebben a példában mindkét átjáró ugyanabban az előfizetésben vannak. Így lehetséges, létrehozása és konfigurálása mindkét kapcsolat ugyanaz a IPsec/IKE-szabályzat, a PowerShell-munkamenetben.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$vnet2gw = Get-AzVirtualNetworkGateway -Name $GWName2  -ResourceGroupName $RG2

New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'
```

> [!IMPORTANT]
> Miután egy IPsec/IKE-házirendet egy kapcsolathoz van megadva, az Azure VPN gateway csak küldjön, vagy elfogadja az IPsec/IKE-megadott titkosítási algoritmusokkal és kulcserősségekkel, hogy a kapcsolat a. Ellenőrizze, hogy az IPsec-házirendek kapcsolatok azonosak, ellenkező esetben a VNet – VNet kapcsolat nem jön létre.

Ezek a lépések elvégzése után létrejön a kapcsolat néhány perc múlva, és kell a következő hálózati topológia elején látható módon:

![ipsec-ike-policy](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)


## <a name ="managepolicy"></a>5. rész – kapcsolat frissítés IPsec/IKE-szabályzat

Utolsó szakasza bemutatja, hogyan kezelheti az IPsec/IKE-házirendet egy létező S2S vagy VNet – VNet kapcsolat. Az alábbi gyakorlat végigvezeti a kapcsolat a következő műveleteket:

1. Az IPsec/IKE szabályzat a kapcsolatok megjelenítése
2. Az IPsec/IKE szabályzat hozzáadásakor vagy a kapcsolatot
3. Az IPsec/IKE-házirendet eltávolítja a kapcsolat

Ugyanazokat a lépéseket az S2S- és VNet – VNet-kapcsolatok vonatkozik.

> [!IMPORTANT]
> A támogatott IPsec/IKE-házirend *Standard* és *HighPerformance* útvonalalapú VPN-átjárók csak. Az alapszintű átjáró-Termékváltozatot vagy a csoportházirend-alapú VPN-átjáró nem működik.

#### <a name="1-show-the-ipsecike-policy-of-a-connection"></a>1. Az IPsec/IKE szabályzat a kapcsolatok megjelenítése

Az alábbi példa bemutatja a IPsec/IKE-szabályzat konfigurálva a kapcsolat. A parancsfájlok is a fenti gyakorlatok folytatása.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Az utolsó parancs az aktuális IPsec/IKE-házirend konfigurálása a kapcsolaton, sorolja fel, ha vannak ilyenek. Az alábbiakban látható egy mintakimenet a kapcsolat:

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

Ha nincs az IPsec/IKE-szabályzat konfigurálva, a parancs (PS > $connection6.policy) egy üres visszatérési beolvasása. Ez nem jelenti azt, IPsec/IKE a kapcsolat nincs konfigurálva, azonban, hogy nincs egyéni IPsec/IKE-szabályzat nem létezik. A tényleges kapcsolati használja az alapértelmezett házirendet, a helyszíni VPN-eszköz és az Azure VPN-átjáró között.

#### <a name="2-add-or-update-an-ipsecike-policy-for-a-connection"></a>2. Kapcsolat egy IPsec/IKE szabályzat hozzáadásakor vagy

Adjon hozzá egy új házirendet, vagy egy kapcsolat egy meglévő szabályzat frissítése szükséges lépések megegyeznek: hozzon létre egy új szabályzatot, majd a alkalmazni az új szabályzat a kapcsolatot.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$newpolicy6   = New-AzIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6
```

Egy helyszíni házirendalapú VPN-eszközhöz való csatlakozáskor a "usepolicybasedtrafficselectors paraméterre" engedélyezéséhez vegye fel a "-UsePolicyBaseTrafficSelectors" paramétert a parancsmaghoz, vagy állítsa az értékét $False a beállítás letiltása:

```powershell
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6 -UsePolicyBasedTrafficSelectors $True
```

A kapcsolat ismét ellenőrizze, hogy ha a házirendet kérheti le.

```powershell
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Az utolsó sort kimenete az alábbi példában látható módon kell megjelennie:

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

#### <a name="3-remove-an-ipsecike-policy-from-a-connection"></a>3. A kapcsolat egy IPsec/IKE-házirend eltávolítása

Miután eltávolítja a kapcsolat egyéni házirendjét, az Azure VPN-átjáró visszaáll a [IPsec/IKE-javaslatok alapértelmezett listáját](vpn-gateway-about-vpn-devices.md) és újbóli a egyeztetést végez a helyszíni VPN-eszköz az újra.

```powershell
$RG1           = "TestPolicyRG1"
$Connection16  = "VNet1toSite6"
$connection6   = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$currentpolicy = $connection6.IpsecPolicies[0]
$connection6.IpsecPolicies.Remove($currentpolicy)

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6
```

Használhatja ugyanazt a parancsprogramot, ha a házirendet el lett távolítva a kapcsolat ellenőrzése.

## <a name="next-steps"></a>További lépések

Lásd: [több helyszíni házirendalapú VPN-eszköz csatlakoztatása](vpn-gateway-connect-multiple-policybased-rm-ps.md) szabályzatalapú forgalomválasztóinak további részletei.

Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. A lépésekért lásd: [Virtuális gép létrehozása](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
