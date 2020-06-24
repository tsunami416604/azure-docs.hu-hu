---
title: Always-On VPN-alagút konfigurálása
titleSuffix: Azure Virtual WAN
description: Az Always On VPN-eszközök bújtatásának konfigurálása a virtuális WAN-hoz
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/13/2020
ms.author: cherylmc
ms.openlocfilehash: e911bf6e3736c931ca5c1563ab42f52ecb5cf3c1
ms.sourcegitcommit: 4ac596f284a239a9b3d8ed42f89ed546290f4128
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/12/2020
ms.locfileid: "84750587"
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