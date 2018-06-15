---
title: Azure S2S VPN-kapcsolatok létrehozása és felügyelete a PowerShell használatával | Microsoft Docs
description: Oktatóanyag – S2S VPN-kapcsolatok létrehozása és felügyelete az Azure PowerShell-modullal
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure
ms.date: 05/08/2018
ms.author: yushwang
ms.custom: mvc
ms.openlocfilehash: da077f013c558448be63dce9b215ded99362d22e
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012941"
---
# <a name="create-and-manage-s2s-vpn-connections-with-the-azure-powershell-module"></a>S2S VPN-kapcsolatok létrehozása és felügyelete az Azure PowerShell-modullal

Az Azure S2S VPN-kapcsolatok biztonságos, helyszínek közötti kapcsolatot biztosítanak az ügyfél helyszínei és az Azure között. Ez az oktatóanyag bemutatja az IPsec S2S VPN-kapcsolatok életciklusát, például az S2S VPN-kapcsolatok létrehozását és felügyeletét. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * S2S VPN-kapcsolat létrehozása
> * Kapcsolat tulajdonságainak frissítése: előmegosztott kulcs, BGP, IPsec/IKE-szabályzat
> * További VPN-kapcsolatok hozzáadása
> * VPN-kapcsolat törlése

Az alábbi diagram az oktatóanyagban használt topológiát mutatja:

![Helyek közötti VPN-kapcsolat ábrája](./media/vpn-gateway-tutorial-vpnconnection-powershell/site-to-site-diagram.png)

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az Azure PowerShell-modul 5.3-as vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="requirements"></a>Követelmények

Az első oktatóanyag ([VPN-átjáró létrehozása az Azure PowerShell-lel](vpn-gateway-tutorial-create-gateway-powershell.md)) elvégzésével hozza létre a következő erőforrásokat:

1. Erőforráscsoport (TestRG1), virtuális hálózat (VNet1) és átjáró-alhálózat
2. VPN-átjáró (VNet1GW)

A virtuális hálózat paramétereinek értékei alább vannak felsorolva. Figyelje meg a helyi hálózati átjáró további értékeit, amelyek a helyszíni hálózatot jelölik. A környezeti és hálózati beállításainak megfelelően módosítsa az értékeket.

```azurepowershell-interactive
# Virtual network
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "East US"
$VNet1Prefix = "10.1.0.0/16"
$VNet1ASN    = 65010
$Gw1         = "VNet1GW"

# On-premises network
$LNG1        = "VPNsite1"
$LNGprefix1  = "10.101.0.0/24"
$LNGprefix2  = "10.101.1.0/24"
$LNGIP1      = "YourDevicePublicIP"

# Optional - on-premises BGP properties
$LNGASN1     = 65011
$BGPPeerIP1  = "10.101.1.254"

# Connection
$Connection1 = "VNet1ToSite1"
```

Az S2S VPN-kapcsolatot létrehozó munkafolyamat egyszerű:

1. A helyszíni hálózatot jelölő helyi hálózati átjáró létrehozása
2. Kapcsolat létrehozása az Azure VPN-átjáró és a helyi hálózati átjáró között

## <a name="create-a-local-network-gateway"></a>Helyi hálózati átjáró létrehozása

A helyi hálózati átjáró a helyszíni hálózatot jelöli. A helyszíni hálózati átjáró tulajdonságait a helyi hálózati átjárón adhatja meg, beleértve a következőket:

* A VPN-eszköz nyilvános IP-címe
* A helyszíni címtér
* (Nem kötelező) A BGP attribútumai (a BGP-társ IP-címe és AS-száma)

Hozzon létre egy helyi hálózati átjárót a [New-AzureRmLocalNetworkGateway](/powershell/module/azurerm.resources/new-azurermlocalnetworkgateway) paranccsal.

```azurepowershell-interactive
New-AzureRmLocalNetworkGateway -Name $LNG1 -ResourceGroupName $RG1 `
  -Location 'East US' -GatewayIpAddress $LNGIP1 -AddressPrefix $LNGprefix1,$LNGprefix2
```

## <a name="create-a-s2s-vpn-connection"></a>S2S VPN-kapcsolat létrehozása

Ezután hozzon létre egy helyek közötti VPN-kapcsolatot a virtuális hálózati átjáró és a VPN-eszköz között a [New-AzureRmVirtualNetworkGatewayConnection](/powershell/module/azurerm.resources/new-azurermvirtualnetworkgatewayconnection) paranccsal. Vegye figyelembe, hogy helyek közötti VPN-kapcsolat esetében a „-ConnectionType” értéke: *IPsec*.

```azurepowershell-interactive
$vng1 = Get-AzureRmVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
$lng1 = Get-AzureRmLocalNetworkGateway   -Name $LNG1 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection1 -ResourceGroupName $RG1 `
  -Location $Location1 -VirtualNetworkGateway1 $vng1 -LocalNetworkGateway2 $lng1 `
  -ConnectionType IPsec -SharedKey "Azure@!b2C3"
```

BGP használata esetén egészítse ki a nem kötelező „**-EnableBGP $True**” tulajdonsággal, amely engedélyezi a BGP-t a kapcsolaton. Ez alapértelmezés szerint le van tiltva.

## <a name="update-the-vpn-connection-pre-shared-key-bgp-and-ipsecike-policy"></a>A VPN kapcsolat előmegosztott kulcs, BGP és IPsec/IKE-szabályzat tulajdonságainak frissítése

### <a name="view-and-update-your-pre-shared-key"></a>Az előmegosztott kulcs megtekintése és frissítése

Az Azure S2S VPN-kapcsolat egy előmegosztott kulcsot (titkot) használ a helyszíni VPN-eszköz és az Azure VPN-átjáró közötti hitelesítéshez. A kapcsolat előmegosztott kulcsát a [Get-AzureRmVirtualNetworkGatewayConnectionSharedKey](/powershell/module/azurerm.resources/get-azurermvirtualnetworkgatewayconnectionsharedkey) és a [Set-AzureRmVirtualNetworkGatewayConnectionSharedKey](/powershell/module/azurerm.resources/set-azurermvirtualnetworkgatewayconnectionsharedkey) parancsokkal tekintheti meg és frissítheti.

> [!IMPORTANT]
> Az előmegosztott kulcs egy **nyomtatható ASCII-karaktereket** tartalmazó, 128 karakternél nem hosszabb karakterlánc.

Ez a parancs mutatja meg a kapcsolat előmegosztott kulcsát:

```azurepowershell-interactive
Get-AzureRmVirtualNetworkGatewayConnectionSharedKey `
  -Name $Connection1 -ResourceGroupName $RG1
```

A fenti példát követve a kimenet a következő lesz: **Azure@!b2C3**. Az alábbi parancs használatával módosítsa az előmegosztott kulcs értékét a következőre: **Azure@!_b2=C3**:

```azurepowershell-interactive
Set-AzureRmVirtualNetworkGatewayConnectionSharedKey `
  -Name $Connection1 -ResourceGroupName $RG1 `
  -Value "Azure@!_b2=C3"
```

### <a name="enable-bgp-on-vpn-connection"></a>BGP engedélyezése VPN-kapcsolaton

Az Azure VPN-átjárók támogatják a BGP dinamikus útválasztási protokollt. Minden egyes kapcsolaton engedélyezheti a BGP-t, attól függően, hogy használ-e BGP-t a helyszíni hálózatokon és eszközökön. Mielőtt egy kapcsolaton engedélyezné a BGP-t, adja meg a következő BGP-tulajdonságokat:

* Azure VPN ASN (autonóm rendszer száma)
* Helyszíni helyi hálózati átjáró ASN-száma
* Helyszíni helyi hálózati átjáró BGP-társának IP-címe

Ha nem konfigurálta a BGP-tulajdonságokat, a következő parancsokkal adhatja hozzá azokat a VPN-átjáróhoz és a helyi hálózati átjáróhoz: [Set-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.resources/set-azurermvirtualnetworkgateway) és [Set-AzureRmLocalNetworkGateway](/powershell/module/azurerm.resources/set-azurermlocalnetworkgateway).

```azurepowershell-interactive
$vng1 = Get-AzureRmVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $vng1 -Asn $VNet1ASN

$lng1 = Get-AzureRmLocalNetworkGateway   -Name $LNG1 -ResourceGroupName $RG1
Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $lng1 `
  -Asn $LNGASN1 -BgpPeeringAddress $BGPPeerIP1
```

Engedélyezze a BGP-t a [Set-AzureRmVirtualNetworkGatewayConnection](/powershell/module/azurerm.resources/set-azurermvirtualnetworkgatewayconnection) paranccsal.

```azurepowershell-interactive
$connection = Get-AzureRmVirtualNetworkGatewayConnection `
  -Name $Connection1 -ResourceGroupName $RG1

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection `
  -EnableBGP $True
```

A BGP letiltásához módosítsa az -EnableBGP tulajdonság értékét a **$False** értékre. Vonatkozó részletes útmutatásért olvassa el [a BGP az Azure VPN-átjárókon való használatát](vpn-gateway-bgp-resource-manager-ps.md) bemutató cikket.

### <a name="apply-a-custom-ipsecike-policy-on-the-connection"></a>Egyéni IPsec/IKE-szabályzat alkalmazása a kapcsolatra

Egy választható IPsec/IKE-szabályzat alkalmazásával megadhatja a kapcsolat IPsec/IKE titkosítási algoritmusai és kulcserősségei pontos kombinációját ahelyett, hogy az [alapértelmezett javaslatokat](vpn-gateway-about-vpn-devices.md#ipsec) használná. Az alábbi mintaszkript egy eltérő IPsec/IKE-szabályzatot hoz létre a következő algoritmusokkal és paraméterekkel:

* IKEv2: AES256, SHA256, DHGroup14
* IPsec: AES128, SHA1, PFS14, 14 400 másodperces és 102 400 000 KB-os SA-élettartam

```azurepowershell-interactive
$connection = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection1 `
                -ResourceGroupName $RG1
$newpolicy  = New-AzureRmIpsecPolicy `
                -IkeEncryption AES256 -IkeIntegrity SHA256 -DhGroup DHGroup14 `
                -IpsecEncryption AES128 -IpsecIntegrity SHA1 -PfsGroup PFS2048 `
                -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection `
  -IpsecPolicies $newpolicy
```

Az algoritmusok teljes listájáért és az utasításokért olvassa el [az S2S vagy a virtuális hálózatok közötti kapcsolatokra vonatkozó IPsec/IKE-szabályzatokat](vpn-gateway-ipsecikepolicy-rm-powershell.md) bemutató cikket.

## <a name="add-another-s2s-vpn-connection"></a>Másik S2S VPN-kapcsolat hozzáadása

További S2S VPN-kapcsolat ugyanahhoz a VPN-átjáróhoz való hozzáadásához hozzon létre egy másik helyi hálózati átjárót, valamint egy új kapcsolatot az új helyi hálózati átjáró és a VPN-átjáró között. A cikkben szereplő példát követve.

```azurepowershell-interactive
# On-premises network
$LNG2        = "VPNsite2"
$Location2   = "West US"
$LNGprefix21 = "10.102.0.0/24"
$LNGprefix22 = "10.102.1.0/24"
$LNGIP2      = "YourDevicePublicIP"
$Connection2 = "VNet1ToSite2"

New-AzureRmLocalNetworkGateway -Name $LNG2 -ResourceGroupName $RG1 `
  -Location $Location2 -GatewayIpAddress $LNGIP2 -AddressPrefix $LNGprefix21,$LNGprefix22

$vng1 = Get-AzureRmVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
$lng2 = Get-AzureRmLocalNetworkGateway   -Name $LNG2 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection2 -ResourceGroupName $RG1 `
  -Location $Location1 -VirtualNetworkGateway1 $vng1 -LocalNetworkGateway2 $lng2 `
  -ConnectionType IPsec -SharedKey "AzureA1%b2_C3+"
```

Most már két S2S-kapcsolattal rendelkezik az Azure VPN-átjáróhoz.

![Többhelyes VPN-kapcsolatok](./media/vpn-gateway-tutorial-vpnconnection-powershell/multisite-connections.png)

## <a name="delete-a-s2s-vpn-connection"></a>S2S VPN-kapcsolat törlése

Az S2S VPN-kapcsolatokat a [Remove-AzureRmVirtualNetworkGatewayConnection](/powershell/module/azurerm.resources/remove-azurermvirtualnetworkgatewayconnection) paranccsal törölheti.

```azurepowershell-interactive
Remove-AzureRmVirtualNetworkGatewayConnection -Name $Connection2 -ResourceGroupName $RG1
```

Törölje a helyi hálózati átjárót, ha már nincs rá szüksége. A helyi hálózati átjárók nem törölhetőek, ha további társított kapcsolatokkal rendelkeznek.

```azurepowershell-interactive
Remove-AzureRmVirtualNetworkGatewayConnection -Name $LNG2 -ResourceGroupName $RG1
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az S2S VPN-kapcsolatok létrehozásának és kezelésének módjával ismerkedett meg, például:

> [!div class="checklist"]
> * S2S VPN-kapcsolat létrehozása
> * Kapcsolat tulajdonságainak frissítése: előmegosztott kulcs, BGP, IPsec/IKE-szabályzat
> * További VPN-kapcsolatok hozzáadása
> * VPN-kapcsolat törlése

Folytassa a következő oktatóanyaggal, amelyben a helyek közötti (S2S), a virtuális hálózatok közötti (VNet-to-VNet) és a pont–hely (P2S) kapcsolatokkal ismerkedhet meg.

> [!div class="nextstepaction"]
> * [Virtuális hálózatok közötti kapcsolatok létrehozása](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Pont–hely kapcsolatok létrehozása](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
