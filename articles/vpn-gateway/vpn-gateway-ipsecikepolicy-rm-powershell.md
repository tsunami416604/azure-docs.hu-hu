---
title: "S2S VPN- és VNet – VNet kapcsolatokhoz IPsec/IKE-házirend konfigurálása: Azure Resource Manager: PowerShell |} Microsoft Docs"
description: "S2S és VNet – VNet kapcsolatokhoz IPsec/IKE-házirend konfigurálása az Azure VPN Gatewayek Azure Resource Manager és a PowerShell használatával."
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
ms.date: 02/14/2018
ms.author: yushwang
ms.openlocfilehash: 19233ccd306f507ef2e36bee878aa9705c115780
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="configure-ipsecike-policy-for-s2s-vpn-or-vnet-to-vnet-connections"></a>S2S VPN- és VNet – VNet kapcsolatokhoz IPsec/IKE-házirend konfigurálása

Ez a cikk végigvezeti a telephelyek közötti VPN- és VNet – VNet kapcsolatokhoz, a Resource Manager üzembe helyezési modellben és a PowerShell használatával IPsec/IKE-házirendet konfigurálhat.

## <a name="about"></a>Az Azure VPN gatewayek IPsec és az internetes KULCSCSERE házirend paraméterek
Standard IPsec és IKE protokoll titkosítási algoritmusok számos különböző kombinációkban támogatja. Tekintse meg [kriptográfiai követelményeiről és az Azure VPN gatewayek](vpn-gateway-about-compliance-crypto.md) hogyan Ez segítségére lehet biztosítása létesítmények közötti és VNet – VNet-kapcsolatot megfelelnek a megfelelőségi és biztonsági.

Ez a cikk bemutatja, és hozhat létre és IPsec/IKE-szabályzat beállítása egy új vagy meglévő kapcsolat alkalmazható:

* [1 - munkafolyamat létrehozása és IPsec/IKE házirend beállítása. rész](#workflow)
* [2. rész - támogatott titkosítási algoritmusok és a kulcs szintjeiről](#params)
* [3. rész – hozzon létre egy új S2S VPN-kapcsolat IPsec/IKE-házirend](#crossprem)
* [Rész 4 – hozzon létre egy új VNet – VNet-kapcsolatot IPsec/IKE-házirend](#vnet2vnet)
* [5 - rész kezelése (létrehozása, hozzáadása, eltávolítása) kapcsolat IPsec/IKE-házirend](#managepolicy)

> [!IMPORTANT]
> 1. Vegye figyelembe, hogy IPsec/IKE házirend csak akkor működik a a következő átjáró-termékváltozat:
>    * ***VpnGw1, VpnGw2, VpnGw3*** (útválasztó-alapú)
>    * ***Standard*** és ***HighPerformance*** (útválasztó-alapú)
> 2. Egy adott kapcsolathoz csak ***egy*** házirendet adhat meg.
> 3. Meg kell adnia a internetes KULCSCSERE (alapmód) és a IPsec (gyorsmódú) algoritmusok és a paraméterek. A részleges házirend-megadás nem engedélyezett.
> 4. Vegye fel a kapcsolatot VPN szállító műszaki győződjön meg arról, a házirend a helyszíni VPN-eszközök esetén támogatott. S2S vagy a VNet – VNet kapcsolatokhoz a nem tud, ha a házirendek nem kompatibilisek.

## <a name ="workflow">1 - munkafolyamat létrehozása és IPsec/IKE házirend beállítása. rész</a>
Ez a szakasz ismerteti a munkafolyamat létrehozásához, és a S2S VPN- vagy a VNet – VNet kapcsolat IPsec/IKE-házirend frissítése:
1. Virtuális hálózat és VPN-átjáró létrehozása
2. Helyi hálózati átjáró a helyi kapcsolat vagy egy másik virtuális hálózati közötti és VNet – VNet-kapcsolatot az átjáró létrehozása
3. Hozzon létre egy IPsec/IKE házirendet a kijelölt algoritmusok és paraméterek
4. Az IPsec/IKE házirendet (IPsec vagy VNet2VNet) kapcsolat létrehozása
5. Frissítés/hozzáadása egy meglévő kapcsolat az IPsec/IKE házirend

A jelen cikkben lévő utasítások segít beállítása és konfigurálása IPsec/IKE házirendek az ábrán látható módon:

![ipsec-ike-policy](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)

## <a name ="params"></a>2. rész - támogatott titkosítási algoritmusok és a kulcs szintjeiről

Az alábbi táblázat a támogatott titkosítási algoritmusok és a kulcs szintjeiről konfigurálható az ügyfelek:

| **IPsec/IKEv2**  | **Beállítások**    |
| ---  | --- 
| IKEv2-titkosítás | AES256, AES192, AES128, DES3, DES  
| IKEv2-integritás  | SHA384, MD5, SHA1, SHA256  |
| DH-csoport         | DHGroup24, ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, None |
| IPsec-titkosítás | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, Nincs    |
| IPsec-integritás  | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5 |
| PFS-csoport        | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, Nincs 
| Gyorsmódú biztonsági társítás élettartama   | (**Nem kötelező**: alapértelmezett értékek vannak használt Ha nincs megadva)<br>Másodperc (egész szám; **min. 300**/alapértelmezett érték: 27000 másodperc)<br>KB (egész szám; **min. 1024**/alapértelmezett érték: 102400000 KB)   |
| Forgalomválasztó | UsePolicyBasedTrafficSelectors ** ($True vagy $False; **Nem kötelező**, alapértelmezett $False Ha nincs megadva)    |
|  |  |

> [!IMPORTANT]
> 1. **A helyszíni VPN-eszköz konfigurációjában kell felel meg, vagy a következő algoritmusokat és paraméterek meg az Azure IPsec/IKE házirend tartalmazza:**
>    * IKE titkosítási algoritmus (alapmódú / 1. fázis)
>    * IKE integritási algoritmus (alapmódú / 1. fázis)
>    * DH-csoport (alapmódú / 1. fázis)
>    * IPsec titkosítási algoritmus (gyorsmódú / 2. szakasza)
>    * IPsec-integritási algoritmus (gyorsmódú / 2. szakasza)
>    * PFS-csoport (gyorsmódú / 2. fázis)
>    * A választó forgalom (ha UsePolicyBasedTrafficSelectors használatos)
>    * AZ SA-k élettartamai helyi szinten definiáltak, így azoknak nem kell megegyezniük.
>
> 2. **GCMAES mint IPsec titkosítási algoritmus használata esetén ki kell választania a azonos GCMAES algoritmus és a kulcshossz IPsec sértetlenségét; például mind a GCMAES128 használatával**
> 3. A fenti táblázatban:
>    * IKEv2 felel meg az alapmód vagy 1. fázis
>    * IPsec gyorsmódú vagy a 2. szakasza felel meg
>    * DH-csoport megadja a Diffie-Hellmen alapmódú vagy 1. fázis
>    * PFS csoport megadva, a Diffie-Hellmen csoport gyorsmódú vagy a 2. fázis
> 4. Az IKEv2 fő módú biztonsági hozzárendelés élettartama 28 800 másodpercen van rögzítve az Azure VPN-átjárókon
> 5. $True "UsePolicyBasedTrafficSelectors" beállítást a kapcsolat konfigurálja az Azure VPN gateway házirendalapú VPN-tűzfal a helyszíni való kapcsolódáshoz. Ha engedélyezi a PolicyBasedTrafficSelectors, szeretné-e ellenőrizze a megfelelő forgalmat választók meghatározott összes kiegészítve a helyszíni hálózati belőle az Azure-beli virtuális hálózat előtagok (helyi hálózati átjáró) előtagok ahelyett, hogy rendelkezik-e a VPN-eszköz bármely elem közöttiként. Például ha a helyszíni hálózati előtagok a 10.1.0.0/16 és a 10.2.0.0/16, a virtuális hálózati előtagok pedig 192.168.0.0/16 és 172.16.0.0/16, az alábbi forgalomválasztókat kell megadnia:
>    * 10.1.0.0/16 <====> 192.168.0.0/16
>    * 10.1.0.0/16 <====> 172.16.0.0/16
>    * 10.2.0.0/16 <====> 192.168.0.0/16
>    * 10.2.0.0/16 <====> 172.16.0.0/16

Csoportházirend-alapú forgalom választók kapcsolatos további információkért lásd: [csatlakozás több helyszíni házirendalapú VPN-eszközök](vpn-gateway-connect-multiple-policybased-rm-ps.md).

A következő táblázat felsorolja a megfelelő Diffie-Hellman csoport, az egyéni házirend által támogatott:

| **Diffie-Hellman csoport**  | **DH-csoport**              | **PFS-csoport** | **A kulcs hossza** |
| --- | --- | --- | --- |
| 1                         | DHGroup1                 | PFS1         | 768 bites MODP   |
| 2                         | DHGroup2                 | PFS2         | 1024 bites MODP  |
| 14                        | DHGroup14<br>DHGroup2048 | PFS2048      | 2048 bites MODP  |
| 19                        | ECP256                   | ECP256       | 256 bites ECP    |
| 20                        | ECP384                   | ECP284       | 384 bites ECP    |
| 24                        | DHGroup24                | PFS24        | 2048 bites MODP  |

További részletekért lásd: [RFC3526](https://tools.ietf.org/html/rfc3526) és [RFC5114](https://tools.ietf.org/html/rfc5114).

## <a name ="crossprem">3. rész – hozzon létre egy új S2S VPN-kapcsolat IPsec/IKE-házirend</a>

Ez a szakasz bemutatja, hogyan hozzon létre egy S2S VPN-kapcsolatot az IPsec/IKE házirendjével. Az alábbi lépéseket a kapcsolat létrehozása, az ábrán látható módon:

![s2s-házirend](./media/vpn-gateway-ipsecikepolicy-rm-powershell/s2spolicy.png)

Lásd: [S2S VPN-kapcsolatot](vpn-gateway-create-site-to-site-rm-powershell.md) részletesebb lépésenkénti S2S VPN-kapcsolat létrehozásához.

### <a name="before"></a>Előkészületek

* Győződjön meg arról, hogy rendelkezik Azure-előfizetéssel. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).
* Az Azure Resource Manager PowerShell-parancsmagjainak telepítése. Lásd: [áttekintés az Azure PowerShell](/powershell/azure/overview) a PowerShell-parancsmagok telepítéséről további információt.

### <a name="createvnet1"></a>1. lépés – a virtuális hálózat, a VPN-átjáró és a helyi hálózati átjáró létrehozása

#### <a name="1-declare-your-variables"></a>1. A változók deklarálása

Ehhez a gyakorlathoz először is deklarálni kell a változókat. Az éles konfigurációhoz ne felejtse el ezeket az értékeket a saját értékeire cserélni.

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
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>3. A virtuális hálózat, a VPN-átjáró és a helyi hálózati átjáró létrehozása

Az alábbi minta létrehoz a virtuális hálózat, TestVNet1 három alhálózatokon, és a VPN-átjáró. Az értékek behelyettesítésekor fontos, hogy az átjáróalhálózat neve mindenképp GatewaySubnet legyen. Ha ezt másként nevezi el, az átjáró létrehozása meghiúsul.

```powershell
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1    = New-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1      = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1    = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1

New-AzureRmLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix $LNGPrefix61,$LNGPrefix62
```

### <a name="s2sconnection"></a>2. lépés - az IPsec/IKE házirendjével S2S VPN-kapcsolat létrehozása

#### <a name="1-create-an-ipsecike-policy"></a>1. IPsec/IKE-házirend létrehozása

Az alábbi mintaparancsfájl házirendet hoz létre IPsec/IKE a következő algoritmusokat és a Paraméterek:

* IKEv2: AES256, SHA384 DHGroup24
* IPsec: AES256, SHA-256, nincs PFS, SA élettartama 14400 másodperc & 102400000KB

```powershell
$ipsecpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

IPsec GCMAES használja, ha kell használnia az azonos GCMAES algoritmus és a kulcshossz IPsec titkosításhoz és sértetlenségét. Például újabb verziók esetén a megfelelő paramétereket lesz "-IpsecEncryption GCMAES256 - IpsecIntegrity GCMAES256" GCMAES256 használatakor.

#### <a name="2-create-the-s2s-vpn-connection-with-the-ipsecike-policy"></a>2. Az S2S VPN-kapcsolat létrehozása az IPsec/IKE irányelvnek

S2S VPN-kapcsolat létrehozásához, és alkalmazza a korábban létrehozott IPsec/IKE-házirendet.

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng6 = Get-AzureRmLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
```

Opcionálisan hozzáadhat "-UsePolicyBasedTrafficSelectors $True" a létrehozás kapcsolat parancsmagnak Azure VPN-átjáró házirendalapú VPN-eszközök a helyszínen, csatlakozni engedélyezése a fent leírt módon.

> [!IMPORTANT]
> Miután egy IPsec-/ h.rend kapcsolat van megadva, az Azure VPN gateway csak elküldi vagy elfogadja a IPsec/IKE-a megadott titkosítási algoritmusok és a kulcs szintjeiről a adott kapcsolat. Győződjön meg arról, hogy a kapcsolat a helyszíni VPN-eszköz használ, vagy fogadja el a pontos házirend kombináció, ellenkező esetben az S2S VPN-alagút fog létrehozni a.


## <a name ="vnet2vnet">Rész 4 – hozzon létre egy új VNet – VNet-kapcsolatot IPsec/IKE-házirend</a>

Hozzon létre egy VNet – VNet-kapcsolatot az IPsec/IKE házirendjével hasonlóak az S2S VPN-kapcsolatot. A következő minta parancsfájlokat hozza létre a kapcsolat, az ábrán látható módon:

![v2v-házirend](./media/vpn-gateway-ipsecikepolicy-rm-powershell/v2vpolicy.png)

Lásd: [VNet – VNet-kapcsolatot](vpn-gateway-vnet-vnet-rm-ps.md) részletes lépéseket a VNet – VNet-kapcsolat létrehozásához. Meg kell adnia a [3. rész](#crossprem) létrehozása és TestVNet1 és a VPN-átjáró konfigurálása.

### <a name="createvnet2"></a>1. lépés – a második virtuális hálózat és a VPN-átjáró létrehozása

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

#### <a name="2-create-the-second-virtual-network-and-vpn-gateway-in-the-new-resource-group"></a>2. A második virtuális hálózat és a VPN-átjárót az új erőforráscsoport létrehozása

```powershell
New-AzureRmResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2

$gw2pip1    = New-AzureRmPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
$vnet2      = Get-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2    = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gw2ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1

New-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance
```

### <a name="step-2---create-a-vnet-tovnet-connection-with-the-ipsecike-policy"></a>2. lépés - a VNet-toVNet kapcsolatot létrehozni az IPsec/IKE-házirend

Hasonló a S2S VPN-kapcsolat IPsec/IKE-házirend létrehozása, akkor a házirend az új kapcsolat alapján alkalmazza.

#### <a name="1-create-an-ipsecike-policy"></a>1. IPsec/IKE-házirend létrehozása

Az alábbi mintaparancsfájl egy másik IPsec/IKE-házirendet hoz létre a következő algoritmusokat és a Paraméterek:
* IKEv2: Az AES128, SHA1, DHGroup14
* IPsec: GCMAES128, GCMAES128, PFS14, 102400000KB & SA élettartama 14400 másodperc

```powershell
$ipsecpolicy2 = New-AzureRmIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption GCMAES128 -IpsecIntegrity GCMAES128 -PfsGroup PFS14 -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

#### <a name="2-create-vnet-to-vnet-connections-with-the-ipsecike-policy"></a>2. VNet – VNet kapcsolatokhoz a IPsec/IKE-házirend létrehozása

VNet – VNet-kapcsolatot, és a létrehozott IPsec/IKE házirend alkalmazása. Ebben a példában két átjáró ugyanahhoz az előfizetéshez vannak. Így a létrehozása és konfigurálása mindkét kapcsolatok az ugyanazon IPsec/IKE-házirendet a PowerShell-munkamenetben.

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$vnet2gw = Get-AzureRmVirtualNetworkGateway -Name $GWName2  -ResourceGroupName $RG2

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'
```

> [!IMPORTANT]
> Miután egy IPsec-/ h.rend kapcsolat van megadva, az Azure VPN gateway csak elküldi vagy elfogadja a IPsec/IKE-a megadott titkosítási algoritmusok és a kulcs szintjeiről a adott kapcsolat. Ellenőrizze, hogy az IPsec-házirendek mindkét kapcsolatok megegyeznek, ellenkező esetben a VNet – VNet-kapcsolatot nem fogja létrehozni.

A lépések elvégzése után a kapcsolat néhány perc múlva, és a következő hálózati topológia fog, ahogy az a kezdő:

![ipsec-ike-policy](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)


## <a name ="managepolicy"></a>Rész 5 - kapcsolat frissítés IPsec/IKE-házirend

Az utolsó szakasza bemutatja, hogyan meglévő S2S vagy VNet – VNet kapcsolat IPsec/IKE házirendjének kezeléséhez. Az alábbiakban a gyakorlatban végigvezeti a kapcsolat a következő műveleteket:

1. Az IPsec/IKE házirend kapcsolódási megjelenítése
2. Szabályzat hozzáadásakor vagy módosításakor a IPsec/IKE kapcsolathoz
3. Távolítsa el az IPsec/IKE-házirendet a kapcsolatot

Ugyanezek a lépések S2S és a VNet – VNet kapcsolatokhoz vonatkozik.

> [!IMPORTANT]
> IPsec-/ h.rend támogatott *szabványos* és *HighPerformance* csak VPN-átjárók útválasztó-alapú. Az alapszintű átjáró-Termékváltozat vagy a csoportházirend-alapú VPN-átjáró nem működik.

#### <a name="1-show-the-ipsecike-policy-of-a-connection"></a>1. Az IPsec/IKE házirend kapcsolódási megjelenítése

A következő példa bemutatja, hogyan beolvasni a kapcsolat konfigurált IPsec/IKE-szabályzatot. A parancsfájlok továbbra is a fenti gyakorlatokat.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Az utolsó parancs megjeleníti az aktuális IPsec/IKE-házirendet, a kapcsolat konfigurálva, ha van ilyen. A következő egy minta kimenet a kapcsolat látható:

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

Ha nincs IPsec/IKE szabályzat konfigurálva, a parancs (PS > $connection6.policy) egy üres visszatérési lekérdezi. IPsec/IKE a kapcsolat nincs konfigurálva, azonban, hogy nincs-e egyéni IPsec/IKE házirend nem jelenti. A tényleges kapcsolat használja az alapértelmezett házirendet, a helyszíni VPN-eszköz és az Azure VPN gateway között.

#### <a name="2-add-or-update-an-ipsecike-policy-for-a-connection"></a>2. A kapcsolat egy IPsec/IKE szabályzat hozzáadásakor vagy módosításakor

Adjon hozzá egy új házirendet, vagy a kapcsolat egy meglévő házirend frissítése lépései megegyeznek: hozzon létre egy új szabályzatot, akkor alkalmazza az új szabályzat a kapcsolatra.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$newpolicy6   = New-AzureRmIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6
```

A helyi csoportházirend-alapú VPN-eszközön való csatlakozáskor "UsePolicyBasedTrafficSelectors" engedélyezéséhez vegye fel a "-UsePolicyBaseTrafficSelectors" paramétert a parancsmaghoz, vagy állítsa az értékét $False a beállítás letiltása:

```powershell
Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6 -UsePolicyBasedTrafficSelectors $True
```

A kapcsolat újra kereséséhez frissül, ha a házirend kérheti le.

```powershell
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

A kimenet utolsó sora, a következő példában látható módon kell megjelennie:

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

#### <a name="3-remove-an-ipsecike-policy-from-a-connection"></a>3. Távolítsa el az IPsec/IKE házirendet a kapcsolatot

Amennyiben a kapcsolat az egyéni házirendet eltávolítja, az Azure VPN gateway visszavált a [alapértelmezett IPsec/IKE javaslatok listájának](vpn-gateway-about-vpn-devices.md) és újbóli a egyeztetést végez, a helyszíni VPN-eszköz újra.

```powershell
$RG1           = "TestPolicyRG1"
$Connection16  = "VNet1toSite6"
$connection6   = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$currentpolicy = $connection6.IpsecPolicies[0]
$connection6.IpsecPolicies.Remove($currentpolicy)

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6
```

Ellenőrizze, hogy ha a házirend el lett távolítva a kapcsolati használhatja ugyanazt a parancsfájlt.

## <a name="next-steps"></a>További lépések

Lásd: [csatlakozás több helyszíni házirendalapú VPN-eszközök](vpn-gateway-connect-multiple-policybased-rm-ps.md) csoportházirend-alapú forgalom választók kapcsolatos további részletekért.

Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. A lépésekért lásd: [Virtuális gép létrehozása](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).