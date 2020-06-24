---
title: Always-On VPN felhasználói alagút konfigurálása
titleSuffix: Azure VPN Gateway
description: Ez a cikk bemutatja, hogyan konfigurálhat egy always on VPN-felhasználói alagutat a VPN-átjáróhoz
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/12/2020
ms.author: cherylmc
ms.openlocfilehash: 8cfe1d6434c0f5765196776ae9f6712fe14c52a3
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2020
ms.locfileid: "84984123"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>AlwaysOn VPN felhasználói alagút konfigurálása

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>Az átjáró konfigurálása

 A [pont – hely VPN-kapcsolat konfigurálása](vpn-gateway-howto-point-to-site-resource-manager-portal.md) című cikk utasításait követve konfigurálja a VPN-átjárót a IKEv2 és a tanúsítványalapú hitelesítés használatára.

## <a name="configure-a-user-tunnel"></a>Felhasználói alagút konfigurálása

[!INCLUDE [user configuration](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>Profil eltávolítása

A profilok eltávolításához kövesse az alábbi lépéseket:

1. Futtassa az alábbi parancsot:

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. Válassza le a kapcsolatot, és törölje az **Automatikus csatlakozás** jelölőnégyzet jelölését.

   ![Felesleges tartalmak törlése](./media/vpn-gateway-howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>További lépések

Az esetlegesen előforduló kapcsolódási problémák elhárításához tekintse meg az [Azure pont – hely kapcsolati problémák](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)című témakört.
