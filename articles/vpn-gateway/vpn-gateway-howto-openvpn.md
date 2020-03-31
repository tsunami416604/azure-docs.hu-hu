---
title: 'Az OpenVPN konfigurálása az Azure VPN-átjárón: PowerShell'
description: Az OpenVPN azure VPN-átjáróhoz való konfigurálásának lépései
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: cherylmc
ms.openlocfilehash: 7505420cc31fe751ecc0c114a89fea0734cbc6cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162407"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway"></a>OpenVPN konfigurálása az Azure pont-hely VPN átjárójához

Ez a cikk segít az **OpenVPN® Protocol** beállítását az Azure VPN-átjárón. A cikk feltételezi, hogy már rendelkezik egy munkapont-hely környezetben. Ha nem, az 1.



## <a name="1-create-a-point-to-site-vpn"></a><a name="vnet"></a>1. Hozzon létre egy pont-hely VPN

Ha még nem rendelkezik működő pont-hely környezettel, kövesse az utasításokat, hogy hozzon létre egyet. Lásd: [Pont-hely VPN létrehozása](vpn-gateway-howto-point-to-site-resource-manager-portal.md) és konfigurálása egy pont-hely VPN átjáró natív Azure-tanúsítvány hitelesítéssel. 

> [!IMPORTANT]
> Az alaptermékváltozat nem támogatott az OpenVPN esetében.

## <a name="2-enable-openvpn-on-the-gateway"></a><a name="enable"></a>2. OpenVPN engedélyezése az átjárón

Engedélyezze az OpenVPN-t az átjárón. A következő parancsok futtatása előtt győződjön meg arról, hogy az átjáró már konfigurálva van a pont-hely (IKEv2 vagy SSTP) számára:

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>További lépések

Az OpenVPN ügyfelek konfigurálása az [OpenVPN-ügyfelek konfigurálása című témakörben látható.](vpn-gateway-howto-openvpn-clients.md)

**Az "OpenVPN" az OpenVPN Inc. védjegye.**
