---
title: 'Azure VPN-átjáró: A P2S VPN-ügyfélprofilokról'
description: Ez segít az ügyfélprofil-fájl megsegítésben
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 03/17/2020
ms.author: cherylmc
ms.openlocfilehash: b78d32dbb7b4e0d9a3b13ff741e6e38c12be0e62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528506"
---
# <a name="about-p2s-vpn-client-profiles"></a>A P2S VPN-ügyfélprofilokról

A letöltött profilfájl a VPN-kapcsolat konfigurálásához szükséges információkat tartalmazza. Ez a cikk segít a VPN-ügyfélprofilhoz szükséges információk beszerzésében és megértésében.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* Az **OpenVPN mappa** tartalmazza az *ovpn* profilt, amelyet módosítani kell, hogy tartalmazza a kulcsot és a tanúsítványt. További információ: [OpenVPN-ügyfelek konfigurálása az Azure VPN-átjáróhoz című témakörben.](vpn-gateway-howto-openvpn-clients.md#windows) Ez a mappa nem lesz jelen a zip-fájlban, ha az Azure AD-hitelesítés van kiválasztva a VPN-átjárón. Ehelyett az azurevpnconfig.xml az AzureVPN mappában lesz.

## <a name="next-steps"></a>További lépések

A pontról webhelyre című témakörben talál további információt a [pontról webhelyre című témakörben.](point-to-site-about.md)
