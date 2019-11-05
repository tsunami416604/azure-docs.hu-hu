---
title: 'Tudnivalók a pont – hely VPN-ügyfél profiljairól: Azure VPN Gateway | Microsoft Docs'
description: Ez segít az ügyfél-profil fájljának működésében
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 4e3276e6cde254daf2299f33d8b5ed9f54985327
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517656"
---
# <a name="about-p2s-vpn-client-profiles"></a>Tudnivalók a P2S VPN-ügyfél profiljairól

A letöltött profil fájl a VPN-kapcsolat konfigurálásához szükséges információkat tartalmazza. Ez a cikk segítséget nyújt a VPN-ügyfél profiljához szükséges információk beszerzésében és megismerésében.

## <a name="1-download-the-file"></a>1. Töltse le a fájlt

Futtassa az alábbi parancsokat. Másolja le az eredmény-URL-címet a böngészőjébe a profil zip-fájljának letöltéséhez.

```azurepowershell-interactive
$profile = New-AzVpnClientConfiguration -ResourceGroupName AADAuth -Name AADauthGW -AuthenticationMethod "EapTls"
   
$PROFILE.VpnProfileSASUrl
```

## <a name="2-extract-the-zip-file"></a>2. Csomagolja ki a zip-fájlt

Csomagolja ki a tömörített fájlt. A fájl a következő mappákat tartalmazza:

* AzureVPN
* Általános
* OpenVPN

## <a name="3-retrieve-information"></a>3. adatok lekérése

A **AzureVPN** mappában navigáljon a ***azurevpnconfig. XML*** fájlhoz, és nyissa meg a Jegyzettömbben. Jegyezze fel a következő címkék közötti szöveget.

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>Profil részletei

Kapcsolat hozzáadásakor használja az előző lépésben összegyűjtött adatokat a profil részletei lapon. A mezők a következő adatoknak felelnek meg:

   * **Célközönség:** Azonosítja azt a címzett-erőforrást, amelyet a jogkivonat szánt
   * **Kiállító:** A jogkivonatot és az Azure AD-bérlőt kibocsátó biztonságijogkivonat-szolgáltatás (STS) azonosítása
   * **Bérlő:** A jogkivonatot kiállító címtár-bérlő megváltoztathatatlan, egyedi azonosítóját tartalmazza
   * **Teljes tartománynév:** Az Azure VPN Gateway teljes tartományneve (FQDN)
   * **ServerSecret:** A VPN-átjáró előmegosztott kulcsa

## <a name="folder-contents"></a>Mappa tartalma

* Az **OpenVPN-mappa** tartalmazza azt a *ovpn* -profilt, amelyet módosítani kell a kulcs és a tanúsítvány belefoglalásához. További információ: az [OpenVPN-ügyfelek konfigurálása az Azure VPN Gatewayhoz](vpn-gateway-howto-openvpn-clients.md#windows).

* Az **általános mappa** tartalmazza a nyilvános kiszolgáló tanúsítványát és a VpnSettings. xml fájlt. A VpnSettings. xml fájl az általános ügyfél konfigurálásához szükséges információkat tartalmazza.

* Előfordulhat, hogy a letöltött zip-fájl **WindowsAmd64** és **WindowsX86** mappákat is tartalmaz. Ezek a mappák tartalmazzák az SSTP és a IKEv2 Windows-ügyfelekhez készült telepítőjét. A telepítéshez rendszergazdai jogosultságok szükségesek az ügyfélen.

## <a name="next-steps"></a>További lépések

További információ a pont – hely kapcsolatról: [Tudnivalók a pont – hely](point-to-site-about.md)kapcsolatról.