---
title: 'Hogyan OpenVPN konfigurálása Azure VPN Gateway átjárón: PowerShell| Microsoft Docs'
description: Az Azure VPN Gateway OpenVPN konfigurálásának lépései
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: cherylmc
ms.openlocfilehash: 609c2ef91fafe0ae955252a594292d861e772f87
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002963"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway"></a>Az Azure pont – hely VPN Gateway OpenVPN konfigurálása

Ez a cikk segít beállítani **OpenVPN® protokoll** Azure VPN gatewayen. A cikk feltételezi, hogy már a pont – hely munkakörnyezet. Ha nem, kövesse az utasításokat az 1. lépésben a pont – hely VPN létrehozása.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="vnet"></a>1. Pont – hely VPN létrehozása

Ha még nem rendelkezik egy működő pont – hely környezetben, útmutatást követve hozzon létre egyet. Lásd: [hozzon létre egy pont – hely VPN](vpn-gateway-howto-point-to-site-resource-manager-portal.md) létrehozása és pont – hely VPN-átjáró konfigurálása az Azure natív tanúsítványalapú hitelesítésének. 

> [!IMPORTANT]
> Az alapszintű Termékváltozathoz nem támogatott OpenVPN.

## <a name="enable"></a>2. Az átjáró OpenVPN engedélyezése

Az átjáró OpenVPN engedélyezése. Győződjön meg arról, hogy az átjáró már konfigurálva van a pont – hely (IKEv2- vagy SSTP) a következő parancsok futtatása előtt:

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>További lépések

OpenVPN ügyfelek konfigurálása, lásd: [OpenVPN konfigurálása az ügyfelek](vpn-gateway-howto-openvpn-clients.md).

**"OpenVPN" a OpenVPN Inc. védjegye.**
