---
title: 'Azure VPN Gateway: tudnivalók a P2S VPN-ügyfél profiljairól'
description: Ebből a cikkből megtudhatja, hogy milyen információkra van szüksége a VPN-ügyfél profiljához.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 6a09767a7992a5f902adea6f99e937f3fc6fa7fa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90985933"
---
# <a name="about-p2s-vpn-client-profiles"></a>Tudnivalók a P2S VPN-ügyfél profiljairól

A letöltött profil fájl a VPN-kapcsolat konfigurálásához szükséges információkat tartalmazza. Ez a cikk segítséget nyújt a VPN-ügyfél profiljához szükséges információk beszerzésében és megismerésében.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* Az **OpenVPN-mappa** tartalmazza azt a *ovpn* -profilt, amelyet módosítani kell a kulcs és a tanúsítvány belefoglalásához. További információ: az [OpenVPN-ügyfelek konfigurálása az Azure VPN Gatewayhoz](vpn-gateway-howto-openvpn-clients.md#windows). Ha az Azure AD-hitelesítés be van jelölve a VPN-átjárón, akkor ez a mappa nem szerepel a zip-fájlban. Ehelyett keresse meg a AzureVPN mappát, és keresse meg a azurevpnconfig.xml.

## <a name="next-steps"></a>Következő lépések

További információ a pont – hely kapcsolatról: [Tudnivalók a pont – hely](point-to-site-about.md)kapcsolatról.
