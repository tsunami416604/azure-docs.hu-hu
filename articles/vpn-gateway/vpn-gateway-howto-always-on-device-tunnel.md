---
title: Always-On VPN-alagút konfigurálása
titleSuffix: Azure VPN Gateway
description: Megtudhatja, hogyan konfigurálhatja az Always on szolgáltatást, amely az eseményindítók alapján fenntart egy VPN-kapcsolatot, például a felhasználói bejelentkezést, a hálózati állapot változását vagy az eszközbeállítások aktív állapotát.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/12/2020
ms.author: cherylmc
ms.openlocfilehash: 24043576fca4910631ccddb3924303dd642c6842
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2020
ms.locfileid: "87927058"
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

## <a name="next-steps"></a>További lépések

Hibaelhárítási információkért lásd: [Azure pont – hely kapcsolati problémák](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
