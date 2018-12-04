---
title: 'Csatlakozás az Azure VPN-átjárók több helyszíni házirendalapú VPN-eszköz: az Azure Resource Manager: PowerShell |} A Microsoft Docs'
description: Konfigurálja az Azure útvonalalapú VPN-átjáró több házirendalapú VPN-eszköz Azure Resource Manager és a PowerShell használatával.
services: vpn-gateway
documentationcenter: na
author: yushwang
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: yushwang
ms.openlocfilehash: 46555bf121e674b82c0c7dd39f74ee3708fc4439
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52850641"
---
# <a name="connect-azure-vpn-gateways-to-multiple-on-premises-policy-based-vpn-devices-using-powershell"></a>Csatlakozás az Azure VPN-átjárók több helyszíni házirendalapú VPN-eszköz PowerShell-lel

Ez a cikk segít az Azure útvonalalapú VPN gateway csatlakozás több helyszíni házirendalapú VPN-eszköz kihasználva S2S VPN-kapcsolatot az egyéni IPsec/IKE-szabályzatok konfigurálása.

## <a name="about"></a>Házirendalapú és útvonalalapú VPN-átjárókkal kapcsolatban

A házirend - *és* útvonalalapú VPN-eszközök különböznek az IPsec forgalomválasztóinak a kapcsolat beállításának módját:

* **Csoportházirend-alapú** VPN-eszközök az előtagok mindkét hálózatokról kombinációja segítségével meghatározhatja, hogyan forgalom IPsec-alagutakon keresztül titkosított vagy visszafejtett-e. Hálózaticsomag-szűrés végző tűzfaleszközhöz általában alapján készült. A csomag szűrési és eseményfeldolgozó motor az IPsec-alagút titkosítási és visszafejtési kerülnek.
* **Útválasztó-alapú** VPN-eszközök használata (helyettesítő) – bármely forgalomválasztóinak és IPsec-alagútjainak táblák közvetlen forgalom különböző útválasztási, továbbítás segítségével. Útválasztó-platformokat, ahol minden egyes IPsec-alagút van modellezve a hálózati adapter vagy VTI (virtuális alagút-illesztő) általában alapján készült.

Az alábbi ábrák jelölje ki a két modell:

### <a name="policy-based-vpn-example"></a>Házirendalapú VPN-példa
![Csoportházirend-alapú](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedmultisite.png)

### <a name="route-based-vpn-example"></a>Útvonalalapú VPN-példa
![útválasztó-alapú](./media/vpn-gateway-connect-multiple-policybased-rm-ps/routebasedmultisite.png)

### <a name="azure-support-for-policy-based-vpn"></a>Azure házirendalapú VPN-támogatás
Jelenleg az Azure VPN Gateway átjárók mindkét mód támogatja: az útvonalalapú VPN-átjárók és a csoportházirend-alapú VPN-átjárók. A beépített különböző platformokon belső, ami a különböző előírások:

|                          | **Házirendalapú VPN-átjáró** | **Útvonalalapú VPN-átjáró**               |
| ---                      | ---                         | ---                                      |
| **Azure-átjáró Termékváltozata**    | Alapszintű                       | Basic, Standard, HighPerformance, VpnGw1, VpnGw2, VpnGw3 |
| **IKE verziószám**          | IKEv1                       | IKEv2                                    |
| **Max. S2S-kapcsolatok** | **1**                       | Alapszintű/Standard: 10<br> Nagy teljesítményű: 30 |
|                          |                             |                                          |

Az egyéni IPsec/IKE-házirend, az mostantól konfigurálhatja az Azure útvonalalapú VPN-átjárók előtag-alapú forgalomválasztóinak használata beállítást "**PolicyBasedTrafficSelectors**": csatlakozás helyszíni házirendalapú VPN-eszközhöz. Ez a funkció lehetővé teszi a Azure-beli virtuális hálózathoz történő csatlakoztatásához és a VPN-átjáró több helyszíni házirendalapú VPN/tűzfal-eszközhöz, az egyetlen kapcsolathoz megadott korlátot eltávolítását az aktuális Azure házirendalapú VPN-átjárókhoz.

> [!IMPORTANT]
> 1. A csatlakozás engedélyezéséhez támogatnia kell a helyszíni házirendalapú VPN-eszközök **IKEv2** csatlakozni az Azure útvonalalapú VPN-átjárókhoz. Ellenőrizze a VPN-eszköz specifikációja.
> 2. Az ezt a mechanizmust keresztül házirendalapú VPN-eszközök csatlakoztatása a helyszíni hálózatok csak lehet csatlakozni az Azure virtuális hálózat; **azokat más helyszíni hálózat vagy az ugyanazon Azure VPN Gateway virtuális hálózatok nem átvitel**.
> 3. A konfigurációs beállítást az egyéni IPsec/IKE kapcsolati házirend részét képezi. Ha a házirend-alapú forgalom választó lehetőséget választja, meg kell adnia a teljes szabályzat (IPsec/IKE-titkosítás és -integritás algoritmusok, a legfőbb előnyök és SA élettartama).

Az alábbi ábrán látható, hogy miért tranzit útválasztás az Azure VPN Gateway nem működik a házirend alapú beállítás:

![Csoportházirend-alapú átvitel](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedtransit.png)

Ahogy az ábrán látható, az Azure VPN gateway rendelkezik forgalomválasztóinak a virtuális hálózatról az egyes a helyszíni hálózati előtagok, de nem a keresztkapcsolat előtagok. Például a helyszíni hely 2, webhely 3 és 4. webhely egyes kommunikálhatnak a VNet1 jelölik, de nem tud csatlakozni egymáshoz az Azure VPN gatewayen keresztül. Az ábrán látható a cross-connect forgalomválasztóinak, amelyek nem érhető el ez a konfiguráció alatt az Azure VPN gatewayen.

## <a name="configurepolicybased"></a>A kapcsolat szabályzatalapú forgalomválasztóinak konfigurálása

Ez a cikk utasításait követve ugyanebben a példában leírtak szerint [konfigurálása az IPsec/IKE-szabályzat S2S- és VNet – VNet kapcsolatokhoz](vpn-gateway-ipsecikepolicy-rm-powershell.md) egy S2S VPN-kapcsolat létrehozásához. Ez az alábbi ábrán is látható:

![s2s-policy](./media/vpn-gateway-connect-multiple-policybased-rm-ps/s2spolicypb.png)

A munkafolyamat a kapcsolat:
1. A virtuális hálózat, a VPN-átjáró és a helyi hálózati átjáró a létesítmények közötti kapcsolat létrehozása
2. Egy IPsec/IKE-szabályzat létrehozása
3. A szabályzat alkalmazható, ha S2S vagy VNet – VNet kapcsolat létrehozása és **engedélyezése a szabályzatalapú forgalomválasztóinak** a kapcsolat
4. Ha a kapcsolat már létrejött, alkalmazhat, vagy egy létező kapcsolatot, a házirend módosítása

## <a name="before-you-begin"></a>Előkészületek

Győződjön meg arról, hogy rendelkezik Azure-előfizetéssel. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="enablepolicybased"></a>Szabályzatalapú forgalomválasztóinak a kapcsolat engedélyezése

Győződjön meg arról, hogy végrehajtotta [3. részben a konfigurálása IPsec/IKE-házirend cikk](vpn-gateway-ipsecikepolicy-rm-powershell.md) erre a szakaszra. Az alábbi példában paramétereket és a lépéseket:

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>1. lépés – a virtuális hálózat, a VPN-átjáró és a helyi hálózati átjáró létrehozása

#### <a name="1-connect-to-your-subscription-and-declare-your-variables"></a>1. Csatlakozás az előfizetéshez, és a változók deklarálása

[!INCLUDE [sign in](../../includes/vpn-gateway-cloud-shell-ps login.md)]

Deklarálja a változókat. Ebben a gyakorlatban a következő változókat használjuk:

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

#### <a name="2-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>2. A virtuális hálózat, a VPN-átjáró és a helyi hálózati átjáró létrehozása

Hozzon létre egy erőforráscsoportot.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name $RG1 -Location $Location1
```

Az alábbi példa használatával hozzon létre a TestVNet1 virtuális hálózati három alhálózatot, és a VPN-átjárót. Ha szeretne helyettesítse az értékeket, akkor fontos, hogy Ön mindig az átjáróalhálózat neve "GatewaySubnet". Ha ezt másként nevezi el, az átjáró létrehozása meghiúsul.

```azurepowershell-interactive
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1    = New-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1      = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1    = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance

New-AzureRmLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix $LNGPrefix61,$LNGPrefix62
```

### <a name="step-2---create-a-s2s-vpn-connection-with-an-ipsecike-policy"></a>2. lépés – egy IPsec/IKE-szabályzat S2S VPN-kapcsolat létrehozása

#### <a name="1-create-an-ipsecike-policy"></a>1. Egy IPsec/IKE-szabályzat létrehozása

> [!IMPORTANT]
> Egy IPsec/IKE-házirendet, és engedélyezze a "UsePolicyBasedTrafficSelectors" beállítás a kapcsolat létrehozásához szükséges.

Az alábbi példában egy IPsec/IKE-házirendet hoz létre ezeket az algoritmusokat és paramétereket:
* Az IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA256, PFS24, 2048KB & SA-élettartam 3600 másodperc

```azurepowershell-interactive
$ipsecpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup PFS24 -SALifeTimeSeconds 3600 -SADataSizeKilobytes 2048
```

#### <a name="2-create-the-s2s-vpn-connection-with-policy-based-traffic-selectors-and-ipsecike-policy"></a>2. Az S2S VPN-kapcsolat szabályzatalapú forgalomválasztóinak és IPsec/IKE-szabályzat létrehozása
Hozzon létre S2S VPN-kapcsolatot, és a alkalmazni az IPsec/IKE-házirendet az előző lépésben létrehozott. Vegye figyelembe a további paraméter "-UsePolicyBasedTrafficSelectors $True" lehetővé teszi a szabályzatalapú forgalomválasztóinak a kapcsolaton.

```azurepowershell-interactive
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng6 = Get-AzureRmLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -UsePolicyBasedTrafficSelectors $True -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
```

A lépések elvégzése után az S2S VPN-kapcsolat lesz definiálva IPsec/IKE-házirend használata, és engedélyezze szabályzatalapú forgalomválasztóinak a kapcsolaton. Megismételheti a lépéseket adhat hozzá további kapcsolatok további helyszíni házirendalapú VPN-eszközök az Azure VPN-átjáróhoz.

## <a name="update-policy-based-traffic-selectors-for-a-connection"></a>A kapcsolat szabályzatalapú forgalomválasztóinak frissítése
Az utolsó szakaszban jeleníti meg a csoportházirend-alapú forgalom választók beállítás egy létező S2S VPN-kapcsolat frissítése.

### <a name="1-get-the-connection"></a>1. A kapcsolat beolvasása
A kapcsolati erőforrás lekérése.

```azurepowershell-interactive
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
```

### <a name="2-check-the-policy-based-traffic-selectors-option"></a>2. Ellenőrizze a csoportházirend-alapú forgalom választók beállítás
A következő sorban látható-e a kapcsolat szabályzatalapú forgalomválasztóinak használt:

```azurepowershell-interactive
$connection6.UsePolicyBasedTrafficSelectors
```

Ha a sort adja vissza "**igaz**", majd a kapcsolat szabályzatalapú forgalomválasztóinak történik; ellenkező esetben adja vissza "**hamis**."

### <a name="3-enabledisable-the-policy-based-traffic-selectors-on-a-connection"></a>3. Engedélyezi/letiltja a szabályzatalapú forgalomválasztóinak kapcsolaton
Miután beszerezte a kapcsolati erőforrást, is engedélyezi vagy letiltja a beállítást.

#### <a name="to-enable-usepolicybasedtrafficselectors"></a>UsePolicyBasedTrafficSelectors engedélyezése
Az alábbi példa a csoportházirend-alapú forgalom választók beállítás lehetővé teszi, de az IPsec/IKE szabályzat változatlanul hagyja:

```azurepowershell-interactive
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $True
```

#### <a name="to-disable-usepolicybasedtrafficselectors"></a>UsePolicyBasedTrafficSelectors letiltása
A következő példa letiltja a csoportházirend-alapú forgalom választók lehetőséget, de az IPsec/IKE szabályzat változatlanul hagyja:

```azurepowershell-interactive
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $False
```

## <a name="next-steps"></a>További lépések
Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. A lépésekért lásd: [Virtuális gép létrehozása](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Emellett tekintse át [konfigurálása az IPsec/IKE-szabályzat S2S VPN- és VNet – VNet kapcsolatokhoz](vpn-gateway-ipsecikepolicy-rm-powershell.md) egyéni IPsec/IKE-szabályzatok további részleteiért.
