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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066224"
---
## <a name="1-download-the-file"></a>1. Töltse le a fájlt

Futtassa az alábbi parancsokat. Másolja az eredmény URL-jét a böngészőbe, hogy letölthesse a profil zip-fájlt.

```azurepowershell-interactive
$profile = New-AzVpnClientConfiguration -ResourceGroupName AADAuth -Name AADauthGW -AuthenticationMethod "EapTls"
   
$PROFILE.VpnProfileSASUrl
```

## <a name="2-extract-the-zip-file"></a>2. Bontsa ki a zip fájlt

Csomagolja ki a tömörített fájlt. A fájl a következő mappákat tartalmazza:

* AzureVPN
* Általános
* OpenVPN (Ha engedélyezte az OpenVPN és az Azure AD hitelesítési beállításait az átjárón. Vpn-átjáró ról: [Bérlő létrehozása.](../articles/vpn-gateway/openvpn-azure-ad-tenant.md) A Virtual [WAN,lásd: Bérlő létrehozása - VWAN](../articles/virtual-wan/openvpn-azure-ad-tenant.md).

## <a name="3-retrieve-information"></a>3. Információ lekérése

Az **AzureVPN** mappában keresse meg az ***azurevpnconfig.xml*** fájlt, és nyissa meg a Jegyzettömbbel. Jegyezze fel a következő címkék közötti szöveget.

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>Profil részletei

Kapcsolat hozzáadásakor használja az előző lépésben gyűjtött adatokat a profil részleteit tartalmazó lapon. A mezők a következő információknak felelnek meg:

   * **Közönség:** Azonosítja azt a címzett erőforrást, amelynek a jogkivonatot szánják.
   * **Kibocsátó:** Azonosítja a biztonsági jogkivonat-szolgáltatást (STS), amely kibocsátotta a jogkivonatot, valamint az Azure AD-bérlő
   * **Bérlő:** A jogkivonatot kibocsátó címtár-bérlő nem módosítható, egyedi azonosítóját tartalmazza
   * **Teljes tartomány:** A teljesen minősített tartománynév (FQDN) az Azure VPN-átjárón
   * **Kiszolgálótitkos:** A VPN-átjáró előmegosztott kulcsa

## <a name="folder-contents"></a>A mappa tartalma

* Az **általános mappa** tartalmazza a nyilvános kiszolgáló tanúsítványát és a VpnSettings.xml fájlt. A VpnSettings.xml fájl az általános ügyfél konfigurálásához szükséges információkat tartalmazza.

* A letöltött zip fájl **WindowsAmd64** és **WindowsX86** mappákat is tartalmazhat. Ezek a mappák tartalmazzák az SSTP és az IKEv2 windowsos ügyfelekhez való telepítőt. A telepítéshez rendszergazdai jogokra van szükség az ügyfélen.