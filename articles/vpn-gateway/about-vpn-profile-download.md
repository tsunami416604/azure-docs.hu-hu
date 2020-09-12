---
title: 'Azure VPN Gateway: tudnivalók a P2S VPN-ügyfél profiljairól'
description: Ez segít az ügyfél-profil fájljának működésében
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 326abaa123ede1fbe371c69fcbed47a310b54511
ms.sourcegitcommit: ac5cbef0706d9910a76e4c0841fdac3ef8ed2e82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/03/2020
ms.locfileid: "89424837"
---
# <a name="about-p2s-vpn-client-profiles"></a>Tudnivalók a P2S VPN-ügyfél profiljairól

A letöltött profil fájl a VPN-kapcsolat konfigurálásához szükséges információkat tartalmazza. Ez a cikk segítséget nyújt a VPN-ügyfél profiljához szükséges információk beszerzésében és megismerésében.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* Az **OpenVPN-mappa** tartalmazza azt a *ovpn* -profilt, amelyet módosítani kell a kulcs és a tanúsítvány belefoglalásához. További információ: az [OpenVPN-ügyfelek konfigurálása az Azure VPN Gatewayhoz](vpn-gateway-howto-openvpn-clients.md#windows). Ha az Azure AD-hitelesítés be van jelölve a VPN-átjárón, akkor ez a mappa nem szerepel a zip-fájlban. Ehelyett keresse meg a AzureVPN mappát, és keresse meg a azurevpnconfig.xml.

## <a name="next-steps"></a>Következő lépések

További információ a pont – hely kapcsolatról: [Tudnivalók a pont – hely](point-to-site-about.md)kapcsolatról.
