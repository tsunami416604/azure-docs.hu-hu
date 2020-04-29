---
title: Always-On VPN felhasználói alagút konfigurálása
titleSuffix: Azure Virtual WAN
description: Ez a cikk bemutatja, hogyan konfigurálhat egy always on VPN felhasználói alagutat a virtuális WAN-hoz
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: cherylmc
ms.openlocfilehash: dd5b215b143fbaf487325744a158bb8b05707951
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79502866"
---
# <a name="configure-an-always-on-vpn-user-tunnel-for-virtual-wan"></a>Always On VPN felhasználói alagút konfigurálása a virtuális WAN-hoz

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>Előfeltételek

Létre kell hoznia egy pont – hely konfigurációt, és szerkesztenie kell a virtuális központ hozzárendelését. A következő részekben talál útmutatást:

* [Pont–hely konfiguráció létrehozása](virtual-wan-point-to-site-portal.md#p2sconfig)
* [A hub-hozzárendelés szerkesztése](virtual-wan-point-to-site-portal.md#edit)

## <a name="configure-a-user-tunnel"></a>Felhasználói alagút konfigurálása

[!INCLUDE [user tunnel](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>Profil eltávolítása

A profilok eltávolításához kövesse az alábbi lépéseket:

1. Futtassa az alábbi parancsot:

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. Válassza le a kapcsolatot, és törölje az **Automatikus csatlakozás** jelölőnégyzet jelölését.

   ![Felesleges tartalmak törlése](./media/howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>További lépések

A virtuális WAN-ról további információt a [Gyakori kérdések](virtual-wan-faq.md)című témakörben talál.
