---
title: Hibák elhárítása hibakód alapján az Azure MFA NPS-bővítmény – az Azure Active Directory
description: Segítség kérése az Azure multi-factor Authentication NPS-bővítményéhez problémák megoldása
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: f80ecf02a7e517300c41e84986659a66cfa11c90
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58313430"
---
# <a name="resolve-error-messages-from-the-nps-extension-for-azure-multi-factor-authentication"></a>Az Azure multi-factor Authentication az NPS-bővítményének hibaüzenetek feloldása

Ha az Azure multi-factor Authentication NPS-bővítményéhez hibákat észlel, ez a cikk segítségével gyorsabban megoldáshoz. Az NPS-bővítmény naplók az eseménynaplóban alatt találhatók **egyéni nézetek** > **kiszolgálói szerepkörök** > **hálózati házirend- és elérési szolgáltatások** a a kiszolgáló, amelyen telepítve van-e az NPS bővítményével.

## <a name="troubleshooting-steps-for-common-errors"></a>Gyakori hibák hibaelhárítási lépéseket

| Hibakód | Hibaelhárítási lépések |
| ---------- | --------------------- |
| **CONTACT_SUPPORT** | [Forduljon az ügyfélszolgálathoz](#contact-microsoft-support), és említik a naplók gyűjtésére szolgáló lépések listája. Adja meg a lehető legtöbb információt arról, mi történt a hiba, beleértve a bérlő azonosítóját, és az egyszerű felhasználónév (UPN) előtt. |
| **CLIENT_CERT_INSTALL_ERROR** | Előfordulhat, hogy az ügyféltanúsítvány telepítése vagy a tenanthoz társított hogyan problémáját. Kövesse a [hibáinak elhárítása az MFA NPS-bővítményének](howto-mfa-nps-extension.md#troubleshooting) client cert problémák vizsgálatához. |
| **ESTS_TOKEN_ERROR** | Kövesse a [hibáinak elhárítása az MFA NPS-bővítményének](howto-mfa-nps-extension.md#troubleshooting) ügyféltanúsítvány, és az adal-t vizsgálhatja a jogkivonat-problémák. |
| **HTTPS_COMMUNICATION_ERROR** | A hálózati házirend-kiszolgáló nem tudja az Azure MFA válaszokat kaphatnak. Ellenőrizze, hogy a tűzfal nyitva kétirányúan a forgalmat a https://adnotifications.windowsazure.com |
| **HTTP_CONNECT_ERROR** | Az NPS-bővítményt futtató kiszolgáló, ellenőrizze, hogy elérheti https://adnotifications.windowsazure.com és https://login.microsoftonline.com/. Ha ezeket a helyeket nem töltődik be, az adott kiszolgálón-kapcsolatának hibaelhárítása. |
| **Az Azure MFA NPS-bővítményének:** <br> Az Azure MFA NPS-bővítményének másodlagos hitelesítés csak a Radius-kéréseket AccessAccept állapotban hajtja végre. A kérelem érkezett a következő válasz állapota AccessReject, figyelmen kívül hagyja a kérelem felhasználó felhasználóneve. | Ez a hiba általában tükrözi hitelesítési hiba történt az ad-ben, vagy a hálózati házirend-kiszolgáló nem tud válaszokat kaphatnak az Azure ad-ből. Ellenőrizze, hogy a tűzfal nyitva kétirányúan a forgalmat a https://adnotifications.windowsazure.com és https://login.microsoftonline.com 80-as és 443-as portot használja. Fontos továbbá ellenőrizze, hogy a hálózati hozzáférési engedélyek DIAL-IN lapján, a beállítás értéke "szabályozhatja a hálózati házirend-kiszolgáló elérés". |
| **REGISTRY_CONFIG_ERROR** | A kulcs nincs megadva a beállításjegyzékben, az alkalmazás, amely lehet az oka az [PowerShell-parancsprogram](howto-mfa-nps-extension.md#install-the-nps-extension) nem volt a telepítés után futtassa. A hibaüzenet tartalmaznia kell a hiányzó kulcs. Győződjön meg arról, hogy a kulcs alatt HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa. |
| **REQUEST_FORMAT_ERROR** <br> RADIUS-kérést Radius userName\Identifier kötelező attribútum hiányzik. Győződjön meg arról, hogy a hálózati házirend-kiszolgáló RADIUS-kéréseket fogad | Ez a hiba általában a telepítési probléma tükrözi. Az NPS-bővítményt telepíteni kell a RADIUS-kérelmeket fogadhat hálózati házirend-kiszolgálókat. Telepíti a függőségeket RDG és RRAS például hálózati házirend-kiszolgálókat nem kapja meg radius-kéréseket. NPS-bővítményének nem működik, ha ilyen telepítések és a hibák meg telepíteni, mert a nem tud olvasni a hitelesítési kérelem részleteit. |
| **REQUEST_MISSING_CODE** | Győződjön meg arról, hogy a titkosítási protokollt a hálózati házirend- és NAS-kiszolgálók közötti támogatja-e a másodlagos hitelesítési módszert használ. **A PAP** a felhőben, az Azure MFA hitelesítési módszereket támogatja: telefonhívás, egyirányú SMS, mobilalkalmazásbeli értesítés és mobilalkalmazásbeli ellenőrző kód. **CHAPv2** és **EAP** telefonhívás- és mobilalkalmazás-értesítés támogatja. |
| **USERNAME_CANONICALIZATION_ERROR** | Ellenőrizze, hogy a felhasználó szerepel a helyszíni Active Directory-példánynak, valamint, hogy a hálózati házirend-kiszolgáló szolgáltatást a jogosult a könyvtár eléréséhez. Erdők közötti bizalmi kapcsolatok, használatakor [forduljon az ügyfélszolgálathoz](#contact-microsoft-support) további segítségért. |

### <a name="alternate-login-id-errors"></a>Alternatív bejelentkezési azonosító hibák

| Hibakód | Hibaüzenet | Hibaelhárítási lépések |
| ---------- | ------------- | --------------------- |
| **ALTERNATE_LOGIN_ID_ERROR** | Chyba: userObjectSid-címkeresés sikertelen volt | Győződjön meg arról, hogy a felhasználó létezik-e a helyszíni Active Directory-példányában. Erdők közötti bizalmi kapcsolatok, használatakor [forduljon az ügyfélszolgálathoz](#contact-microsoft-support) további segítségért. |
| **ALTERNATE_LOGIN_ID_ERROR** | Hiba: Nem sikerült másik LoginId keresése | Győződjön meg arról, hogy LDAP_ALTERNATE_LOGINID_ATTRIBUTE értékre van állítva egy [érvényes active directory-attribútumot](https://msdn.microsoft.com/library/ms675090(v=vs.85).aspx). <br><br> Ha LDAP_FORCE_GLOBAL_CATALOG True értékre van állítva, vagy LDAP_LOOKUP_FORESTS egy nem üres érték van beállítva, győződjön meg arról, hogy konfigurálta a globális katalógus és, hogy a AlternateLoginId attribútumot ad hozzá. <br><br> LDAP_LOOKUP_FORESTS egy nem üres érték van beállítva, győződjön meg arról, hogy helyesen szerepel-e az értéket. Ha egynél több erdő neve, a neveket pontosvesszővel elválasztva, szóközt nem kell elválasztva. <br><br> Ha ezek a lépések nem oldják meg a problémát, [forduljon az ügyfélszolgálathoz](#contact-microsoft-support) további segítséget itt találhat. |
| **ALTERNATE_LOGIN_ID_ERROR** | Hiba: Alternatív LoginId érték üres | Győződjön meg arról, hogy a felhasználó a AlternateLoginId attribútum van konfigurálva. |

## <a name="errors-your-users-may-encounter"></a>A felhasználók előfordulhatnak hibák

| Hibakód | Hibaüzenet | Hibaelhárítási lépések |
| ---------- | ------------- | --------------------- |
| **AccessDenied** | Hívó bérlő nem rendelkezik engedéllyel ehhez a felhasználóhoz hitelesítési | Ellenőrizze, hogy a bérlői és az egyszerű felhasználónév (UPN), a tartományban-e ugyanaz. Például győződjön meg arról, hogy user@contoso.com próbál a Contoso bérlő hitelesítéséhez. Az egyszerű felhasználónév a bérlő az Azure-ban érvényes felhasználót jelöl. |
| **AuthenticationMethodNotConfigured** | A megadott hitelesítési módszer esetén a felhasználónak nem volt beállítva | A felhasználó hozzáadásához, vagy ellenőrizze a hitelesítési módszereket található utasítások szerint [felügyelheti a kétlépéses ellenőrzés](../user-help/multi-factor-authentication-end-user-manage-settings.md). |
| **AuthenticationMethodNotSupported** | Nem támogatott a megadott hitelesítési módszert. | A hibát tartalmazó naplók gyűjtése és [forduljon az ügyfélszolgálathoz](#contact-microsoft-support). Amikor a, forduljon az ügyfélszolgálathoz, adja meg a felhasználónevet és a másodlagos ellenőrzési módszert a hibát kiváltó. |
| **BecAccessDenied** | MSODS Bec hívása adott vissza a hozzáférés megtagadva, valószínűleg a felhasználónév nincs definiálva a bérlőhöz | A felhasználó szerepel a helyszíni Active Directory, de nincs szinkronizálva az Azure AD által AD Connect. Vagy a felhasználó nem található a bérlő számára. Adja hozzá a felhasználót az Azure ad-hez, és azokat a hitelesítési módszereket található utasítások szerint hozzáadása [felügyelheti a kétlépéses ellenőrzés](../user-help/multi-factor-authentication-end-user-manage-settings.md). |
| **InvalidFormat** vagy **StrongAuthenticationServiceInvalidParameter** | A telefonszám formátuma ismeretlen van | Az ellenőrzési telefonszámok javítsa ki a felhasználó rendelkezik. |
| **InvalidSession** | A megadott munkamenet érvénytelen, vagy esetleg elévült | A munkamenet több mint három percig tartott. Győződjön meg arról, hogy a felhasználó az ellenőrző kód megadása, vagy az alkalmazásban megjelenő értesítésre, a hitelesítési kérelem indítása három percen belül válaszol. Ha ez nem oldja a problémát, ellenőrizze, hogy nincsenek-e ügyfél, NAS-kiszolgáló, hálózati házirend-kiszolgáló és az Azure MFA-végpont között nincs hálózati késéseket.  |
| **NoDefaultAuthenticationMethodIsConfigured** | Nem alapértelmezett hitelesítési módszere lett konfigurálva, a felhasználó számára | A felhasználó hozzáadásához, vagy ellenőrizze a hitelesítési módszereket található utasítások szerint [felügyelheti a kétlépéses ellenőrzés](../user-help/multi-factor-authentication-end-user-manage-settings.md). Győződjön meg arról, hogy a felhasználó egy alapértelmezett hitelesítési módszere a választott, pedig ez a módszer a fiókjuk konfigurálva. |
| **OathCodePinIncorrect** | Hibás kódot és a megadott PIN-kód. | Ez a hiba nem vár az NPS-bővítményt. Ha a felhasználó észlel, [forduljon az ügyfélszolgálathoz](#contact-microsoft-support) hibaelhárítási segítséget. |
| **ProofDataNotFound** | Megvalósíthatósági példában adatok nem a megadott hitelesítési módszer konfigurálása. | A felhasználónak, próbálkozzon másik ellenőrzési módszert, vagy adjon hozzá egy új ellenőrzési módszert az utasítások szerint [felügyelheti a kétlépéses ellenőrzés](../user-help/multi-factor-authentication-end-user-manage-settings.md). Ha a felhasználó továbbra is ez a hibaüzenet jelenik meg, hogy az ellenőrzési módszert megfelelően van-e beállítani ellenőrzése után [forduljon az ügyfélszolgálathoz](#contact-microsoft-support). |
| **SMSAuthFailedWrongCodePinEntered** | Hibás kódot és a megadott PIN-kód. (OneWaySMS) | Ez a hiba nem vár az NPS-bővítményt. Ha a felhasználó észlel, [forduljon az ügyfélszolgálathoz](#contact-microsoft-support) hibaelhárítási segítséget. |
| **TenantIsBlocked** | Bérlő le van tiltva. | [Forduljon az ügyfélszolgálathoz](#contact-microsoft-support) a címtár-azonosító az Azure Portalon az Azure ad-ben tulajdonságai lapon. |
| **UserNotFound** | A megadott felhasználó nem található. | A bérlő már nem aktív, az Azure ad-ben látható. Ellenőrizze, hogy aktív-e az előfizetés, és hogy rendelkezik a szükséges belső alkalmazások. Ügyeljen arra, hogy a bérlő, a tanúsítvány tárgyában az elvárt módon, és a tanúsítvány továbbra is érvényes, és az egyszerű szolgáltatás alatt regisztrált-e. |

## <a name="messages-your-users-may-encounter-that-arent-errors"></a>A felhasználók számára, amelyek előfordulhatnak üzenetek nincsenek hibák

Egyes esetekben a felhasználók lehet, hogy üzenetek beolvasása a multi-factor Authentication használatát, mert a hitelesítési kérelem sikertelen volt. Ezek a hibák a konfiguráció a termék nem, de a szándékos figyelmeztetések vannak elmagyarázza, hogy miért a hitelesítési kérelem megtagadva.

| Hibakód | Hibaüzenet | Javasolt lépések | 
| ---------- | ------------- | ----------------- |
| **OathCodeIncorrect** | Hibás kódot entered\OATH kód helytelen | Az a felhasználó hibás kódot adott meg. Rendelkezik, azokat újra egy új kódot kér, vagy jelentkezzen be újra. | 
| **SMSAuthFailedMaxAllowedCodeRetryReached** | Elérte a maximális megengedett kódot újrapróbálkozási | A felhasználó az ellenőrző kérdés túl sokszor sikertelen volt. A beállítások attól függően, előfordulhat, hogy szükségük lehet a rendszergazdája mostantól feloldva.  |
| **SMSAuthFailedWrongCodeEntered** | Hibás a megadott vagy szöveges üzenet OTP helytelen kód | Az a felhasználó hibás kódot adott meg. Rendelkezik, azokat újra egy új kódot kér, vagy jelentkezzen be újra. |

## <a name="errors-that-require-support"></a>Támogatást igénylő hibák

Ha az egyik ezeket a hibákat észlel, azt javasoljuk, hogy Ön [forduljon az ügyfélszolgálathoz](#contact-microsoft-support) diagnosztikai segítséget. Nincs nem szabványos készletét lépéseket, amelyek kezelheti ezeket a hibákat. Amikor kapcsolatba lép a támogatással, ügyeljen arra, hogy mennyi információ a lehető a hibához vezető lépéseket, és a bérlő adatait tartalmazza.

| Hibakód | Hibaüzenet |
| ---------- | ------------- |
| **InvalidParameter** | Kérelem nem lehet null értékű |
| **InvalidParameter** | Objektumazonosító nem lehet null értékű vagy üres ReplicationScope:{0} |
| **InvalidParameter** | Cégnév hossza \{0} \ hosszabb, mint a megengedett maximális hossz {1} |
| **InvalidParameter** | UserPrincipalName nem lehet null értékű vagy üres |
| **InvalidParameter** | A megadott Bérlőazonosító nem a megfelelő formátumban van. |
| **InvalidParameter** | Munkamenet-azonosító nem lehet null értékű vagy üres |
| **InvalidParameter** | Nem sikerült feloldani bármely ProofData kérelemből vagy az Msods. A ProofData ismeretlen |
| **InternalError** |  |
| **OathCodePinIncorrect** |  |
| **VersionNotSupported** |  |
| **MFAPinNotSetup** |  |

## <a name="next-steps"></a>További lépések

### <a name="troubleshoot-user-accounts"></a>Felhasználói fiókok hibáinak elhárítása

Ha a felhasználók [problémákat tapasztal a kétlépéses ellenőrzéssel](../user-help/multi-factor-authentication-end-user-troubleshoot.md), Súgó őket saját diagnosztizálhatja a problémákat.

### <a name="contact-microsoft-support"></a>Kapcsolatfelvétel a Microsoft ügyfélszolgálatával

Ha további segítségre van szüksége, forduljon a támogatási szakember keresztül [Azure multi-factor Authentication-kiszolgáló támogatási](https://support.microsoft.com/oas/default.aspx?prid=14947). Ha felvette velünk a kapcsolatot, esetén lehet hasznos, a lehető a problémával kapcsolatban, hozzáadhatja a lehető legtöbb információt. Adhat meg információkat tartalmaz az oldal, ahol a hiba, a konkrét hibakód látta az adott munkamenet-azonosító, látott a hibát, és hibakeresési naplókat felhasználó azonosítója.

Támogatási diagnosztikai hibakeresési naplók összegyűjtése, használja a hálózati házirend-kiszolgálón az alábbi lépéseket:

1. Nyissa meg a Beállításszerkesztőt, és keresse meg a HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa set **VERBOSE_LOG** való **igaz**
2. Nyisson meg egy rendszergazdai parancssort, és futtassa a következő parancsokat:

   ```
   Mkdir c:\NPS
   Cd NPS
   netsh trace start Scenario=NetConnection capture=yes tracefile=c:\NPS\nettrace.etl
   logman create trace "NPSExtension" -ow -o c:\NPS\NPSExtension.etl -p {7237ED00-E119-430B-AB0F-C63360C8EE81} 0xffffffffffffffff 0xff -nb 16 16 -bs 1024 -mode Circular -f bincirc -max 4096 -ets
   logman update trace "NPSExtension" -p {EC2E6D3A-C958-4C76-8EA4-0262520886FF} 0xffffffffffffffff 0xff -ets
   ```

3. Reprodukálja a hibát

4. Állítsa le a nyomkövetést, a következő parancsokkal:

   ```
   logman stop "NPSExtension" -ets
   netsh trace stop
   wevtutil epl AuthNOptCh C:\NPS\%computername%_AuthNOptCh.evtx
   wevtutil epl AuthZOptCh C:\NPS\%computername%_AuthZOptCh.evtx
   wevtutil epl AuthZAdminCh C:\NPS\%computername%_AuthZAdminCh.evtx
   Start .
   ```

5. Nyissa meg a Beállításszerkesztőt, és keresse meg a HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa set **VERBOSE_LOG** való **false (hamis)**
6. Zip-a C:\NPS mappa tartalmát, és csatolja a tömörített fájlt a támogatási esetet.
