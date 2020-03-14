---
title: Always-On VPN felhasználói alagút konfigurálása
titleSuffix: Azure VPN Gateway
description: Ez a cikk bemutatja, hogyan konfigurálhat egy always on VPN-felhasználói alagutat a VPN-átjáróhoz
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: cherylmc
ms.openlocfilehash: bf9dbd0cef19ad54ba6c3b58f2b9b3071b98bd93
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370981"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>AlwaysOn VPN felhasználói alagút konfigurálása

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>Az átjáró konfigurálása

 A [pont – hely VPN-kapcsolat konfigurálása](vpn-gateway-howto-point-to-site-resource-manager-portal.md) című cikk utasításait követve konfigurálja a VPN-átjárót a IKEv2 és a tanúsítványalapú hitelesítés használatára.

[!INCLUDE [user configuration](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>Profil eltávolítása

A profilok eltávolításához kövesse az alábbi lépéseket:

1. Futtassa az alábbi parancsot:

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. Válassza le a kapcsolatot, és törölje az **Automatikus csatlakozás** jelölőnégyzet jelölését.

   ![Felesleges tartalmak törlése](./media/vpn-gateway-howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>Következő lépések

Az esetlegesen előforduló kapcsolódási problémák elhárításához tekintse meg az [Azure pont – hely kapcsolati problémák](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)című témakört.
