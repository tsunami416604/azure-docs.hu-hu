---
title: 'Az OpenVPN konfigurálása az Azure VPN Gatewayban: PowerShell'
description: Megtudhatja, hogyan állíthatja be az Azure VPN Gateway OpenVPN-protokollját egy működő pont – hely környezethez. Szükség esetén pont – hely típusú VPN-t is létrehozhat.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 05/21/2019
ms.author: cherylmc
ms.openlocfilehash: 6c1c97bb0e4909bafe2d5ee9012190264b326e6c
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2020
ms.locfileid: "87926148"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway"></a>Az OpenVPN konfigurálása az Azure pont – hely kapcsolathoz VPN Gateway

Ebből a cikkből megtudhatja, hogyan állíthatja be az **OpenVPN® protokollt** az Azure VPN Gateway-on. A cikk feltételezi, hogy már rendelkezik egy működő pont – hely környezettel. Ha nem, az 1. lépésben szereplő utasításokat követve hozzon létre egy pont – hely típusú VPN-t.



## <a name="1-create-a-point-to-site-vpn"></a><a name="vnet"></a>1. pont – hely típusú VPN létrehozása

Ha még nem rendelkezik működő pont – hely környezettel, kövesse az utasításokat, és hozzon létre egyet. Lásd: [pont – hely típusú VPN létrehozása](vpn-gateway-howto-point-to-site-resource-manager-portal.md) és konfigurálása egy pont – hely típusú VPN-átjáróhoz natív Azure-Tanúsítványos hitelesítéssel. 

> [!IMPORTANT]
> Az OpenVPN esetében az alapszintű SKU nem támogatott.

## <a name="2-enable-openvpn-on-the-gateway"></a><a name="enable"></a>2. az OpenVPN engedélyezése az átjárón

Az OpenVPN engedélyezése az átjárón. Az alábbi parancsok futtatása előtt győződjön meg arról, hogy az átjáró már konfigurálva van a pont – hely (IKEv2 vagy SSTP) számára:

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>További lépések

Az OpenVPN-ügyfelek konfigurálásával kapcsolatban lásd: az [OpenVPN-ügyfelek konfigurálása](vpn-gateway-howto-openvpn-clients.md).

**Az "OpenVPN" az OpenVPN Inc védjegye.**
