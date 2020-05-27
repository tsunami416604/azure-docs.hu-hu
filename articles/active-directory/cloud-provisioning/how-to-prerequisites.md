---
title: Az Azure AD-ban Azure AD Connect felhőalapú üzembe helyezés előfeltételei
description: Ez a cikk a felhőalapú üzembe helyezéshez szükséges előfeltételeket és hardverkövetelményekat ismerteti.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55f2167552e21973d304f98693be022683fdf661
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "83870931"
---
# <a name="prerequisites-for-azure-ad-connect-cloud-provisioning"></a>A felhő-kiépítés Azure AD Connectának előfeltételei
Ez a cikk útmutatást nyújt a Azure Active Directory (Azure AD) és a felhőalapú kiépítés a személyazonossági megoldáshoz való kiválasztásához és használatához.



## <a name="cloud-provisioning-agent-requirements"></a>A felhőalapú kiépítési ügynökre vonatkozó követelmények
Azure AD Connect felhőalapú kiépítés használatához a következőkre lesz szüksége:
    
- Globális rendszergazdai fiók az Azure AD-bérlőhöz, amely nem vendég felhasználó.
- Helyszíni kiszolgáló a kiépítési ügynökhöz Windows 2012 R2 vagy újabb rendszerrel.
- Helyszíni tűzfal-konfigurációk.

>[!NOTE]
>A kiépítési ügynök jelenleg csak angol nyelvű kiszolgálókon telepíthető. Egy angol nyelvi csomag nem angol nyelvű kiszolgálón való telepítése nem érvényes Áthidaló megoldás, ezért az ügynök nem lesz telepítve. 

A dokumentum további részében részletes útmutatót talál az előfeltételekhez.

### <a name="in-the-azure-active-directory-admin-center"></a>A Azure Active Directory felügyeleti központban

1. Hozzon létre egy csak felhőalapú globális rendszergazdai fiókot az Azure AD-bérlőn. Így kezelheti a bérlő konfigurációját, ha a helyszíni szolgáltatások meghibásodnak vagy elérhetetlenné válnak. Ismerje meg, hogyan [adhat hozzá csak felhőalapú globális rendszergazdai fiókot](../active-directory-users-create-azure-portal.md). Ennek a lépésnek a befejezése kritikus fontosságú annak biztosítása érdekében, hogy ne legyen kizárva a bérlőből.
1. Adjon hozzá egy vagy több [Egyéni tartománynevet](../active-directory-domains-add-azure-portal.md) az Azure ad-bérlőhöz. A felhasználók a következő tartománynevek egyikével jelentkezhetnek be.

### <a name="in-your-directory-in-active-directory"></a>A címtárában Active Directory

A [IdFix eszköz](https://docs.microsoft.com/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix) futtatásával készítse elő a címtár-attribútumok szinkronizálását.

### <a name="in-your-on-premises-environment"></a>Helyszíni környezetben

1. Azonosítson egy Windows Server 2012 R2 vagy újabb rendszert futtató, tartományhoz csatlakoztatott gazdagépet legalább 4 GB RAM-mal és .NET 4.7.1 + futtatókörnyezettel.

1. A helyi kiszolgálón található PowerShell végrehajtási házirendet nem definiált vagy RemoteSigned értékre kell beállítani.

1. Ha tűzfal található a kiszolgálók és az Azure AD között, konfigurálja a következő elemeket:
   - Győződjön meg arról, hogy az ügynökök az alábbi portokon keresztül tehetnek *kimenő* kéréseket az Azure ad-nek:

        | Portszám | Használatuk módja |
        | --- | --- |
        | **80** | Letölti a visszavont tanúsítványok listáját (CRL) a TLS/SSL-tanúsítvány ellenőrzése közben.  |
        | **443** | A szolgáltatással folytatott összes kimenő kommunikációt kezeli. |
        | **8080** (nem kötelező) | Az ügynökök 10 percenként jelentik az állapotukat az 8080-as porton keresztül, ha a 443-es port nem érhető el. Ez az állapot az Azure AD-portálon jelenik meg. |
     
   - Ha a tűzfal a kezdeményező felhasználók alapján kényszeríti a szabályokat, nyissa meg ezeket a portokat a hálózati szolgáltatásként futtató Windows-szolgáltatások forgalmára.
   - Ha a tűzfal vagy proxy lehetővé teszi a biztonságos utótagok megadását, vegyen fel kapcsolatokat a \* . msappproxy.net és a \* . servicebus.Windows.net. Ha nem, engedélyezze a hozzáférést az [Azure Datacenter IP-tartományokhoz](https://www.microsoft.com/download/details.aspx?id=41653), amelyek hetente frissülnek.
   - Az ügynököknek hozzá kell férniük a login.windows.net és a login.microsoftonline.com a kezdeti regisztrációhoz. Nyissa meg a tűzfalat az URL-címekhez is.
   - A tanúsítvány érvényesítéséhez oldja fel a következő URL-címeket: mscrl.microsoft.com:80, crl.microsoft.com:80, ocsp.msocsp.com:80 és www \. Microsoft.com:80. Ezeket az URL-címeket a rendszer más Microsoft-termékekkel való tanúsítvány-ellenőrzésre használja, ezért előfordulhat, hogy az URL-címek feloldása már megtörtént.

>[!NOTE]
> A felhőalapú kiépítési ügynök telepítése a Windows Server Core rendszerre nem támogatott.


### <a name="additional-requirements"></a>További követelmények
- [Microsoft .NET Framework 4.7.1](https://www.microsoft.com/download/details.aspx?id=56116) 

#### <a name="tls-requirements"></a>TLS-követelmények

>[!NOTE]
>A Transport Layer Security (TLS) egy olyan protokoll, amely biztonságos kommunikációt tesz lehetővé. A TLS-beállítások módosítása a teljes erdőre hatással van. További információ: [frissítés a tls 1,1 és a tls 1,2 alapértelmezett biztonságos protokollokként a Windowsban](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi).

A Azure AD Connect felhőalapú kiépítési ügynököt futtató Windows Servernek a telepítése előtt rendelkeznie kell a TLS 1,2-es beállítással.

A TLS 1,2 engedélyezéséhez kövesse az alábbi lépéseket.

1. Állítsa be a következő beállításkulcsokat:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Indítsa újra a kiszolgálót.


## <a name="next-steps"></a>További lépések 

- [Mi az az üzembe helyezés?](what-is-provisioning.md)
- [Mi az az Azure AD Connect felhőalapú jogosultságkiosztás?](what-is-cloud-provisioning.md)

