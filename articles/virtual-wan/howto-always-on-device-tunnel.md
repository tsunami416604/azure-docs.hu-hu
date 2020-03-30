---
title: Mindig bekapcsolt VPN-alagút konfigurálása
titleSuffix: Azure Virtual WAN
description: A Mindig a VPN-eszközalagút konfigurálásának lépései a Virtual WAN-hoz
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: cherylmc
ms.openlocfilehash: e3eea639eaa52c07e877476e9215144e916618d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502853"
---
# <a name="configure-an-always-on-vpn-device-tunnel-for-virtual-wan"></a>Mindig bekapcsolt VPN-eszközbújtatás konfigurálása a Virtual WAN számára

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>Előfeltételek

Létre kell hoznia egy pont-hely konfigurációt, és szerkesztheti a virtuális hub-hozzárendelést. Az utasításokat a következő szakaszokban találja:

* [Pont–hely konfiguráció létrehozása](virtual-wan-point-to-site-portal.md#p2sconfig)
* [A központi hozzárendelés szerkesztése](virtual-wan-point-to-site-portal.md#edit)

## <a name="configure-the-device-tunnel"></a>Az eszközalagút konfigurálása

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>Profil eltávolítása

A profil eltávolításához futtassa a következő parancsot:

![Felesleges tartalmak törlése](./media/howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>További lépések

A Virtual WAN-ról további információt a GYAKORI KÉRDÉSEK című [témakörben talál.](virtual-wan-faq.md)