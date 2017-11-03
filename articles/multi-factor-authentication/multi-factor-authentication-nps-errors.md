---
title: "Hibaelhárítási hibakódok az Azure MFA NPS-kiterjesztés |} Microsoft Docs"
description: "A hálózati házirend-kiszolgáló kiterjesztésű problémák elhárításáról Azure multi-factor Authentication leggyakoribb hibaüzenetek adott feloldását kapcsolatos súgó elérése"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: it-pro
ms.openlocfilehash: 173353d67772c2549aa1b8ec9f2a471bd1c65677
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="resolve-error-messages-from-the-nps-extension-for-azure-multi-factor-authentication"></a>Hárítsa el a hálózati házirend-kiszolgáló bővítmény hibaüzeneteket az Azure multi-factor Authentication

Ha hibákba ütközik a hálózati házirend-kiszolgáló kiterjesztésű Azure multi-factor Authentication, ez a cikk segítségével gyorsabban megoldáshoz. 

## <a name="troubleshooting-steps-for-common-errors"></a>Gyakori hibák hibaelhárítási lépéseket

| Hibakód: | Hibaelhárítási lépések |
| ---------- | --------------------- |
| **CONTACT_SUPPORT** | [Forduljon a támogatási szolgálathoz](#contact-microsoft-support), és említse meg a naplók gyűjtésére szolgáló lépéseket listáját. Minél több információt arról, mi történt a hiba, beleértve a bérlő azonosítója, és egyszerű felhasználónév (UPN) előtt teheti meg. |
| **CLIENT_CERT_INSTALL_ERROR** | Előfordulhat, hogy az ügyféltanúsítvány telepítése vagy a tenanthoz társított hogyan kapcsolatos problémát. Kövesse az utasításokat a [az MFA NPS-bővítmény hibaelhárítási](multi-factor-authentication-nps-extension.md#troubleshooting) ügyfél cert problémák vizsgálatára. |
| **ESTS_TOKEN_ERROR** | Kövesse az utasításokat a [az MFA NPS-bővítmény hibaelhárítási](multi-factor-authentication-nps-extension.md#troubleshooting) vizsgálja meg ügyféltanúsítványt és ADAL token problémákat. |
| **HTTPS_COMMUNICATION_ERROR** | A hálózati házirend-kiszolgáló nem tudja az Azure MFA válasz fogadása. Ellenőrizze, hogy a tűzfalak nyitott kétirányúan https://adnotifications.windowsazure.com érkező vagy oda irányuló forgalom |
| **HTTP_CONNECT_ERROR** | A hálózati házirend-kiszolgáló bővítményt futtató kiszolgálón győződjön meg róla, hogy https://adnotifications.windowsazure.com és https://login.microsoftonline.com/ elérhessék. Ha ezeket a helyeket nem tölthető be, hárítsa el a kapcsolatot az adott kiszolgálón. |
| **REGISTRY_CONFIG_ERROR** | Az alkalmazás, amely lehet, hogy a beállításjegyzékben hiányzik egy kulcs az [PowerShell-parancsfájl](multi-factor-authentication-nps-extension.md#install-the-nps-extension) nem futtathatja a telepítés után. A hibaüzenet a hiányzó kulcsot kell tartalmaznia. Győződjön meg arról, hogy a kulcs alatti HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa. |
| **REQUEST_FORMAT_ERROR** <br> RADIUS-kérelmet RADIUS-userName\Identifier kötelező attribútum hiányzik. Győződjön meg arról, hogy a hálózati házirend-kiszolgáló részesül-e a RADIUS-kérelmek | Ez a hiba általában a telepítési hibát tükrözi. A hálózati házirend-kiszolgáló bővítmény telepítenie kell a hálózati házirend-kiszolgálókat, amelyek a RADIUS-kérelmek fogadására. NPS-kiszolgálókon telepített szolgáltatásokat, mint a távoli asztali Átjárókiszolgáló és a hozzá tartozó függőségek nem fogadjon RADIUS-kérelmeket. Hálózati házirend-kiszolgáló kiterjesztés csak akkor működik, amikor a részletek a hitelesítési kérelem nem olvasása óta. ilyen telepítések és a kimenő hibák telepíti. |
| **REQUEST_MISSING_CODE** | Győződjön meg arról, hogy a titkosítási protokollt a hálózati házirend-kiszolgáló és NAS között támogatja-e a másodlagos hitelesítési módszer, amelynek a használata. **PAP** a felhőben Azure MFA hitelesítési módszereket támogatja: telefonhívás, a egyirányú SMS-üzenet, a mobilalkalmazáson keresztüli értesítések és a mobilalkalmazás ellenőrzőkódjának. **CHAPv2** és **EAP** támogatja a telefonhívás, és értesítést a mobilalkalmazásban. |
| **USERNAME_CANONICALIZATION_ERROR** | Ellenőrizze, hogy a felhasználó megtalálható-e a helyszíni Active Directory-példányban, valamint, hogy a hálózati házirend-kiszolgáló szolgáltatás jogosult a könyvtár eléréséhez. Erdők közötti bizalmi kapcsolatok, használatakor [forduljon a támogatási szolgálathoz](#contact-microsoft-support) további segítségért. |


   

### <a name="alternate-login-id-errors"></a>Másodlagos bejelentkezési azonosító hibák

| Hibakód: | Hibaüzenet | Hibaelhárítási lépések |
| ---------- | ------------- | --------------------- |
| **ALTERNATE_LOGIN_ID_ERROR** | Hiba: userObjectSid keresés sikertelen volt | Győződjön meg arról, hogy a felhasználó létezik-e a helyszíni Active Directory-példányban. Erdők közötti bizalmi kapcsolatok, használatakor [forduljon a támogatási szolgálathoz](#contact-microsoft-support) további segítségért. |
| **ALTERNATE_LOGIN_ID_ERROR** | Hiba: Nem sikerült másik LoginId keresési | Ellenőrizze, hogy LDAP_ALTERNATE_LOGINID_ATTRIBUTE egy [érvényes active directory-attribútumot](https://msdn.microsoft.com/library/ms675090(v=vs.85).aspx). <br><br> Ha LDAP_FORCE_GLOBAL_CATALOG igaz értékre van beállítva, vagy LDAP_LOOKUP_FORESTS egy nem üres érték van beállítva, győződjön meg arról, hogy konfigurálta-e a globális katalógus és, hogy a AlternateLoginId attribútum hozzáadni. <br><br> LDAP_LOOKUP_FORESTS egy nem üres érték van beállítva, győződjön meg arról, hogy az érték megfelelő. Ha egynél több erdő neve, a neveket pontosvesszővel, szóközt nem külön kell. <br><br> Ha ezeket a lépéseket nem segít a problémán, [forduljon a támogatási szolgálathoz](#contact-microsoft-support) további segítséget itt találhat. |
| **ALTERNATE_LOGIN_ID_ERROR** | Hiba: Alternatív LoginId értéke üres | Győződjön meg arról, hogy a felhasználó a AlternateLoginId attribútum van konfigurálva. |


## <a name="errors-your-users-may-encounter"></a>A felhasználók szembesülhetnek hibák

| Hibakód: | Hibaüzenet | Hibaelhárítási lépések |
| ---------- | ------------- | --------------------- |
| **Hozzáférés megtagadva** | Hívó bérlő nem rendelkezik hozzáférési engedélyekkel a felhasználó hitelesítést | Ellenőrzése, hogy a bérlő tartománya és a egyszerű felhasználónév (UPN) azonos. Például győződjön meg arról, hogy user@contoso.com a Contoso bérlő hitelesítést próbál. Az egyszerű Felhasználónevet érvényes felhasználó jelenti. a bérlő az Azure-ban. |
| **AuthenticationMethodNotConfigured** | A felhasználó nem konfigurálta a megadott hitelesítési módszert. | A felhasználó hozzáadása, és ellenőrizze a hitelesítési módszerek az utasításainak megfelelően [kezelheti a kétlépéses ellenőrzés beállításait](./end-user/multi-factor-authentication-end-user-manage-settings.md). |
| **AuthenticationMethodNotSupported** | Nem támogatott a megadott hitelesítési módszert. | Gyűjteni a naplókat, amely tartalmazza a hiba, és [forduljon a támogatási szolgálathoz](#contact-microsoft-support). Ha támogatási szolgálatához fordul, adja meg a felhasználónév és a másodlagos hitelesítési módszert a hibát kiváltó. |
| **BecAccessDenied** | MSODS Bec hívása adott vissza a hozzáférés megtagadva, valószínűleg a nincs megadva a bérlő | A felhasználó megtalálható-e a helyszíni Active Directory, de nem van-e szinkronizálva az Azure AD által AD Connect. Vagy a felhasználó nincs megadva a bérlő számára. Adja hozzá a felhasználót az Azure AD, és azok hozzáadása a hitelesítési módszerek az utasításainak megfelelően [kezelheti a kétlépéses ellenőrzés beállításait](./end-user/multi-factor-authentication-end-user-manage-settings.md). |
| **InvalidFormat** vagy **StrongAuthenticationServiceInvalidParameter** | A telefonszám nem felismerhetetlen formátumú | Az ellenőrzési telefonszámok javítsa ki a felhasználó rendelkezik. |
| **InvalidSession** | A megadott munkamenet érvénytelen, vagy esetleg elévült | A munkamenet több mint három percig tartott. Győződjön meg arról, hogy a felhasználó az ellenőrző kód megadása, vagy az alkalmazásban megjelenő értesítésre módot ad a hitelesítési kérelmet három percen belül válaszol. Ha ez nem segít a problémán, ellenőrizze, hogy vannak-e ügyfél NAS kiszolgáló, hálózati házirend-kiszolgáló és az Azure MFA-végpont között nincs hálózati késések fordulnak elő.  |
| **NoDefaultAuthenticationMethodIsConfigured** | A felhasználó a nem alapértelmezett hitelesítési módszer konfigurálása | A felhasználó hozzáadása, és ellenőrizze a hitelesítési módszerek az utasításainak megfelelően [kezelheti a kétlépéses ellenőrzés beállításait](./end-user/multi-factor-authentication-end-user-manage-settings.md). Győződjön meg arról, hogy a felhasználó rendelkezik egy alapértelmezett hitelesítési módszer választása, és ez a módszer a fiókjuk konfigurálva. |
| **OathCodePinIncorrect** | Helytelen kódot és a megadott PIN-kód. | Ez a hiba nem várható a hálózati házirend-kiszolgáló bővítményben. Ha a felhasználó tapasztal, [forduljon a támogatási szolgálathoz](#contact-microsoft-support) hibaelhárítási segítséget. |
| **ProofDataNotFound** | Ellenőrző adatok nem konfigurálta a megadott hitelesítési módszert. | A felhasználó, próbálkozzon egy másik ellenőrzési módszerrel, vagy adjon hozzá egy új ellenőrzési módszert az utasításainak megfelelően [kezelheti a kétlépéses ellenőrzés beállításait](./end-user/multi-factor-authentication-end-user-manage-settings.md). Ha a felhasználó továbbra is ezt a hibaüzenetet látja, hogy, hogy az ellenőrzési módszert megfelelően van-e beállítva megerősítését követően [forduljon a támogatási szolgálathoz](#contact-microsoft-support). |
| **SMSAuthFailedWrongCodePinEntered** | Helytelen kódot és a megadott PIN-kód. (OneWaySMS) | Ez a hiba nem várható a hálózati házirend-kiszolgáló bővítményben. Ha a felhasználó tapasztal, [forduljon a támogatási szolgálathoz](#contact-microsoft-support) hibaelhárítási segítséget. |
| **TenantIsBlocked** | Bérlői le van tiltva. | [Forduljon a támogatási szolgálathoz](#contact-microsoft-support) Directory azonosítójú Azure-portálon az Azure AD tulajdonságai lapon. |
| **UserNotFound** | A megadott felhasználó nem található. | A bérlő most már az Azure AD aktívnak látható. Ellenőrizze, hogy az előfizetés aktív-e, és rendelkezik a szükséges először féltől származó alkalmazásokat. Győződjön meg arról is a tanúsítvány tulajdonosának a bérlő által várt módon, és a cert továbbra is érvényesek és az egyszerű szolgáltatás alatt regisztrált. |

## <a name="messages-your-users-may-encounter-that-arent-errors"></a>A felhasználók szembesülhetnek, amely üzenetek nem hibák

Egyes esetekben a felhasználók is jelenik meg a multi-factor Authentication a hitelesítési kérés meghiúsult, mert. Ezek nem, a termék konfigurációjának hibáit, de a rendszer szándékos figyelmeztetések foglalja össze, miért lett megtagadva a hitelesítési kérelmet.

| Hibakód: | Hibaüzenet | Javasolt lépések | 
| ---------- | ------------- | ----------------- |
| **OathCodeIncorrect** | Helytelen kódot entered\OATH kód helytelen | Nem hiba, a felhasználó sikeres megadott kód helytelen. | A felhasználó által megadott helytelen kódot. Adjon meg egy új kódot kér, vagy jelentkezzen be újra rendelkezik. | 
| **SMSAuthFailedMaxAllowedCodeRetryReached** | Elérte a maximális megengedett kód újrapróbálkozási | A felhasználó az ellenőrző kérdés túl sokszor sikertelen volt. Attól függően, a beállításokat, szükség lehet egy rendszergazda most feloldva.  |
| **SMSAuthFailedWrongCodeEntered** | Hibás a megadott vagy szöveges üzenet OTP helytelen kód | A felhasználó által megadott helytelen kódot. Adjon meg egy új kódot kér, vagy jelentkezzen be újra rendelkezik. |

## <a name="errors-that-require-support"></a>A hibákat, támogatásra van szüksége

Ha egy ezeket a hibákat észlel, azt javasoljuk, hogy Ön [forduljon a támogatási szolgálathoz](#contact-microsoft-support) diagnosztikai segítségét. Nincs szabványos lépésekre lehet oldani ezeket a hibákat. Amikor az ügyfélszolgálatot, ügyeljen arra, hogy mennyi információ lehető hibához lépéseit, és a bérlői kapcsolatos információkat tartalmazza.

| Hibakód: | Hibaüzenet |
| ---------- | ------------- |
| **InvalidParameter** | Kérelem nem lehet null. |
| **InvalidParameter** | ObjectId nem lehet null vagy üres a ReplicationScope: {0} |
| **InvalidParameter** | Cégnév hosszát \{0} \ hosszabb, mint a maximális engedélyezett hosszt {1} |
| **InvalidParameter** | UserPrincipalName nem lehet null vagy üres |
| **InvalidParameter** | A megadott TenantId nem a megfelelő formátumban van. |
| **InvalidParameter** | Munkamenet-azonosító nem lehet null vagy üres |
| **InvalidParameter** | Nem sikerült feloldani a ProofData kérelemből vagy az Msods. A ProofData érvénytelen. |
| **InternalError** |  |
| **OathCodePinIncorrect** |  |
| **VersionNotSupported** |  |
| **MFAPinNotSetup** |  |

## <a name="next-steps"></a>Következő lépések

### <a name="troubleshoot-user-accounts"></a>Felhasználói fiókok hibáinak elhárítása

Ha a felhasználók is [problémák adódtak a kétlépéses ellenőrzéshez használttal](./end-user/multi-factor-authentication-end-user-troubleshoot.md), őket: problémák önálló diagnosztizálása érdekében. 

### <a name="contact-microsoft-support"></a>Forduljon a Microsoft támogatási szolgálatához.

Ha további segítségre van szüksége, forduljon a támogatási szakember keresztül [Azure multi-factor Authentication kiszolgáló támogatási](https://support.microsoft.com/oas/default.aspx?prid=14947). Lépjen kapcsolatba velünk, ha esetén lehet hasznos információt tartalmazhatnak a lehető problémával kapcsolatos. Megadhat olyan információkat tartalmaz az oldal, ahol a hiba, a konkrét hibakód megtekinthető látta az adott munkamenet-azonosító, a hiba látott, és hibakeresési naplókat, a felhasználó Azonosítóját.

Támogatási diagnosztikai hibakeresési naplók összegyűjtésére, tegye a következőket: 

1. Nyisson meg egy rendszergazdai parancssort, és futtassa az alábbi parancsokat:

   ```
   Mkdir c:\NPS
   Cd NPS
   netsh trace start Scenario=NetConnection capture=yes tracefile=c:\NPS\nettrace.etl
   logman create trace "NPSExtension" -ow -o c:\NPS\NPSExtension.etl -p {7237ED00-E119-430B-AB0F-C63360C8EE81} 0xffffffffffffffff 0xff -nb 16 16 -bs 1024 -mode Circular -f bincirc -max 4096 -ets
   logman update trace "NPSExtension" -p {EC2E6D3A-C958-4C76-8EA4-0262520886FF} 0xffffffffffffffff 0xff -ets
   ```

2. A probléma reprodukálásához szükséges

3. Állítsa le a nyomkövetést a következő parancsokkal:

   ```
   logman stop "NPSExtension" -ets
   netsh trace stop
   wevtutil epl AuthNOptCh C:\NPS\%computername%_AuthNOptCh.evtx
   wevtutil epl AuthZOptCh C:\NPS\%computername%_AuthZOptCh.evtx
   wevtutil epl AuthZAdminCh C:\NPS\%computername%_AuthZAdminCh.evtx
   Start .
   ```

4. A ZIP-C:\NPS mappa tartalmát, és a zip-fájl csatolása a támogatási esetet.


