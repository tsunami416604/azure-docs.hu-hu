---
title: Mindig bekapcsolt VPN felhasználói alagút konfigurálása
titleSuffix: Azure VPN Gateway
description: Ez a cikk azt ismerteti, hogyan konfigurálható a VPN-átjáró mindig bekapcsolt VPN-felhasználói alagútja
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: cherylmc
ms.openlocfilehash: 56934dd13661d8f623e673e2817e87618675c7ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502269"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>AlwaysOn VPN felhasználói alagút konfigurálása

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>Az átjáró konfigurálása

 A [Pont-hely VPN-kapcsolat konfigurálása](vpn-gateway-howto-point-to-site-resource-manager-portal.md) című cikk utasításaival konfigurálhatja a VPN-átjárót az IKEv2 és a tanúsítványalapú hitelesítés használatára.

## <a name="configure-a-user-tunnel"></a>Felhasználói alagút konfigurálása

[!INCLUDE [user configuration](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>Profil eltávolítása

Profil eltávolításához kövesse az alábbi lépéseket:

1. Futtassa az alábbi parancsot:

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. Válassza le a kapcsolatot, és törölje a jelet az **Automatikus csatlakozás** jelölőnégyzetből.

   ![Felesleges tartalmak törlése](./media/vpn-gateway-howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>További lépések

Az esetlegesen előforduló csatlakozási problémák elhárításához olvassa el az [Azure pont-hely kapcsolati problémáit.](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
