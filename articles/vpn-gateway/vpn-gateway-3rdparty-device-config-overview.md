---
title: Partner VPN-eszközkonfigurációk az Azure VPN-átjárókhoz való csatlakozáshoz
description: Ez a cikk áttekintést nyújt a partner VPN-eszköz konfigurációk az Azure VPN-átjárók hoz való csatlakozáshoz.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 06/20/2017
ms.author: yushwang
ms.openlocfilehash: b914afaa6725920078da309981bcda5bb765e155
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279402"
---
# <a name="overview-of-partner-vpn-device-configurations"></a>A partner VPN-eszközkonfigurációinak áttekintése
Ez a cikk áttekintést nyújt a helyszíni VPN-eszközök konfigurálása az Azure VPN-átjárókhoz való csatlakozáshoz. Az Azure virtuális hálózat és a VPN-átjáró mintabeállítása megmutatja, hogyan csatlakozhat a különböző helyszíni VPN-eszközkonfigurációkhoz ugyanazon paraméterek használatával.



## <a name="device-requirements"></a>Az eszközre vonatkozó követelmények
Az Azure VPN-átjárók szabványos IPsec/IKE protokollcsomagokat használnak a helyek közötti (S2S) VPN-alagutakhoz. Az Azure VPN-átjárók IPsec/IKE paramétereinek és kriptográfiai algoritmusainak listáját a [VPN-eszközök – lisztezés](vpn-gateway-about-vpn-devices.md)című témakörben található. Megadhatja az adott kapcsolat pontos algoritmusait és kulcserősségeit is a [Kriptográfiai követelmények – ismertetése](vpn-gateway-about-compliance-crypto.md)című részben leírtak szerint.

## <a name="single-vpn-tunnel"></a><a name ="singletunnel"></a>Egy VPN-alagút
A minta első konfigurációja egyetlen S2S VPN-alagútból áll egy Azure VPN-átjáró és egy helyszíni VPN-eszköz között. A Border Gateway Protocol (BGP) protokollt a [VPN-alagúton keresztül](#bgp)is konfigurálhatja.

![Egyetlen S2S VPN-alagút diagramja](./media/vpn-gateway-3rdparty-device-config-overview/singletunnel.png)

Az egyetlen VPN-alagút beállításának lépésenkénti [útmutatója a Helyek közötti kapcsolat konfigurálása](vpn-gateway-howto-site-to-site-resource-manager-portal.md)című témakörben található. A következő szakaszok a mintakonfiguráció csatlakozási paramétereit határozzák meg, és egy PowerShell-parancsfájlt biztosítanak az első lépésekhez.

### <a name="connection-parameters"></a>Kapcsolódási paraméterek
Ez a szakasz az előző szakaszokban ismertetett példák paramétereit sorolja fel.

| **Paraméter**                | **Érték**                    |
| ---                          | ---                          |
| Virtuális hálózati cím előtagok        | 10.11.0.0/16<br>10.12.0.0/16 |
| Azure VPN-átjáró IP-címe         | Az Azure VPN-átjáró IP-címe         |
| Helyszíni címelőtagok | 10.51.0.0/16<br>10.52.0.0/16 |
| Helyszíni VPN-eszköz IP-címe    | Helyszíni VPN-eszköz IP-címe    |
| * Virtuális hálózat BGP ASN                | 65010                        |
| * Az Azure BGP-társ IP-címe           | 10.12.255.30                 |
| * Helyszíni BGP ASN         | 65050                        |
| * Helyszíni BGP-társ IP-címe     | 10.52.255.254                |

\*Csak a BGP-hez választható paraméter.

### <a name="sample-powershell-script"></a>PowerShell-parancsfájl mintája
Ez a szakasz egy mintaparancsfájlt biztosít a kezdéshez. Részletes útmutatást az [S2S VPN-kapcsolat létrehozása a PowerShell használatával című témakörben talál.](vpn-gateway-create-site-to-site-rm-powershell.md)

```powershell
# Declare your variables

$Sub1          = "Replace_With_Your_Subscription_Name"
$RG1           = "TestRG1"
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
$VNet1ASN      = 65010
$DNS1          = "8.8.8.8"
$GWName1       = "VNet1GW"
$GWIPName1     = "VNet1GWIP"
$GWIPconfName1 = "gwipconf1"
$Connection15  = "VNet1toSite5"
$LNGName5      = "Site5"
$LNGPrefix50   = "10.52.255.254/32"
$LNGPrefix51   = "10.51.0.0/16"
$LNGPrefix52   = "10.52.0.0/16"
$LNGIP5        = "Your_VPN_Device_IP"
$LNGASN5       = 65050
$BGPPeerIP5    = "10.52.255.254"

# Connect to your subscription and create a new resource group

Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1

# Create virtual network

$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1 $besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

# Create VPN gateway

$gwpip1    = New-AzPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1     = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1   = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1

New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN

# Create local network gateway

New-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix51,$LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5

# Create the S2S VPN connection

$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False
```

### <a name="optional-use-custom-ipsecike-policy-with-usepolicybasedtrafficselectors"></a><a name ="policybased"></a>(Nem kötelező) Egyéni IPsec/IKE-házirend használata usePolicyBasedTrafficSelectors
Ha a VPN-eszközök nem támogatják a forgalom választókat, például az útvonal- vagy VTI-alapú konfigurációkat, hozzon létre egy egyéni IPsec/IKE-házirendet a [UsePolicyBasedTrafficSelectors](vpn-gateway-connect-multiple-policybased-rm-ps.md) beállítással.

> [!IMPORTANT]
> A **UsePolicyBasedTrafficSelectors** beállítás engedélyezéséhez létre kell hoznia egy IPsec/IKE-házirendet a kapcsolaton.


A mintaparancsfájl ipsec/IKE-házirendet hoz létre a következő algoritmusokkal és paraméterekkel:
* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA1, PFS24, SA élettartam 7200 másodperc, és 20.480.000 KB (20 GB)

A parancsfájl az IPsec/IKE házirendet alkalmazza, és engedélyezi a **UsePolicyBasedTrafficSelectors** beállítást a kapcsolaton.

```powershell
$ipsecpolicy5 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA1 -PfsGroup PFS24 -SALifeTimeSeconds 7200 -SADataSizeKilobytes 20480000

$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False -IpsecPolicies $ipsecpolicy5 -UsePolicyBasedTrafficSelectors $True
```

### <a name="optional-use-bgp-on-s2s-vpn-connection"></a><a name ="bgp"></a>(Nem kötelező) BGP használata S2S VPN-kapcsolaton
Az S2S VPN-kapcsolat létrehozásakor a BGP-t is használhatja [a VPN-átjáróhoz.](vpn-gateway-bgp-resource-manager-ps.md) Ennek a megközelítésnek két különbsége van:

* A helyszíni címelőtagok lehetnek egyetlen állomáscím. A helyszíni BGP-társ IP-címe a következőképpen van megadva:

    ```powershell
    New-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
    ```

* A kapcsolat létrehozásakor az **-EnableBGP** kapcsolót $True:

    ```powershell
    New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
    ```

## <a name="next-steps"></a>További lépések
Az aktív-aktív VPN-átjárók beállításának lépésenkénti [útmutatója az aktív-aktív VPN-átjárók konfigurálása a létesítmények közötti és a virtuális hálózatok](vpn-gateway-activeactive-rm-powershell.md)közötti kapcsolatokhoz című témakörben található.

