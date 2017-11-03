---
title: "Azure VPN gatewayek való csatlakozás VPN eszközkonfigurációk partneri |} Microsoft Docs"
description: "Ez a cikk áttekintést partner VPN eszközkonfigurációk Azure VPN gatewayek való kapcsolódáshoz."
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: 
ms.assetid: a8bfc955-de49-4172-95ac-5257e262d7ea
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/20/2017
ms.author: yushwang
ms.openlocfilehash: b3806d16d3b78347e183ecbd2ab5a463a2142110
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-partner-vpn-device-configurations"></a>Partner VPN eszközkonfigurációk áttekintése
Ez a cikk áttekintést konfigurálása a helyszíni VPN-eszközök Azure VPN gatewayek való kapcsolódáshoz. A minta Azure-beli virtuális hálózat és a VPN-átjáró telepítési használt bemutatják a különböző helyszíni VPN eszközkonfigurációk ugyanazokkal a paraméterekkel használatával csatlakoznak.

## <a name="device-requirements"></a>Eszközkövetelmények
Azure VPN gatewayek szabványos IPsec/IKE protokoll csomagok használja a helyek (közötti S2S) VPN-alagutat. IPsec/IKE-paraméterek és az Azure VPN gatewayek titkosítási algoritmusok listájáért lásd: [kapcsolatos VPN-eszközök](vpn-gateway-about-vpn-devices.md). Azt is megadhatja a pontos algoritmusok és egy adott kapcsolat kulcs szintjeiről a [titkosítási követelményekkel kapcsolatos](vpn-gateway-about-compliance-crypto.md).

## <a name ="singletunnel"></a>Egy VPN-alagút
A minta az első konfigurálása az Azure VPN gateway és egy helyszíni VPN-eszköz között egyetlen S2S VPN-alagúton áll. Konfigurálhatja a [Border Gateway Protocol (BGP) a VPN-alagúton keresztül](#bgp).

![Egyetlen S2S VPN-alagúton ábrája](./media/vpn-gateway-3rdparty-device-config-overview/singletunnel.png)

Egy VPN-alagúton beállítása lépésenkénti útmutatót lásd: [webhelyek kapcsolat beállítása](vpn-gateway-howto-site-to-site-resource-manager-portal.md). A következő szakaszok kell megadni a mintakonfiguráció kapcsolat paramétereit, és adjon meg egy PowerShell-parancsfájl segítségével hozzákezdhet.

### <a name="connection-parameters"></a>Kapcsolódási paraméterek
Ez a szakasz a példákat a korábbi szakaszokban ismertetett paramétereket tartalmazza.

| **A paraméter**                | **Érték**                    |
| ---                          | ---                          |
| Virtuális hálózati címelőtagokat        | 10.11.0.0/16<br>10.12.0.0/16 |
| Az Azure VPN gateway IP-címet         | Az Azure VPN Gateway IP         |
| A helyszíni címelőtagokat | 10.51.0.0/16<br>10.52.0.0/16 |
| A helyszíni VPN-eszköz IP-címet    | A helyszíni VPN-eszköz IP-címet    |
| * A virtuális hálózati BGP ASN                | 65010                        |
| * Az azure BGP-Társgép IP-           | 10.12.255.30                 |
| * A helyszíni BGP ASN         | 65050                        |
| * A helyszíni BGP-Társgép IP-     | 10.52.255.254                |

\*Nem kötelező paraméter BGP csak.

### <a name="sample-powershell-script"></a>PowerShell-parancsfájlpélda
Ez a témakör egy minta parancsfájlt az első lépésekhez. Részletes útmutatásért lásd: [S2S VPN-kapcsolat létrehozásához a PowerShell használatával](vpn-gateway-create-site-to-site-rm-powershell.md).

```powershell
# Declare your variables

$Sub1          = "Replace_With_Your_Subcription_Name"
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

Login-AzureRmAccount
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

### <a name ="policybased"></a>(Választható) Egyéni IPsec/IKE-házirend használata UsePolicyBasedTrafficSelectors
Ha a VPN-eszközök nem támogatják az any közöttiként forgalom választók, például az útválasztó-alapú vagy VTI-alapú konfigurációk esetén az egyéni IPsec/IKE-házirend létrehozása a [UsePolicyBasedTrafficSelectors](vpn-gateway-connect-multiple-policybased-rm-ps.md) lehetőséget.

> [!IMPORTANT]
> Az IPsec/IKE engedélyezésére vonatkozó házirendet kell létrehoznia a **UsePolicyBasedTrafficSelectors** beállítást a kapcsolathoz.


A parancsfájlpéldát házirendet hoz létre IPsec/IKE a következő algoritmusokat és a Paraméterek:
* IKEv2: AES256, SHA384 DHGroup24
* IPsec: AES256, SHA1, PFS24, SA élettartama 7,200 másodperc, és 20,480,000 KB (20 GB)

A parancsfájl az IPsec/IKE házirend vonatkozik, és lehetővé teszi, hogy a **UsePolicyBasedTrafficSelectors** beállítást a kapcsolathoz.

```powershell
$ipsecpolicy5 = New-AzureRmIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA1 -PfsGroup PFS24 -SALifeTimeSeconds 7200 -SADataSizeKilobytes 20480000

$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False -IpsecPolicies $ipsecpolicy5 -UsePolicyBasedTrafficSelectors $True
```

### <a name ="bgp"></a>(Választható) A BGP S2S VPN-kapcsolat használata
Az S2S VPN-kapcsolat létrehozásakor is használhat [a VPN Gateway a BGP](vpn-gateway-bgp-resource-manager-ps.md). Ez a megközelítés azzal két különbségek:

* A helyszíni címelőtagokat egyetlen fogadó cím is lehet. A helyszíni BGP társ IP-cím van megadva az alábbiak szerint:

    ```powershell
    New-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
    ```

* A kapcsolat létrehozásakor meg kell adni a **- EnableBGP** $true lehetőséget:

    ```powershell
    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
    ```

## <a name="next-steps"></a>Következő lépések
Aktív-aktív VPN-átjárók beállítása lépésenkénti útmutatót lásd: [aktív-aktív VPN-átjárók a létesítmények közötti és VNet – VNet kapcsolatokhoz konfigurálása](vpn-gateway-activeactive-rm-powershell.md).

