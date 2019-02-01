---
title: Az Azure VPN Gateway átjárókhoz csatlakozik a VPN-eszközök konfigurációjának partneri |} A Microsoft Docs
description: Ez a cikk az Azure VPN Gateway átjárókhoz csatlakozik partner VPN-eszközök konfigurációjának áttekintését.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: ''
ms.assetid: a8bfc955-de49-4172-95ac-5257e262d7ea
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/20/2017
ms.author: yushwang
ms.openlocfilehash: 901de2ac3d80b3ee529d89a22afae7e823437b9b
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55506872"
---
# <a name="overview-of-partner-vpn-device-configurations"></a>Partner VPN-eszközök konfigurációjának áttekintése
Ez a cikk az Azure VPN Gateway átjárókhoz csatlakozik a helyszíni VPN-eszközök konfigurálásának áttekintése. A példa az Azure virtual network, és VPN-átjáró telepítőjének megmutatjuk, hogyan lehet csatlakozni másik helyszíni VPN-eszközök konfigurációjának azonos paraméterekkel használja.

## <a name="device-requirements"></a>Eszközkövetelmények
Az Azure VPN-átjárók használata szabványos IPsec/IKE protokoll programcsomagok site-to-site (S2S) VPN-alagutat. IPsec/IKE-paraméterek és az Azure VPN gatewayek titkosítási algoritmusok listájáért lásd: [tudnivalók a VPN-eszközök](vpn-gateway-about-vpn-devices.md). Azt is beállíthatja a pontos algoritmusokat és kulcserősségeket egy adott kapcsolathoz tartozó leírtak szerint [információ a kriptográfiai követelményekről](vpn-gateway-about-compliance-crypto.md).

## <a name ="singletunnel"></a>Egyetlen VPN-alagút
A minta első konfiguráció áll egyetlen S2S VPN-alagút az Azure VPN gateway és a egy helyszíni VPN-eszköz között. Igény szerint konfigurálható a [Border Gateway Protocol (BGP) a VPN-alagúton keresztül](#bgp).

![Egyetlen S2S VPN-alagút ábrája](./media/vpn-gateway-3rdparty-device-config-overview/singletunnel.png)

Állítsa be a VPN-alagút egyetlen lépésenkénti útmutatójáért lásd: [hely – hely kapcsolat konfigurálása](vpn-gateway-howto-site-to-site-resource-manager-portal.md). A következő szakaszok adja meg a minta konfigurációs kapcsolat paramétereit, és adjon meg egy PowerShell-parancsprogram induláshoz.

### <a name="connection-parameters"></a>Kapcsolódási paraméterek
Ez a szakasz tartalmazza a példák a korábbi szakaszokban ismertetett paramétereket.

| **A paraméter**                | **Érték**                    |
| ---                          | ---                          |
| Virtuális hálózat címelőtagjainak        | 10.11.0.0/16<br>10.12.0.0/16 |
| Az Azure VPN gateway-IP         | Az Azure VPN-átjáró IP         |
| A helyszíni címelőtagokat | 10.51.0.0/16<br>10.52.0.0/16 |
| A helyszíni VPN-eszköz IP    | A helyszíni VPN-eszköz IP    |
| * A virtuális hálózat BGP ASN-je                | 65010                        |
| * Az azure BGP társ IP-Címét           | 10.12.255.30                 |
| * A helyszíni BGP ASN-je         | 65050                        |
| * A helyszíni BGP-Társgép IP     | 10.52.255.254                |

\* Nem kötelező paraméter a BGP csak.

### <a name="sample-powershell-script"></a>PowerShell-parancsprogram
Ez a témakör egy mintaszkriptet a kezdéshez. Részletes útmutatásért lásd: [S2S VPN-kapcsolat létrehozása a PowerShell használatával](vpn-gateway-create-site-to-site-rm-powershell.md).

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

Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1

# Create virtual network

$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1 $besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

# Create VPN gateway

$gwpip1    = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1     = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1

New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN

# Create local network gateway

New-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix51,$LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5

# Create the S2S VPN connection

$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False
```

### <a name ="policybased"></a>(Nem kötelező) UsePolicyBasedTrafficSelectors egyéni IPsec/IKE-házirend használata
Ha a VPN-eszközök nem támogatják a – bármely forgalomválasztóinak útválasztó-alapú vagy VTI-alapú konfigurációk esetén például hozzon létre egy egyéni IPsec/IKE-házirendet a a [UsePolicyBasedTrafficSelectors](vpn-gateway-connect-multiple-policybased-rm-ps.md) lehetőséget.

> [!IMPORTANT]
> Létre kell hoznia egy IPsec/IKE-házirend engedélyezése a **UsePolicyBasedTrafficSelectors** beállítást a kapcsolat.


A példaszkript egy IPsec/IKE-házirendet hoz létre a következő algoritmusokat és paramétereket:
* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA1, PFS24, SA élettartama 7,200 másodperc és 20,480,000 KB (20 GB)

A parancsfájl az IPsec/IKE-szabályzat vonatkozik, és lehetővé teszi, hogy a **UsePolicyBasedTrafficSelectors** beállítást a kapcsolat.

```powershell
$ipsecpolicy5 = New-AzureRmIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA1 -PfsGroup PFS24 -SALifeTimeSeconds 7200 -SADataSizeKilobytes 20480000

$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False -IpsecPolicies $ipsecpolicy5 -UsePolicyBasedTrafficSelectors $True
```

### <a name ="bgp"></a>(Nem kötelező) A BGP használata az S2S VPN-kapcsolat
Az S2S VPN-kapcsolat létrehozásakor is használhat [a VPN Gateway a BGP](vpn-gateway-bgp-resource-manager-ps.md). Ennek a megközelítésnek vannak, két eltéréssel:

* A helyszíni címelőtagokat lehet egyetlen gazdagépcímét. A helyszíni BGP-társ IP-címét az alábbiak szerint van megadva:

    ```powershell
    New-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
    ```

* A kapcsolat létrehozásakor meg kell állítani a **- enablebgp paramétert** $true lehetőséget:

    ```powershell
    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
    ```

## <a name="next-steps"></a>További lépések
Állítsa be az aktív-aktív VPN-átjárók lépésenkénti útmutatójáért lásd: [konfigurálása létesítmények közötti és VNet – VNet kapcsolat aktív-aktív VPN-átjárók](vpn-gateway-activeactive-rm-powershell.md).

