---
title: 'Azure VPN-átjáró: Átjárók csatlakoztatása több helyszíni házirend-alapú VPN-eszközhöz'
description: Konfiguráljon egy Azure-útvonal-alapú VPN-átjárót több házirendalapú VPN-eszközre az Azure Resource Manager és a PowerShell használatával.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: yushwang
ms.openlocfilehash: 687c33e50a986cf8af08d0201fe0159a79cf02a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123318"
---
# <a name="connect-azure-vpn-gateways-to-multiple-on-premises-policy-based-vpn-devices-using-powershell"></a>Azure VPN-átjárók csatlakoztatása több helyszíni házirend-alapú VPN-eszközhöz a PowerShell használatával

Ez a cikk segít konfigurálni egy Azure útvonal-alapú VPN-átjáró, hogy csatlakozzon több helyszíni házirend-alapú VPN-eszközök kihasználva az egyéni IPsec/IKE-házirendek S2S VPN-kapcsolatokon.

## <a name="about-policy-based-and-route-based-vpn-gateways"></a><a name="about"></a>Házirend- és útvonalalapú VPN-átjárók –

A házirendalapú *és* az útvonalalapú VPN-eszközök különböznek attól, ahogyan az IPsec-forgalom választók be vannak állítva egy kapcsolaton:

* **Politikaalapú** A VPN-eszközök mindkét hálózat előtagok kombinációját használják a forgalom IPsec-alagutakon keresztüli titkosításának/visszafejtésének meghatározásához. Általában csomagszűrést végző tűzfaleszközökre épül. Az IPsec-alagút titkosítási és visszafejtési műveletei hozzáadódnak a csomagok szűrését és feldolgozását végző motorhoz.
* **Útvonal-alapú** A VPN-eszközök bármilyen (helyettesítő) forgalomválasztót használnak, és lehetővé teszik, hogy az útválasztási/továbbítási táblák irányítsák a forgalmat a különböző IPsec-alagutakba. Általában olyan útválasztó platformokra épül, ahol minden IPsec-alagút hálózati adapterként vagy VTI-ként (virtuális alagút-összeköttetésként) van modellezve.

Az alábbi ábrák kiemelik a két modellt:

### <a name="policy-based-vpn-example"></a>Példa házirendalapú VPN-re
![politikaalapú](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedmultisite.png)

### <a name="route-based-vpn-example"></a>Útvonalalapú VPN-példa
![útvonal-alapú](./media/vpn-gateway-connect-multiple-policybased-rm-ps/routebasedmultisite.png)

### <a name="azure-support-for-policy-based-vpn"></a>Azure-támogatás a házirendalapú VPN-hez
Jelenleg az Azure támogatja a VPN-átjárók mindkét módját: az útvonalalapú VPN-átjárókat és a házirend-alapú VPN-átjárókat. Különböző belső platformokra épülnek, amelyek különböző specifikációkat eredményeznek:

|                          | **Házirendalapú VPN-átjáró** | **Útvonalalapú VPN-átjáró**       |**Útvonalalapú VPN-átjáró**                          |
| ---                      | ---                         | ---                              |---                                                 |
| **Azure Gateway Termékváltozat**    | Basic                       | Basic                            | VpnGw1, VpnGw2, VpnGw3, VpnGw4, VpnGw5  |
| **Internetes kulcscsere verziója**          | IKEv1                       | IKEv2                            | IKEv1 és IKEv2                         |
| **Max. S2S-kapcsolatok** | **1**                       | 10                               | 30                     |
|                          |                             |                                  |                                                    |

Az egyéni IPsec/IKE-házirenddel most már konfigurálhatja az Azure útvonalalapú VPN-átjáróit, hogy előtag-alapú forgalomválasztókat használjanak a "**PolicyBasedTrafficSelectors**" beállítással, hogy helyszíni házirend-alapú VPN-eszközökhöz csatlakozzon. Ez a funkció lehetővé teszi, hogy egy Azure virtuális hálózatról és VPN-átjáróról több helyszíni házirend-alapú VPN/tűzfal eszközhöz csatlakozzon, és távolítsa el az egyetlen kapcsolatkorlátot a jelenlegi Azure-szabályzatalapú VPN-átjárókból.

> [!IMPORTANT]
> 1. A kapcsolat engedélyezéséhez a helyszíni házirend-alapú VPN-eszközök nek támogatniuk kell az **IKEv2-t** az Azure útvonalalapú VPN-átjárókhoz való csatlakozáshoz. Ellenőrizze a VPN-eszköz specifikációit.
> 2. A házirend-alapú VPN-eszközökön keresztül ezzel a mechanizmussal összekötő helyszíni hálózatok csak az Azure virtuális hálózatához csatlakozhatnak; **nem tudnak más helyszíni hálózatokra vagy virtuális hálózatokra áthaladni ugyanazon az Azure VPN-átjárón keresztül.**
> 3. A konfigurációs beállítás az egyéni IPsec/IKE kapcsolatházirend része. Ha engedélyezi a házirend-alapú forgalomválasztó beállítást, meg kell adnia a teljes házirendet (IPsec/IKE titkosítási és integritási algoritmusok, kulcserősségek és biztonsági társítások élettartama).

Az alábbi ábra bemutatja, hogy az Azure VPN-átjárón keresztüli tranzitútválasztás miért nem működik a házirendalapú beállítással:

![politikaalapú tranzit](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedtransit.png)

Amint az az ábrán látható, az Azure VPN-átjáró a virtuális hálózatból a virtuális hálózat minden egyes helyszíni hálózati előtaghoz, de a keresztkapcsolat-előtagokhoz. Például a helyszíni 2-es, 3-as és 4-es helyen kommunikálhat a VNet1-vel, de nem tud csatlakozni az Azure VPN-átjárón keresztül egymáshoz. Az ábrán a cross-connect forgalom választók, amelyek nem érhetők el az Azure VPN-átjáró ebben a konfigurációban.

## <a name="workflow"></a><a name="workflow"></a>Munkafolyamat

Ebben a cikkben található utasítások az [S2S- vagy V2S-hálózati kapcsolatok IPsec/IKE-házirendjének konfigurálása](vpn-gateway-ipsecikepolicy-rm-powershell.md) című példában leírtakat követik az S2S VPN-kapcsolatok létrehozásához. Ez az alábbi ábrán látható:

![s2s-politika](./media/vpn-gateway-connect-multiple-policybased-rm-ps/s2spolicypb.png)

A kapcsolat engedélyezéséhez:
1. Hozza létre a virtuális hálózatot, a VPN-átjárót és a helyi hálózati átjárót a létesítmények közötti kapcsolathoz.
2. IPsec/IKE-házirend létrehozása.
3. Alkalmazza a házirendet, amikor s2s- vagy virtuálishálózat-vnet-kapcsolatot hoz létre, és **engedélyezi a házirend-alapú forgalomválasztókat** a kapcsolaton.
4. Ha a kapcsolat már létrejött, alkalmazhatja vagy frissítheti a házirendet egy meglévő kapcsolatra.

## <a name="before-you-begin"></a>Előkészületek

* Győződjön meg arról, hogy rendelkezik Azure-előfizetéssel. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial).

* [!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="enable-policy-based-traffic-selectors"></a><a name="enablepolicybased"></a>Házirendalapú forgalomválasztók engedélyezése

Ez a szakasz bemutatja, hogyan engedélyezheti a házirend-alapú forgalom választók egy kapcsolaton. Győződjön meg arról, hogy elvégezte [az IPsec/IKE házirendjének konfigurálása című cikk 3.](vpn-gateway-ipsecikepolicy-rm-powershell.md) A cikkben ismertetett lépések ugyanazokat a paramétereket használják.

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>1. lépés - A virtuális hálózat, a VPN-átjáró és a helyi hálózati átjáró létrehozása

#### <a name="connect-to-your-subscription-and-declare-your-variables"></a>Csatlakozás az előfizetéshez és deklarálhatja változóit

1. Ha a PowerShellhelyi en fut a számítógépen, jelentkezzen be a *Connect-AzAccount* parancsmag használatával. Vagy inkább használja az Azure Cloud Shellt a böngészőjében.

2. Deklarálja a változókat. Ehhez a gyakorlathoz a következő változókat használjuk:

   ```azurepowershell-interactive
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

#### <a name="create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>A virtuális hálózat, a VPN-átjáró és a helyi hálózati átjáró létrehozása

1. Hozzon létre egy erőforráscsoportot.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG1 -Location $Location1
   ```
2. A következő példában három alhálózattal hozza létre a TestVNet1 virtuális hálózatot és a VPN-átjárót. Ha értékeket szeretne helyettesíteni, fontos, hogy mindig az átjáró alhálózatát kifejezetten "GatewaySubnet"-nek nevezze el. Ha ezt másként nevezi el, az átjáró létrehozása meghiúsul.

    ```azurepowershell-interactive
    $fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
    $besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
    $gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1
    
    New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
    
    $gw1pip1    = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
    $vnet1      = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
    $subnet1    = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
    $gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1
    
    New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance
    
    New-AzLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix $LNGPrefix61,$LNGPrefix62
    ```

### <a name="step-2---create-an-s2s-vpn-connection-with-an-ipsecike-policy"></a>2. lépés – S2S VPN-kapcsolat létrehozása IPsec/IKE-házirenddel

1. IPsec/IKE-házirend létrehozása.

   > [!IMPORTANT]
   > Létre kell hoznia egy IPsec/IKE-házirendet a "UsePolicyBasedTrafficSelectors" beállítás engedélyezéséhez a kapcsolaton.

   A következő példa létrehoz egy IPsec/IKE-házirendet ezekkel az algoritmusokkal és paraméterekkel:
    * IKEv2: AES256, SHA384, DHGroup24
    * IPsec: AES256, SHA256, PFS None, SA Élettartam 14400 másodperc & 102400000KB

   ```azurepowershell-interactive
   $ipsecpolicy6 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
   ```
1. Hozza létre az S2S VPN-kapcsolatot házirendalapú forgalomválasztókkal és IPsec/IKE házirenddel, és alkalmazza az előző lépésben létrehozott IPsec/IKE házirendet. Legyen tisztában a "-UsePolicyBasedTrafficSelectors $True" további paraméterrel, amely lehetővé teszi a házirend-alapú forgalomválasztók használatát a kapcsolaton.

   ```azurepowershell-interactive
   $vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
   $lng6 = Get-AzLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

   New-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -UsePolicyBasedTrafficSelectors $True -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
   ```
1. A lépések végrehajtása után az S2S VPN-kapcsolat a definiált IPsec/IKE házirendet fogja használni, és engedélyezi a házirend-alapú forgalomválasztókat a kapcsolaton. Ugyanazokat a lépéseket megismételheti további helyszíni házirend-alapú VPN-eszközökhöz való további kapcsolatok hozzáadásához ugyanabból az Azure VPN-átjáróból.

## <a name="to-update-policy-based-traffic-selectors"></a><a name="update"></a>Házirendalapú forgalomválasztók frissítése
Ez a szakasz bemutatja, hogyan frissítheti a házirend-alapú forgalom választók lehetőséget egy meglévő S2S VPN-kapcsolat.

1. A kapcsolati erőforrás beszerezése.

   ```azurepowershell-interactive
   $RG1          = "TestPolicyRG1"
   $Connection16 = "VNet1toSite6"
   $connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
   ```
1. Tekintse meg a házirend-alapú forgalom választók lehetőséget.
A következő sor azt mutatja, hogy a házirend-alapú forgalom választók használják a kapcsolatot:

   ```azurepowershell-interactive
   $connection6.UsePolicyBasedTrafficSelectors
   ```

   Ha a sor "**True**" értéket ad vissza, akkor a házirend-alapú forgalomválasztók a kapcsolaton vannak konfigurálva; ellenkező esetben a "**Hamis**" értéket adja vissza.
1. Miután megszerezte a kapcsolaterőforrást, engedélyezheti vagy letilthatja a házirend-alapú forgalomválasztókat egy kapcsolaton.

   - Az engedélyezéshez

      A következő példa engedélyezi a házirend-alapú forgalomválasztók beállítást, de változatlanul hagyja az IPsec/IKE házirendet:

      ```azurepowershell-interactive
      $RG1          = "TestPolicyRG1"
      $Connection16 = "VNet1toSite6"
      $connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

      Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $True
      ```

   - Letiltás

      A következő példa letiltja a házirend-alapú forgalomválasztók beállítást, de változatlanul hagyja az IPsec/IKE házirendet:

      ```azurepowershell-interactive
      $RG1          = "TestPolicyRG1"
      $Connection16 = "VNet1toSite6"
      $connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

      Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $False
      ```

## <a name="next-steps"></a>További lépések
Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. A lépésekért lásd: [Virtuális gép létrehozása](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Az egyéni IPsec/IKE-házirendekkel kapcsolatos további részletekért tekintse át [az IPsec/IKE-házirend konfigurálása S2S VPN- vagy VNet-vnet-kapcsolatokhoz](vpn-gateway-ipsecikepolicy-rm-powershell.md) című ismertetés című ismertetés című ismertetőt is.
