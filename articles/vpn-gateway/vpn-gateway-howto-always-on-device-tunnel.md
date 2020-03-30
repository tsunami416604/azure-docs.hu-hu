---
title: Mindig bekapcsolt VPN-alagút konfigurálása
titleSuffix: Azure VPN Gateway
description: A VPN-átjáró mindig vpn-alagúton való konfigurálásának lépései
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: cherylmc
ms.openlocfilehash: 9036992a354b41cd75735e5ccd895d7287a00e91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371741"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>AlwaysOn VPN-eszközalagút konfigurálása

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>Az átjáró konfigurálása

Konfigurálja úgy a VPN-átjárót, hogy az IKEv2 és a tanúsítványalapú hitelesítést használja a [Pont-hely VPN-kapcsolat konfigurálása](vpn-gateway-howto-point-to-site-resource-manager-portal.md) cikk használatával.

## <a name="configure-the-device-tunnel"></a>Az eszközalagút konfigurálása

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>Profil eltávolítása

A profil eltávolításához futtassa a következő parancsot:

![Felesleges tartalmak törlése](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>További lépések

A hibaelhárításról az [Azure pont-hely kapcsolati problémái című](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md) témakörben található.
