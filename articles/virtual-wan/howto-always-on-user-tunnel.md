---
title: Mindig bekapcsolt VPN felhasználói alagút konfigurálása
titleSuffix: Azure Virtual WAN
description: Ez a cikk azt ismerteti, hogyan konfigurálható a Virtuális WAN-hálózat mindig bekapcsolt VPN-felhasználói alagútja
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: cherylmc
ms.openlocfilehash: dd5b215b143fbaf487325744a158bb8b05707951
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502866"
---
# <a name="configure-an-always-on-vpn-user-tunnel-for-virtual-wan"></a>Mindig bekapcsolt VPN felhasználói alagút konfigurálása a Virtual WAN számára

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>Előfeltételek

Létre kell hoznia egy pont-hely konfigurációt, és szerkesztheti a virtuális hub-hozzárendelést. Az utasításokat a következő szakaszokban találja:

* [Pont–hely konfiguráció létrehozása](virtual-wan-point-to-site-portal.md#p2sconfig)
* [A központi hozzárendelés szerkesztése](virtual-wan-point-to-site-portal.md#edit)

## <a name="configure-a-user-tunnel"></a>Felhasználói alagút konfigurálása

[!INCLUDE [user tunnel](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>Profil eltávolítása

Profil eltávolításához kövesse az alábbi lépéseket:

1. Futtassa az alábbi parancsot:

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. Válassza le a kapcsolatot, és törölje a jelet az **Automatikus csatlakozás** jelölőnégyzetből.

   ![Felesleges tartalmak törlése](./media/howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>További lépések

A Virtual WAN-ról további információt a GYAKORI KÉRDÉSEK című [témakörben talál.](virtual-wan-faq.md)
