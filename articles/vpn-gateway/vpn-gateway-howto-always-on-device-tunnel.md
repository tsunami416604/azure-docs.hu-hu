---
title: Always-On VPN-alagút konfigurálása
titleSuffix: Azure VPN Gateway
description: Megtudhatja, hogyan használhatja az Azure-ban állandó eszköz-alagutakat az átjárók és a Windows 10 always on használatával.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: ab951375fdf9c5317e2c3a2f67271666d0987142
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/03/2020
ms.locfileid: "89435847"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>AlwaysOn VPN-eszközalagút konfigurálása

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>Az átjáró konfigurálása

Konfigurálja úgy a VPN-átjárót, hogy IKEv2 és tanúsítványalapú hitelesítést használjon a [pont – hely VPN-kapcsolat konfigurálása](vpn-gateway-howto-point-to-site-resource-manager-portal.md) című cikk használatával.

## <a name="configure-the-device-tunnel"></a>Az eszköz bújtatásának konfigurálása

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>Profil eltávolítása

A profil eltávolításához futtassa a következő parancsot:

![Felesleges tartalmak törlése](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Következő lépések

Hibaelhárítási információkért lásd: [Azure pont – hely kapcsolati problémák](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
