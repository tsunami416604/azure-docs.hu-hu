---
title: "Csatlakozás több helyszíni házirendalapú VPN-eszközök Azure VPN-átjárót: Azure Resource Manager: PowerShell |} Microsoft Docs"
description: "Ez a cikk végigvezeti az Azure útvonalalapú VPN-átjáró konfigurálása több csoportházirend-alapú VPN-eszközök Azure Resource Manager és a PowerShell használatával."
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/27/2017
ms.author: yushwang
ms.openlocfilehash: db4d8837fb5c5d15364422e957e4914966215674
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="connect-azure-vpn-gateways-to-multiple-on-premises-policy-based-vpn-devices-using-powershell"></a>Csatlakozás több helyszíni házirendalapú VPN-eszközök PowerShell használata Azure VPN-átjárót

Ez a cikk segítséget nyújt az Azure útvonalalapú VPN-átjáró több helyi csoportházirend-alapú VPN-eszközök egyéni IPsec/IKE-házirendek S2S VPN-kapcsolatok számát, ami csatlakozni konfigurálásához.

## <a name="about-policy-based-and-route-based-vpn-gateways"></a>Házirend- és útvonal-alapú VPN gatewayek kapcsolatban

Házirend - *és* útvonalalapú VPN-eszközök különböznek az IPsec-forgalom választók kapcsolat beállításának módját:

* **Csoportházirend-alapú** VPN-eszközök mindkét hálózat előtagjait kombinációi segítségével meghatározhatja, hogyan forgalom IPsec-alagutakon keresztül titkosítása/visszafejtése-e. Hálózaticsomag-szűrés végző tűzfal eszközök általában épül. A csomag szűrést és a feldolgozási motorja IPsec-alagút titkosítási és visszafejtési kerülnek.
* **Útválasztó-alapú** VPN-eszközök használata bármely elem közöttiként (helyettesítő) forgalmat választók és IPsec bújtatja táblák közvetlen forgalom másik útválasztási vagy továbbítás segítségével. Ha minden IPsec-alagutat van modellezve a hálózati adaptert vagy VTI (virtuális alagút-illesztő) útválasztó platformokon általában be van építve.

Az alábbi ábrák jelölje ki a két modell:

### <a name="policy-based-vpn-example"></a>A házirendalapú VPN – példa
![Csoportházirend-alapú](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedmultisite.png)

### <a name="route-based-vpn-example"></a>Útvonalalapú VPN – példa
![útválasztó-alapú](./media/vpn-gateway-connect-multiple-policybased-rm-ps/routebasedmultisite.png)

### <a name="azure-support-for-policy-based-vpn"></a>Azure házirendalapú VPN-támogatás
Jelenleg Azure támogatja-e a VPN-átjárók mindkét módnál: útvonalalapú VPN-átjárók és a csoportházirend-alapú VPN gatewayek. A beépített különböző platformokon belső, a különböző specifikációihoz eredmények:

|                          | **PolicyBased VPN-átjáró** | **RouteBased VPN-átjáró**               |
| ---                      | ---                         | ---                                      |
| **Az Azure átjáró-Termékváltozat**    | Basic                       | Basic, Standard, HighPerformance, VpnGw1, VpnGw2, VpnGw3 |
| **IKE-verzió**          | IKEv1                       | IKEv2                                    |
| **Max. S2S-kapcsolatok** | **1**                       | Basic vagy Standard: 10<br> HighPerformance: 30 |
|                          |                             |                                          |

Az egyéni IPsec/IKE irányelvnek konfigurálhat Azure útválasztó-alapú VPN gatewayek előtag-alapú forgalom választók használata beállítást "**PolicyBasedTrafficSelectors**", a helyi csoportházirend-alapú VPN-eszközök való kapcsolódáshoz. E képesség lehetővé teszi egy Azure virtuális hálózatra csatlakozni, és több VPN-átjárót a helyszíni házirendalapú VPN/tűzfal eszközök, az egyetlen kapcsolathoz megadott korlátot eltávolítása az aktuális Azure házirendalapú VPN gatewayek.

> [!IMPORTANT]
> 1. Ahhoz, hogy ez az összekapcsolhatóság, támogatnia kell a helyi csoportházirend-alapú VPN-eszközök **IKEv2** az Azure útvonalalapú VPN-átjáróként való kapcsolódáshoz. Ellenőrizze a VPN-eszköz specifikációja.
> 2. A helyszíni hálózatokhoz a mechanizmus a házirendalapú VPN-eszközök keresztül csatlakozó csak csatlakozhat az Azure virtuális hálózat; **azokat más helyszíni hálózatokhoz vagy az ugyanazon Azure VPN-átjárón keresztül virtuális hálózatok nem tranzit**.
> 3. A konfigurációs beállítás IPsec/IKE-kapcsolat egyéni házirend részét képezi. Ha a házirend-alapú forgalom választó lehetőséget választja, meg kell adnia a teljes házirend (a IPsec/IKE titkosítás és sértetlenség algoritmusokról, a kulcs szintjeiről és a biztonsági Társítások élettartama).

Az alábbi ábrán látható, hogy miért tranzit útválasztást Azure VPN-átjárón keresztül nem működik a házirend alapú beállítás:

![Csoportházirend-alapú átvitel közben](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedtransit.png)

Az ábrán is látható, az Azure VPN gateway van a helyszíni hálózati előtagjait, de nem a kereszt-kapcsolat előtagok mindegyikének a virtuális hálózati forgalom választók. Például a helyszíni hely 2, 3 hely és hely 4 egyes jelezheti VNet1 rendre, de nem tud csatlakozni egymáshoz az Azure VPN gateway keresztül. Az ábrán látható, amely nem szerepel a konfiguráció alatt az Azure VPN gateway cross-connect-forgalom választók.

## <a name="configure-policy-based-traffic-selectors-on-a-connection"></a>Csoportházirend-alapú forgalom választók a kapcsolat konfigurálása

Ez a cikk utasításait kövesse ugyanebben a példában ismertetett módon [konfigurálása IPsec/IKE házirend S2S és VNet – VNet kapcsolatokhoz](vpn-gateway-ipsecikepolicy-rm-powershell.md) egy S2S VPN-kapcsolat létrehozásához. Ezt az alábbi ábra mutatja be:

![s2s-házirend](./media/vpn-gateway-connect-multiple-policybased-rm-ps/s2spolicypb.png)

A munkafolyamat ezt kapcsolódásának engedélyezése:
1. A virtuális hálózat, a VPN-átjáró és a helyi hálózati átjáró a létesítmények közötti kapcsolat létrehozása
2. IPsec/IKE-házirend létrehozása
3. Alkalmazza a házirendet, ha S2S vagy VNet – VNet-kapcsolatot, és **engedélyezése a házirend-alapú forgalom választók** a kapcsolaton
4. Ha a kapcsolat már létrejött, alkalmazni, vagy egy létező kapcsolatra a házirend frissítése

## <a name="enable-policy-based-traffic-selectors-on-a-connection"></a>Csoportházirend-alapú forgalom választók kapcsolat engedélyezése

Győződjön meg arról, hogy befejeződött [3. rész konfigurálása IPsec/IKE házirend cikk](vpn-gateway-ipsecikepolicy-rm-powershell.md) az ebben a szakaszban. A következő példában paraméterek és a lépéseket:

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>1. lépés – a virtuális hálózat, a VPN-átjáró és a helyi hálózati átjáró létrehozása

#### <a name="1-declare-your-variables--connect-to-your-subscription"></a>1. Deklarálja a változókat & Csatlakozás az előfizetéshez
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
A Resource Manager parancsmagjainak használatához győződjön meg arról, hogy a PowerShell üzemmódra vált. További információ: [A Windows PowerShell használata a Resource Managerrel](../powershell-azure-resource-manager.md).

Nyissa meg a PowerShell konzolt, és csatlakozzon a fiókjához. A következő minta segíthet a kapcsolódásban:

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="2-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>2. A virtuális hálózat, a VPN-átjáró és a helyi hálózati átjáró létrehozása
Az alábbi példakód létrehozza a virtuális hálózat, három alhálózatokon TestVNet1 és a VPN-átjáró. És az értékeket, fontos, hogy mindig nevezze el az átjáró alhálózatának kifejezetten a "GatewaySubnet". Ha ezt másként nevezi el, az átjáró létrehozása meghiúsul.

```powershell
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

### <a name="step-2---create-a-s2s-vpn-connection-with-an-ipsecike-policy"></a>2. lépés - az IPsec/IKE házirendjével S2S VPN-kapcsolat létrehozása

#### <a name="1-create-an-ipsecike-policy"></a>1. IPsec/IKE-házirend létrehozása

> [!IMPORTANT]
> IPsec/IKE-házirend létrehozása a kapcsolat "UsePolicyBasedTrafficSelectors" beállítást engedélyezni kell.

Az alábbi példa házirendet hoz létre IPsec/IKE ezeket az algoritmusokat és paraméterek:
* IKEv2: AES256, SHA384 DHGroup24
* IPsec: AES256, SHA-256, PFS24, 2048KB & SA élettartam 3600 másodperc

```powershell
$ipsecpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup PFS24 -SALifeTimeSeconds 3600 -SADataSizeKilobytes 2048
```

#### <a name="2-create-the-s2s-vpn-connection-with-policy-based-traffic-selectors-and-ipsecike-policy"></a>2. Az S2S VPN-kapcsolat létrehozása a csoportházirend-alapú forgalom választók és IPsec/IKE házirend
S2S VPN-kapcsolat létrehozásához, és alkalmazza az előző lépésben létrehozott IPsec/IKE szabályzat. Vegye figyelembe a további paraméter "-UsePolicyBasedTrafficSelectors $True" lehetővé teszi a csoportházirend-alapú forgalom választók a kapcsolaton.

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng6 = Get-AzureRmLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -UsePolicyBasedTrafficSelectors $True -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
```

A lépések végrehajtását követően az S2S VPN-kapcsolat lesz az IPsec/IKE házirend lett meghatározva, és engedélyezheti a kapcsolatra vonatkozó házirend-alapú forgalom választók. További helyszíni házirendalapú VPN-eszközök az ugyanazon Azure VPN-átjáróról több kapcsolat hozzáadása a lépéseket megismételve.

## <a name="update-policy-based-traffic-selectors-for-a-connection"></a>Csoportházirend-alapú forgalom választók kapcsolat frissítése
Az utolsó szakasza bemutatja, hogyan a csoportházirend-alapú forgalom választók lehetőséget egy meglévő S2S VPN-kapcsolat frissítése.

### <a name="1-get-the-connection"></a>1. A kapcsolat
A kapcsolati erőforrást beolvasni.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
```

### <a name="2-check-the-policy-based-traffic-selectors-option"></a>2. Jelölje be a házirend-alapú forgalom választók beállítást
A következő sor látható, hogy használja-e a házirend-alapú forgalom választók a kapcsolathoz:

```powershell
$connection6.UsePolicyBasedTrafficSelectors
```

Ha a sort adja vissza "**igaz**", majd csoportházirend-alapú forgalom választók a kapcsolat van konfigurálva; egyéb esetben ad vissza "**hamis**."

### <a name="3-update-the-policy-based-traffic-selectors-on-a-connection"></a>3. A csoportházirend-alapú forgalom választók a kapcsolat frissítése
Miután beszerezte a kapcsolati erőforrást, engedélyezése, vagy letiltja a beállítást.

#### <a name="disable-usepolicybasedtrafficselectors"></a>Tiltsa le a UsePolicyBasedTrafficSelectors
A következő példa a csoportházirend-alapú forgalom választók beállítás letiltása, de hagyja változatlanul IPsec/h.rend:

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $False
```

#### <a name="enable-usepolicybasedtrafficselectors"></a>UsePolicyBasedTrafficSelectors engedélyezése
A következő példa engedélyezi a csoportházirend-alapú forgalom választók, de hagyja változatlanul az IPsec vagy h.rend:

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $True
```

## <a name="next-steps"></a>Következő lépések
Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. A lépésekért lásd: [Virtuális gép létrehozása](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Emellett nézze át az [konfigurálása IPsec/IKE házirend S2S VPN- és VNet – VNet kapcsolatokhoz](vpn-gateway-ipsecikepolicy-rm-powershell.md) egyéni IPsec/IKE-házirendekkel kapcsolatos további részletekért.
