---
title: Az Azure AD-ban Azure AD Connect felhőalapú üzembe helyezés előfeltételei
description: Ez a témakör ismerteti az előfeltételeket és a hardverkövetelmények Felhőbeli üzembe helyezését.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 155edf72a60e079a609853e953e3cf66024cc83c
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794249"
---
# <a name="prerequisites-for-azure-ad-connect-cloud-provisioning"></a>A felhő-kiépítés Azure AD Connectának előfeltételei
Ez a témakör útmutatást nyújt Azure AD Connect felhőalapú kiépítési megoldás kiválasztásához és használatához.



## <a name="cloud-provisioning-agent-requirements"></a>A felhőalapú kiépítési ügynökre vonatkozó követelmények
A következőkre lesz szüksége a Azure AD Connect Cloud kiépítés használatához:
    
- globális rendszergazdai fiók az Azure AD-bérlőhöz
- helyszíni kiszolgáló a kiépítési ügynökhöz Windows 2012 R2 vagy újabb rendszerrel
- helyszíni tűzfal-konfigurációk

A dokumentum további részében részletes útmutatót talál az előfeltételekhez.

### <a name="in-the-azure-active-directory-admin-center"></a>A Azure Active Directory felügyeleti központban

1. Hozzon létre egy csak felhőalapú globális rendszergazdai fiókot az Azure AD-bérlőn. Így kezelheti a bérlő konfigurációját, ha a helyszíni szolgáltatások meghibásodnak vagy elérhetetlenné válnak. További információ [a csak felhőalapú globális rendszergazdai fiók hozzáadásáról](../active-directory-users-create-azure-portal.md). Ennek a lépésnek a befejezése kritikus fontosságú annak biztosítása érdekében, hogy ne legyen kizárva a bérlőből.
2. Adjon hozzá egy vagy több [Egyéni tartománynevet](../active-directory-domains-add-azure-portal.md) az Azure ad-bérlőhöz. A felhasználók a következő tartománynevek egyikével jelentkezhetnek be.

### <a name="in-your-on-premises-environment"></a>Helyszíni környezetben

1. Windows Server 2012 R2 vagy újabb rendszert futtató, tartományhoz csatlakoztatott, legalább 4 GB RAM-mal és .NET 4.7.1 + futtatókörnyezettel rendelkező gazdagép azonosítása 

2. Ha tűzfal található a kiszolgálók és az Azure AD között, konfigurálja a következő elemeket:
   - Győződjön meg arról, hogy az ügynökök az alábbi portokon keresztül tehetnek *kimenő* kéréseket az Azure ad-nek:

     | Portszám | Használatuk módja |
     | --- | --- |
     | **80** | Letölti a visszavont tanúsítványok listáját (CRL) az SSL-tanúsítvány ellenőrzése közben. |
     | **443** | Kezeli az összes kimenő kommunikációt a szolgáltatással. |
     | **8080** (nem kötelező) | Az ügynökök tíz percenként jelentést készítenek az állapotukról az 8080-as porton keresztül, ha a 443-es port nem érhető el. Ez az állapot az Azure AD-portálon jelenik meg. A 8080-es port _nem_ használatos a felhasználói bejelentkezésekhez. |
     
     Ha a tűzfal a kezdeményező felhasználók alapján kényszeríti a szabályokat, nyissa meg ezeket a portokat a hálózati szolgáltatásként futtató Windows-szolgáltatások forgalmára.
   - Ha a tűzfal vagy a proxy lehetővé teszi a biztonságos utótagok megadását, akkor vegyen fel kapcsolatokat **\*. msappproxy.net** és **\*. servicebus.Windows.net**. Ha nem, engedélyezze a hozzáférést az [Azure Datacenter IP-tartományokhoz](https://www.microsoft.com/download/details.aspx?id=41653), amelyek hetente frissülnek.
   - Az ügynököknek hozzá kell férniük a **login.Windows.net** és a **login.microsoftonline.com** a kezdeti regisztrációhoz. Nyissa meg a tűzfalat az URL-címekhez is.
   - A tanúsítvány érvényesítéséhez oldja fel a következő URL-címeket: **mscrl.microsoft.com:80**, **CRL.microsoft.com:80**, **OCSP.msocsp.com:80**és **www\.Microsoft.com:80**. Mivel ezek az URL-címek más Microsoft-termékekkel való tanúsítvány-érvényesítéshez használatosak, előfordulhat, hogy az URL-címeket feloldják.

### <a name="verify-the-port"></a>A port ellenőrzése
Annak ellenőrzéséhez, hogy az Azure figyel-e a 443-es porton, és hogy az ügynök képes-e kommunikálni vele, a következőket használhatja:

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Ez a teszt azt ellenőrzi, hogy az ügynökök képesek-e kommunikálni az Azure-ban a 443-as porton keresztül.  Nyisson meg egy böngészőt, és navigáljon a fenti URL-címre abban a kiszolgálón, ahol az ügynököt telepítették.
![Szolgáltatások](media/how-to-install/verify2.png)

### <a name="additional-requirements"></a>További követelmények
- [Microsoft .NET Framework 4.7.1](https://www.microsoft.com/download/details.aspx?id=56116) 

#### <a name="tls-requirements"></a>TLS-követelmények

>[!NOTE]
>A Transport Layer Security (TLS) egy olyan protokoll, amely biztonságos kommunikációt tesz lehetővé.  A TLS-beállítások módosítása a teljes erdőre hatással van.  További információ: a [tls 1,1 és a tls 1,2 engedélyezése a WinHTTP alapértelmezett biztonságos protokolljaiként a Windowsban](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi)

A Azure AD Connect felhőalapú kiépítési ügynököt futtató Windows Servernek a telepítése előtt engedélyeznie kell a TLS 1,2-et.

A TLS 1,2 engedélyezése:

1. Állítsa be a következő beállításkulcsokat:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Indítsa újra a kiszolgálót.


## <a name="next-steps"></a>Következő lépések 

- [Mi a kiépítés?](what-is-provisioning.md)
- [Mi az Azure AD Connect Cloud kiépítés?](what-is-cloud-provisioning.md)

