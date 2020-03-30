---
title: 'Azure VPN-átjáró: Egyéni útvonalak hirdetése a P2S VPN-ügyfelek számára'
description: Az egyéni útvonalak nak a helyek közötti ügyfelekre való hirdetésének lépései
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 11/11/2019
ms.author: cherylmc
ms.openlocfilehash: 7a904857b8aa0ed2aa18fc2a1b81fe31541e6f9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74151907"
---
# <a name="advertise-custom-routes-for-p2s-vpn-clients"></a>Egyéni útvonalak hirdetése P2S VPN-ügyfelek számára

Előfordulhat, hogy egyéni útvonalakat szeretne hirdetni az összes pont-hely VPN-ügyfélszámára. Ha például engedélyezte a virtuális hálózat tárolóvégpontjait, és azt szeretné, hogy a távoli felhasználók a VPN-kapcsolaton keresztül is hozzáférhessenek ezekhez a tárfiókokhoz. A tárolóvégpont IP-címét az összes távoli felhasználó számára hirdetheti, így a tárfiókba irányuló forgalom a VPN-alagúton, és nem a nyilvános interneten keresztül történik.

![Azure VPN Gateway többhelyes kapcsolat – példa](./media/vpn-gateway-p2s-advertise-custom-routes/custom-routes.png)

## <a name="to-advertise-custom-routes"></a>Egyéni útvonalak hirdetése

Egyéni útvonalak hirdetéséhez `Set-AzVirtualNetworkGateway cmdlet`használja a t. A következő példa bemutatja, hogyan hirdetheti az IP-címet a [Contoso tárfiók-táblákhoz.](https://contoso.table.core.windows.net)

1. Ping *contoso.table.core.windows.net,* és jegyezze fel az IP-címet. Példa:

    ```cmd
    C:\>ping contoso.table.core.windows.net
    Pinging table.by4prdstr05a.store.core.windows.net [13.88.144.250] with 32 bytes of data:
    ```

2. A következő PowerShell-parancsok futtatása:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute 13.88.144.250/32
    ```

3. Több egyéni útvonal hozzáadásához kóma és szóközök használatával válassza el egymástól a címeket. Példa:

    ```azurepowershell-interactive
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute x.x.x.x/xx , y.y.y.y/yy
    ```
## <a name="to-view-custom-routes"></a>Egyéni útvonalak megtekintése

Az alábbi példával tekintheti meg az egyéni útvonalakat:

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  $gw.CustomRoutes | Format-List
  ```
## <a name="to-delete-custom-routes"></a>Egyéni útvonalak törlése

Az alábbi példával törölheti az egyéni útvonalakat:

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute @0
  ```
## <a name="next-steps"></a>További lépések

További P2S útválasztási információkért olvassa el [a pont-hely útválasztás – szó.](vpn-gateway-about-point-to-site-routing.md)
