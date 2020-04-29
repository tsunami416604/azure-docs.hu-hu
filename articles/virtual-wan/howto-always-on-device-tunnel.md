---
title: Always-On VPN-alagút konfigurálása
titleSuffix: Azure Virtual WAN
description: Az Always On VPN-eszközök bújtatásának konfigurálása a virtuális WAN-hoz
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: cherylmc
ms.openlocfilehash: e3eea639eaa52c07e877476e9215144e916618d4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79502853"
---
# <a name="configure-an-always-on-vpn-device-tunnel-for-virtual-wan"></a>Always On VPN-eszköz bújtatásának konfigurálása virtuális WAN-hoz

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>Előfeltételek

Létre kell hoznia egy pont – hely konfigurációt, és szerkesztenie kell a virtuális központ hozzárendelését. A következő részekben talál útmutatást:

* [Pont–hely konfiguráció létrehozása](virtual-wan-point-to-site-portal.md#p2sconfig)
* [A hub-hozzárendelés szerkesztése](virtual-wan-point-to-site-portal.md#edit)

## <a name="configure-the-device-tunnel"></a>Az eszköz bújtatásának konfigurálása

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>Profil eltávolítása

A profil eltávolításához futtassa a következő parancsot:

![Felesleges tartalmak törlése](./media/howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>További lépések

A virtuális WAN-ról további információt a [Gyakori kérdések](virtual-wan-faq.md)című témakörben talál.