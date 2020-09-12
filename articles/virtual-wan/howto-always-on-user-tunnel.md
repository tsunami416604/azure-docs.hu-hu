---
title: Always-On VPN felhasználói alagút konfigurálása
titleSuffix: Azure Virtual WAN
description: Ez a cikk bemutatja, hogyan konfigurálhat egy always on VPN felhasználói alagutat a virtuális WAN-hoz
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/10/2020
ms.author: cherylmc
ms.openlocfilehash: 14790d4be91ffd0463436b6ca1d5c8794c102697
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2020
ms.locfileid: "90016351"
---
# <a name="configure-an-always-on-vpn-user-tunnel-for-virtual-wan"></a>Always On VPN felhasználói alagút konfigurálása a virtuális WAN-hoz

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>Előfeltételek

Létre kell hoznia egy pont – hely konfigurációt, és szerkesztenie kell a virtuális központ hozzárendelését. A következő részekben talál útmutatást:

* [Pont–hely konfiguráció létrehozása](virtual-wan-point-to-site-portal.md#p2sconfig)
* [Hub létrehozása a P2S-átjáróval](virtual-wan-point-to-site-portal.md#hub)

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

## <a name="next-steps"></a>Következő lépések

A virtuális WAN-ról további információt a [Gyakori kérdések](virtual-wan-faq.md)című témakörben talál.
