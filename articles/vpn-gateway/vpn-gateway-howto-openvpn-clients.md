---
title: Az Azure VPN Gateway OpenVPN-ügyfeleinek konfigurálása | Microsoft Docs
description: Az OpenVPN-ügyfelek Azure-VPN Gateway konfigurálásának lépései
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 12/12/2019
ms.author: cherylmc
ms.openlocfilehash: bdddf097265e7af688175688b6f3214413a90fdc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84984092"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>Az Azure VPN Gateway OpenVPN-ügyfeleinek konfigurálása

Ez a cikk segítséget nyújt az **OpenVPN &reg; protokoll** ügyfeleinek konfigurálásához.

## <a name="before-you-begin"></a>Előkészületek

Ellenőrizze, hogy elvégezte-e a VPN-átjáróhoz tartozó OpenVPN konfigurálásához szükséges lépéseket. Részletekért lásd: [az OpenVPN konfigurálása az Azure VPN Gatewayhoz](vpn-gateway-howto-openvpn.md).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>További lépések

Ha azt szeretné, hogy a VPN-ügyfelek hozzáférhessenek egy másik VNet található erőforrásokhoz, kövesse a [VNet – VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) cikk utasításait egy VNet-VNet kapcsolat beállításához. Ügyeljen arra, hogy az átjárók és a kapcsolatok esetében engedélyezze a BGP-t, ellenkező esetben a forgalom nem fog folytatódni.

**Az "OpenVPN" az OpenVPN Inc védjegye.**
