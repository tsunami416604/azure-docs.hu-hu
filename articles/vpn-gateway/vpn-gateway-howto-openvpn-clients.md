---
title: Az Azure VPN Gateway OpenVPN-ügyfeleinek konfigurálása | Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja az OpenVPN Protocol-ügyfeleket az Azure VPN Gatewayhoz, beleértve a Windows-, Mac-és Linux-ügyfeleket is.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 12/12/2019
ms.author: cherylmc
ms.openlocfilehash: 136dcb0b2d1740aa5cadbd716b4a8386ad5cf486
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2020
ms.locfileid: "87926212"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>Az Azure VPN Gateway OpenVPN-ügyfeleinek konfigurálása

Ez a cikk segítséget nyújt az **OpenVPN &reg; protokoll** ügyfeleinek konfigurálásához.

## <a name="before-you-begin"></a>Előkészületek

Ellenőrizze, hogy elvégezte-e a VPN-átjáróhoz tartozó OpenVPN konfigurálásához szükséges lépéseket. Részletekért lásd: [az OpenVPN konfigurálása az Azure VPN Gatewayhoz](vpn-gateway-howto-openvpn.md).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>További lépések

Ha azt szeretné, hogy a VPN-ügyfelek hozzáférhessenek egy másik VNet található erőforrásokhoz, kövesse a [VNet – VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) cikk utasításait egy VNet-VNet kapcsolat beállításához. Ügyeljen arra, hogy az átjárók és a kapcsolatok esetében engedélyezze a BGP-t, ellenkező esetben a forgalom nem fog folytatódni.

**Az "OpenVPN" az OpenVPN Inc védjegye.**
