---
title: Az Azure AD Connect felhőalapú kiépítésének előfeltételei az Azure AD-ben
description: Ez a cikk ismerteti a felhőkiépítés hez szükséges előfeltételeket és hardverkövetelményeket.
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
ms.openlocfilehash: 45648170f69d513b15e79cdd76f56e66bbc88bfa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332086"
---
# <a name="prerequisites-for-azure-ad-connect-cloud-provisioning"></a>Az Azure AD Connect felhőalapú kiépítésének előfeltételei
Ez a cikk útmutatást nyújt az Azure Active Directory (Azure AD) Connect felhőalapú kiépítés identitáskezelési kiválasztásához és használatához.



## <a name="cloud-provisioning-agent-requirements"></a>Felhőkiépítési ügynök követelményei
Az Azure AD Connect felhőalapú kiépítésének használatához az alábbiakra van szükség:
    
- Az Azure AD-bérlő globális rendszergazdai fiókja, amely nem vendégfelhasználó.
- A windows 2012 R2 vagy újabb rendszerrel rendelkező létesítő ügynök helyszíni kiszolgálója.
- Helyszíni tűzfal-konfigurációk.

>[!NOTE]
>A kiépítési ügynök jelenleg csak angol nyelvű kiszolgálókra telepíthető. Az angol nyelvű csomag nem angol nyelvű kiszolgálóra történő telepítése nem érvényes megoldás, ezért az ügynök telepítése nem sikerül. 

A dokumentum többi része lépésről lépésre bemutatja ezeket az előfeltételeket.

### <a name="in-the-azure-active-directory-admin-center"></a>Az Azure Active Directory felügyeleti központban

1. Hozzon létre egy csak felhőalapú globális rendszergazdai fiókot az Azure AD-bérlőn. Ily módon kezelheti a bérlő konfigurációját, ha a helyszíni szolgáltatások sikertelenek vagy elérhetetlenné válnak. További információ a [csak felhőalapú globális rendszergazdai fiók hozzáadásáról.](../active-directory-users-create-azure-portal.md) Ennek a lépésnek a befejezése elengedhetetlen annak érdekében, hogy ne zárja ki a bérlő.
1. Adjon hozzá egy vagy több [egyéni tartománynevet](../active-directory-domains-add-azure-portal.md) az Azure AD-bérlőhöz. A felhasználók a tartománynevek egyikével jelentkezhetnek be.

### <a name="in-your-directory-in-active-directory"></a>Az Active Directory címtárában

Futtassa az [IdFix eszközt](https://docs.microsoft.com/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix) a címtárattribútumok szinkronizálásra való előkészítéséhez.

### <a name="in-your-on-premises-environment"></a>A helyszíni környezetben

1. Azonosítsa a tartományhoz csatlakozó gazdakiszolgálót, amelyen windows Server 2012 R2 vagy nagyobb, legalább 4 GB RAM és .NET 4.7.1+ futtatókörnyezet van.

1. Ha tűzfal van a kiszolgálók és az Azure AD között, konfigurálja a következő elemeket:
   - Győződjön meg arról, hogy az ügynökök *kimenő* kérelmeket tudnak végrehajtani az Azure AD-nek a következő portokon keresztül:

        | Portszám | Hogyan használják |
        | --- | --- |
        | **80** | Letölti a visszavont tanúsítványok listáját a TLS/SSL tanúsítvány érvényesítése közben.  |
        | **443** | Kezeli a szolgáltatással folytatott összes kimenő kommunikációt. |
        | **8080** (nem kötelező) | Az ügynökök 10 percenként jelentik az állapotukat a 8080-as porton keresztül, ha a 443-as port nem érhető el. Ez az állapot jelenik meg az Azure AD portálon. |
     
   - Ha a tűzfal a rendszertől függően kényszeríti a szabályokat, nyissa meg ezeket a portokat a hálózati szolgáltatásként futó Windows-szolgáltatásokból érkező forgalom számára.
   - Ha a tűzfal vagy a proxy lehetővé teszi a \*biztonságos \*utótagok megadását, adjon kapcsolatokat a .msappproxy.net és a .servicebus.windows.net. Ha nem, engedélyezze a hozzáférést az [Azure-adatközpont IP-tartományaihoz,](https://www.microsoft.com/download/details.aspx?id=41653)amelyek hetente frissülnek.
   - Az ügynökeinek hozzáférésre van szükségük login.windows.net és login.microsoftonline.com az első regisztrációhoz. Nyissa meg a tűzfalat az URL-ek számára is.
   - A tanúsítványok érvényesítéséhez a következő URL-címek tiltását kell\.feloldani: mscrl.microsoft.com:80, crl.microsoft.com:80, ocsp.msocsp.com:80 és www microsoft.com:80. Ezeket az URL-címeket más Microsoft-termékek tanúsítvány-ellenőrzésre használják, így előfordulhat, hogy ezeket az URL-címeket már feloldotta.

### <a name="verify-the-port"></a>A port ellenőrzése
Annak ellenőrzéséhez, hogy az Azure figyel-e a 443-as porton, és hogy az ügynök kommunikálhat-e vele, használja a következő URL-címet:

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Ez a teszt ellenőrzi, hogy az ügynökök kommunikálhatnak-e az Azure-ral a 443-as porton keresztül. Nyisson meg egy böngészőt, és lépjen az előző URL-címre abból a kiszolgálóból, amelyen az ügynök telepítve van.

![A kikötői elérhetőség ellenőrzése](media/how-to-install/verify2.png)

### <a name="additional-requirements"></a>További követelmények
- [Microsoft .](https://www.microsoft.com/download/details.aspx?id=56116) 

#### <a name="tls-requirements"></a>TLS-követelmények

>[!NOTE]
>A Transport Layer Security (TLS) egy olyan protokoll, amely biztonságos kommunikációt biztosít. A TLS-beállítások módosítása az egész erdőre hatással van. További információt a [Frissítés a TLS 1.1 és a TLS 1.2 alapértelmezett biztonságos protokollként a Windows WinHTTP rendszerben című témakörében talál.](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi)

Az Azure AD Connect felhőkiépítési ügynökét üzemeltető Windows-kiszolgálótelepítése előtt engedélyeznie kell a TLS 1.2-t.

A TLS 1.2 engedélyezéséhez kövesse az alábbi lépéseket.

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

