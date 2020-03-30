---
title: OpenVPN-ügyfelek konfigurálása az Azure VPN Gateway szolgáltatáshoz| Microsoft dokumentumok
description: Az OpenVPN-ügyfelek Azure VPN-átjáróhoz való konfigurálásának lépései
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: cherylmc
ms.openlocfilehash: 09ff3ccebad0baa4148e68995254c818a29d7bd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066092"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>OpenVPN-ügyfelek konfigurálása az Azure VPN Gateway szolgáltatáshoz

Ez a cikk segít az **OpenVPN &reg; protokoll** ügyfelek konfigurálása.

## <a name="before-you-begin"></a>Előkészületek

Ellenőrizze, hogy elvégezte-e az OpenVPN-nek a VPN-átjáróhoz való konfigurálásához szükséges lépéseket. További információt az [OpenVPN konfigurálása az Azure VPN-átjáróhoz című témakörben talál.](vpn-gateway-howto-openvpn.md)

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>További lépések

Ha azt szeretné, hogy a VPN-ügyfelek egy másik virtuális hálózat erőforrásaihoz férjenek hozzá, majd kövesse a [virtuális hálózat és](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) a virtuális hálózat között cikk utasításait a virtuális hálózat és a vnet között lévő kapcsolat beállításához. Ügyeljen arra, hogy engedélyezze a BGP-t az átjárókon és a kapcsolatokon, különben a forgalom nem fog folyni.

**Az "OpenVPN" az OpenVPN Inc. védjegye.**
