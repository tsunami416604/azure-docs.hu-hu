---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c61378510fbfc8bdc13f35ba1063a0d9316d88e3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80066224"
---
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
* OpenVPN (ha engedélyezte az OpenVPN-és Azure AD-hitelesítési beállításokat az átjárón. VPN Gateway esetén tekintse meg [a bérlő létrehozása](../articles/vpn-gateway/openvpn-azure-ad-tenant.md)című témakört. Virtuális WAN esetén lásd: [bérlői VWAN létrehozása](../articles/virtual-wan/openvpn-azure-ad-tenant.md).

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

* Az **általános mappa** tartalmazza a nyilvános kiszolgáló tanúsítványát és a VpnSettings. xml fájlt. A VpnSettings. xml fájl az általános ügyfél konfigurálásához szükséges információkat tartalmazza.

* Előfordulhat, hogy a letöltött zip-fájl **WindowsAmd64** és **WindowsX86** mappákat is tartalmaz. Ezek a mappák tartalmazzák az SSTP és a IKEv2 Windows-ügyfelekhez készült telepítőjét. A telepítéshez rendszergazdai jogosultságok szükségesek az ügyfélen.