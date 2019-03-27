---
title: Azure S2S VPN-kapcsolatok létrehozása és felügyelete a PowerShell használatával | Microsoft Docs
description: Oktatóanyag – S2S VPN-kapcsolatok létrehozása és felügyelete az Azure PowerShell-modullal
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 02/11/2019
ms.author: yushwang
ms.custom: mvc
ms.openlocfilehash: cac68506803cda2c4e537feac84da2a82bc128bd
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58444288"
---
# <a name="tutorial-create-and-manage-s2s-vpn-connections-using-powershell"></a>Oktatóanyag: PowerShell-lel S2S VPN-kapcsolatok létrehozása és kezelése

Az Azure S2S VPN-kapcsolatok biztonságos, helyszínek közötti kapcsolatot biztosítanak az ügyfél helyszínei és az Azure között. Ez az oktatóanyag bemutatja az IPsec S2S VPN-kapcsolatok életciklusát, például az S2S VPN-kapcsolatok létrehozását és felügyeletét. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * S2S VPN-kapcsolat létrehozása
> * Kapcsolat tulajdonságainak frissítése: előmegosztott kulcs, BGP, IPsec/IKE-szabályzat
> * További VPN-kapcsolatok hozzáadása
> * VPN-kapcsolat törlése

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az alábbi diagram az oktatóanyagban használt topológiát mutatja:

![Helyek közötti VPN-kapcsolat ábrája](./media/vpn-gateway-tutorial-vpnconnection-powershell/site-to-site-diagram.png)

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

## <a name="requirements"></a>Követelmények

Az első oktatóanyag elvégzéséhez: [VPN-átjáró létrehozása az Azure PowerShell-lel](vpn-gateway-tutorial-create-gateway-powershell.md) hozhat létre a következő erőforrásokat:

1. Erőforráscsoport (TestRG1), virtuális hálózat (VNet1) és az átjáró-alhálózat
2. VPN-átjáró (VNet1GW)

A virtuális hálózat paramétereinek értékei alább vannak felsorolva. Megjegyzés: a további értékek a helyi hálózati átjáró a helyszíni hálózatot képviselő. Módosítsa az értékeket az alábbi a környezet és a hálózat beállítása, majd másolja be a állíthatja be a változókat a jelen oktatóanyag alapján. Ha a Cloud Shell-munkamenetek túllépi az időkorlátot, vagy szeretné használni egy másik PowerShell-ablakot, másolja és illessze be a változókat az új munkamenet, és folytassa az oktatóanyagot.

>[!NOTE]
> Ha ez egy kapcsolatot használ, mindenképpen módosítsa az értékeket a helyszíni hálózat megfelelően. Oktatóanyag keretében csak futtatja ezeket a lépéseket, ha nem kell módosításokat, de a kapcsolat nem fog működni.
>

```azurepowershell-interactive
# Virtual network
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "East US"
$VNet1Prefix = "10.1.0.0/16"
$VNet1ASN    = 65010
$Gw1         = "VNet1GW"

# On-premises network - LNGIP1 is the VPN device public IP address
$LNG1        = "VPNsite1"
$LNGprefix1  = "10.101.0.0/24"
$LNGprefix2  = "10.101.1.0/24"
$LNGIP1      = "5.4.3.2"

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

A helyi hálózati átjáró létrehozása a [New-AzLocalNetworkGateway](https://docs.microsoft.com/powershell/module/az.network/new-azlocalnetworkgateway) parancsot.

```azurepowershell-interactive
New-AzLocalNetworkGateway -Name $LNG1 -ResourceGroupName $RG1 `
  -Location 'East US' -GatewayIpAddress $LNGIP1 -AddressPrefix $LNGprefix1,$LNGprefix2
```

## <a name="create-a-s2s-vpn-connection"></a>S2S VPN-kapcsolat létrehozása

Ezután hozza létre a virtuális hálózati átjáró és a VPN-eszköz, a Site-to-Site VPN-kapcsolat a [New-AzVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworkgatewayconnection). Vegye figyelembe, hogy helyek közötti VPN-kapcsolat esetében a „-ConnectionType” értéke: *IPsec*.

```azurepowershell-interactive
$vng1 = Get-AzVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
$lng1 = Get-AzLocalNetworkGateway   -Name $LNG1 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection1 -ResourceGroupName $RG1 `
  -Location $Location1 -VirtualNetworkGateway1 $vng1 -LocalNetworkGateway2 $lng1 `
  -ConnectionType IPsec -SharedKey "Azure@!b2C3"
```

BGP használata esetén egészítse ki a nem kötelező „**-EnableBGP $True**” tulajdonsággal, amely engedélyezi a BGP-t a kapcsolaton. Ez alapértelmezés szerint le van tiltva.

## <a name="update-the-vpn-connection-pre-shared-key-bgp-and-ipsecike-policy"></a>A VPN kapcsolat előmegosztott kulcs, BGP és IPsec/IKE-szabályzat tulajdonságainak frissítése

### <a name="view-and-update-your-pre-shared-key"></a>Az előmegosztott kulcs megtekintése és frissítése

Az Azure S2S VPN-kapcsolat egy előmegosztott kulcsot (titkot) használ a helyszíni VPN-eszköz és az Azure VPN-átjáró közötti hitelesítéshez. Megtekintheti és frissíteni a kapcsolatot az előre megosztott kulcs [Get-AzVirtualNetworkGatewayConnectionSharedKey](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetworkgatewayconnectionsharedkey) és [Set-AzVirtualNetworkGatewayConnectionSharedKey](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworkgatewayconnectionsharedkey).

> [!IMPORTANT]
> Az előmegosztott kulcs egy **nyomtatható ASCII-karaktereket** tartalmazó, 128 karakternél nem hosszabb karakterlánc.

Ez a parancs mutatja meg a kapcsolat előmegosztott kulcsát:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayConnectionSharedKey `
  -Name $Connection1 -ResourceGroupName $RG1
```

A kimenet lesz "**Azure\@! b2C3**" fenti példát követve. Használja az alábbi parancsot az előmegosztott kulcs értékét a módosítása "**Azure\@! _b2 = C3**":

```azurepowershell-interactive
Set-AzVirtualNetworkGatewayConnectionSharedKey `
  -Name $Connection1 -ResourceGroupName $RG1 `
  -Value "Azure@!_b2=C3"
```

### <a name="enable-bgp-on-vpn-connection"></a>BGP engedélyezése VPN-kapcsolaton

Az Azure VPN-átjárók támogatják a BGP dinamikus útválasztási protokollt. Minden egyes kapcsolaton engedélyezheti a BGP-t, attól függően, hogy használ-e BGP-t a helyszíni hálózatokon és eszközökön. Mielőtt egy kapcsolaton engedélyezné a BGP-t, adja meg a következő BGP-tulajdonságokat:

* Azure VPN ASN (autonóm rendszer száma)
* Helyszíni helyi hálózati átjáró ASN-száma
* Helyszíni helyi hálózati átjáró BGP-társának IP-címe

Ha nem konfigurálta a BGP-tulajdonságokat, a következő parancsokat a VPN-átjáró és a helyi hálózati átjáró adja hozzá ezeket a tulajdonságokat: [Set-AzVirtualNetworkGateway](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworkgateway) and [Set-AzLocalNetworkGateway](https://docs.microsoft.com/powershell/module/az.network/set-azlocalnetworkgateway).

Használja az alábbi példa a BGP-tulajdonságok konfigurálása:

```azurepowershell-interactive
$vng1 = Get-AzVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $vng1 -Asn $VNet1ASN

$lng1 = Get-AzLocalNetworkGateway   -Name $LNG1 -ResourceGroupName $RG1
Set-AzLocalNetworkGateway -LocalNetworkGateway $lng1 `
  -Asn $LNGASN1 -BgpPeeringAddress $BGPPeerIP1
```

Enable BGP with [Set-AzVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworkgatewayconnection).

```azurepowershell-interactive
$connection = Get-AzVirtualNetworkGatewayConnection `
  -Name $Connection1 -ResourceGroupName $RG1

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection `
  -EnableBGP $True
```

A BGP letiltásához módosítsa az -EnableBGP tulajdonság értékét a **$False** értékre. Vonatkozó részletes útmutatásért olvassa el [a BGP az Azure VPN-átjárókon való használatát](vpn-gateway-bgp-resource-manager-ps.md) bemutató cikket.

### <a name="apply-a-custom-ipsecike-policy-on-the-connection"></a>Egyéni IPsec/IKE-szabályzat alkalmazása a kapcsolatra

Egy választható IPsec/IKE-szabályzat alkalmazásával megadhatja a kapcsolat IPsec/IKE titkosítási algoritmusai és kulcserősségei pontos kombinációját ahelyett, hogy az [alapértelmezett javaslatokat](vpn-gateway-about-vpn-devices.md#ipsec) használná. Az alábbi mintaszkript egy eltérő IPsec/IKE-szabályzatot hoz létre a következő algoritmusokkal és paraméterekkel:

* IKEv2: AES256, SHA256, DHGroup14
* IPsec: AES128, SHA1, PFS14, 102,400,000 KB & SA élettartama 14400 másodperc

```azurepowershell-interactive
$connection = Get-AzVirtualNetworkGatewayConnection -Name $Connection1 `
                -ResourceGroupName $RG1
$newpolicy  = New-AzIpsecPolicy `
                -IkeEncryption AES256 -IkeIntegrity SHA256 -DhGroup DHGroup14 `
                -IpsecEncryption AES128 -IpsecIntegrity SHA1 -PfsGroup PFS2048 `
                -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection `
  -IpsecPolicies $newpolicy
```

Az algoritmusok teljes listájáért és az utasításokért olvassa el [az S2S vagy a virtuális hálózatok közötti kapcsolatokra vonatkozó IPsec/IKE-szabályzatokat](vpn-gateway-ipsecikepolicy-rm-powershell.md) bemutató cikket.

## <a name="add-another-s2s-vpn-connection"></a>Másik S2S VPN-kapcsolat hozzáadása

További S2S VPN-kapcsolat hozzáadása VPN-átjáróhoz, hozzon létre egy másik helyi hálózati átjárót, és hozzon létre egy új kapcsolatot az új helyi hálózati átjáró és a VPN-átjáró között. Használja az alábbi példák, és gondoskodik róla, hogy módosítsa a változókat, hogy a saját hálózati konfigurációját tükrözzék.

```azurepowershell-interactive
# On-premises network - LNGIP2 is the VPN device public IP address
$LNG2        = "VPNsite2"
$Location2   = "West US"
$LNGprefix21 = "10.102.0.0/24"
$LNGprefix22 = "10.102.1.0/24"
$LNGIP2      = "4.3.2.1"
$Connection2 = "VNet1ToSite2"

New-AzLocalNetworkGateway -Name $LNG2 -ResourceGroupName $RG1 `
  -Location $Location2 -GatewayIpAddress $LNGIP2 -AddressPrefix $LNGprefix21,$LNGprefix22

$vng1 = Get-AzVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
$lng2 = Get-AzLocalNetworkGateway   -Name $LNG2 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection2 -ResourceGroupName $RG1 `
  -Location $Location1 -VirtualNetworkGateway1 $vng1 -LocalNetworkGateway2 $lng2 `
  -ConnectionType IPsec -SharedKey "AzureA1%b2_C3+"
```

Most már két S2S-kapcsolattal rendelkezik az Azure VPN-átjáróhoz.

![Többhelyes VPN-kapcsolatok](./media/vpn-gateway-tutorial-vpnconnection-powershell/multisite-connections.png)

## <a name="delete-a-s2s-vpn-connection"></a>S2S VPN-kapcsolat törlése

Az S2S VPN-kapcsolat törlése [Remove-AzVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetworkgatewayconnection).

```azurepowershell-interactive
Remove-AzVirtualNetworkGatewayConnection -Name $Connection2 -ResourceGroupName $RG1
```

Törölje a helyi hálózati átjárót, ha már nincs rá szüksége. A helyi hálózati átjárók nem törölhetőek, ha további társított kapcsolatokkal rendelkeznek.

```azurepowershell-interactive
Remove-AzVirtualNetworkGatewayConnection -Name $LNG2 -ResourceGroupName $RG1
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha ezt a konfigurációt egy prototípust, tesztelési vagy proof-of-concept-telepítés része, használhatja a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) paranccsal eltávolítható az erőforráscsoport, a VPN-átjáró és az összes kapcsolódó erőforrás.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $RG1
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
