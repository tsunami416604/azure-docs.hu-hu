---
title: Egyéni útvonalak reklámozása pont – hely VPN-ügyfelek számára | Azure | Microsoft Docs
description: Az egyéni útvonalaknak a pont-hely típusú ügyfelekhez való reklámozásának lépései
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/25/2019
ms.author: cherylmc
ms.openlocfilehash: 18a0effcf5157ec2797707db78f9614ef12a4669
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71310242"
---
# <a name="advertise-custom-routes-for-p2s-vpn-clients"></a>Egyéni útvonalak reklámozása a P2S VPN-ügyfelek számára

Érdemes lehet egyéni útvonalakat reklámozni az összes pont – hely VPN-ügyfélre. Ha például engedélyezte a tárolási végpontokat a VNet, és azt szeretné, hogy a távoli felhasználók hozzáférhessenek ezekhez a Storage-fiókokhoz a VPN-kapcsolaton keresztül. Meghirdetheti a tároló végpontjának IP-címét az összes távoli felhasználó számára, hogy a Storage-fiók felé irányuló forgalom a VPN-alagúton, ne pedig a nyilvános interneten legyen.

## <a name="to-advertise-custom-routes"></a>Egyéni útvonalak meghirdetése

Egyéni útvonalak meghirdetéséhez használja a `Set-AzVirtualNetworkGateway cmdlet`következőt:. Az alábbi példa bemutatja, hogyan hirdetheti meg az IP-címet a [Conotoso Storage-fiókok tábláiban](https://contoso.table.core.windows.net).

1. Pingelje a *contoso.table.Core.Windows.net* , és jegyezze fel az IP-címet. Példa:

    ```cmd
    C:\>ping contoso.table.core.windows.net
    Pinging table.by4prdstr05a.store.core.windows.net [13.88.144.250] with 32 bytes of data:
    ```

2. Futtassa a következő PowerShell-parancsokat:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute 13.88.144.250/32
    ```

3. Több egyéni útvonal hozzáadásához használjon kómát és szóközöket a címek elválasztásához. Példa:

    ```azurepowershell-interactive
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute x.x.x.x/xx , y.y.y.y/yy
    ```
## <a name="to-view-custom-routes"></a>Egyéni útvonalak megtekintése

Az alábbi példa az egyéni útvonalak megtekintésére használható:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
    $gw.CustomRoutes | Format-List
    ```

## <a name="next-steps"></a>További lépések

További P2S-útválasztási információk: [Tudnivalók a pont – hely útválasztásról](vpn-gateway-about-point-to-site-routing.md).
