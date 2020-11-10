---
title: OpenVPN-ügyfelek konfigurálása az Azure Virtual WAN-hoz
description: Az Azure Virtual WAN-hoz készült OpenVPN-ügyfelek konfigurálásának lépései
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 3e9674916ea9bb5e756a5e57ff18517f53ca7497
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94427558"
---
# <a name="configure-an-openvpn-client-for-azure-virtual-wan"></a>OpenVPN-ügyfél konfigurálása az Azure Virtual WAN-hoz

Ez a cikk segítséget nyújt az **OpenVPN &reg; protokoll** ügyfeleinek konfigurálásához. A Windows 10-es Azure VPN-ügyfelét is használhatja az OpenVPN protokollon keresztüli csatlakozáshoz 

## <a name="before-you-begin"></a>Előkészületek

Hozzon létre egy felhasználói VPN (pont – hely) konfigurációt. Győződjön meg arról, hogy az "OpenVPN" lehetőséget választja az alagút típusaként. A lépéseket lásd: [P2S-konfiguráció létrehozása az Azure Virtual WAN-hoz](virtual-wan-point-to-site-portal.md#p2sconfig).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Következő lépések

A felhasználói VPN-kapcsolatokról (pont – hely) a [felhasználói VPN-kapcsolatok létrehozása](virtual-wan-point-to-site-portal.md)című témakörben olvashat bővebben.

**Az "OpenVPN" az OpenVPN Inc védjegye.**
