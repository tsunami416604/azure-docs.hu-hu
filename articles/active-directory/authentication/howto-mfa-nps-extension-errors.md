---
title: Az Azure MFA NPS-bővítmény hibáinak elhárítása – Azure Active Directory
description: Segítség az Azure többtényezős hitelesítéshez való kapcsolathívási kiszolgáló bővítményével kapcsolatos problémák megoldásához
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 330363a78aa9b642f4794cee40bbf040d3484b4b
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80653718"
---
# <a name="resolve-error-messages-from-the-nps-extension-for-azure-multi-factor-authentication"></a>Hibaüzenetek által jelzett problémák megszüntetése az Azure Multi-Factor Authentication NPS-bővítményéből

Ha hibákat tapasztal az Azure többtényezős hitelesítés hez a nps-bővítményben, használja ezt a cikket a megoldás gyorsabb eléréséhez. A hálózati házirend-kiszolgáló bővítménynaplói az Eseménynaplóban találhatók az **Egyéni nézetek** > **kiszolgálói szerepkörök** > **hálózati házirend és hozzáférési szolgáltatások** csoportban azon a kiszolgálón, amelyen a hálózati házirend-kiszolgáló bővítmény telepítve van.

## <a name="troubleshooting-steps-for-common-errors"></a>A gyakori hibák elhárítási lépései

| Hibakód | Hibaelhárítási lépések |
| ---------- | --------------------- |
| **CONTACT_SUPPORT** | [Lépjen kapcsolatba](#contact-microsoft-support)az ügyfélszolgálattal, és sorolja fel a naplók gyűjtésére szolgáló lépések listáját. Adjon meg annyi információt a hiba előtt történtekről, mint ahiba, beleértve a bérlői azonosítót és a felhasználó egyszerű nevét (UPN). |
| **CLIENT_CERT_INSTALL_ERROR** | Lehet, hogy az ügyféltanúsítvány telepítése vagy a bérlőhöz társított probléma merül fel. Az ügyféltanúsítványokkal kapcsolatos problémák kivizsgálásához kövesse [az MFA hálózati házirend-kiszolgáló bővítmény hibaelhárítása](howto-mfa-nps-extension.md#troubleshooting) című témakör utasításait. |
| **ESTS_TOKEN_ERROR** | Kövesse az [MFA nps bővítmény hibaelhárítása](howto-mfa-nps-extension.md#troubleshooting) című témakör utasításait az ügyféltanúsítvány- és ADAL-tokenproblémák kivizsgálásához. |
| **HTTPS_COMMUNICATION_ERROR** | A hálózati kiszolgáló nem tud válaszokat kapni az Azure MFA-tól. Ellenőrizze, hogy a tűzfalak kétirányúan vannak-e nyitva a be- éshttps://adnotifications.windowsazure.com |
| **HTTP_CONNECT_ERROR** | A kapcsolati kiszolgálón a kapcsolatikiszolgáló bővítménye, ellenőrizze, hogy el tudja-e érni `https://adnotifications.windowsazure.com` a és `https://login.microsoftonline.com/`a . Ha ezek a webhelyek nem töltődnek be, hárítsa el a kiszolgálón lévő kapcsolódáshibáit. |
| **NPS-bővítmény az Azure MFA-hoz:** <br> Az Azure MFA-bővítmény e-alapú n-ps-bővítmény csak az AccessAccept-állapotban lévő Radius-kérelmek másodlagos hitelesítését hajtja végre. AccessReject válaszállapotú felhasználónévre érkezett kérelem, figyelmen kívül hagyva a kérést. | Ez a hiba általában az AD hitelesítési hibáját tükrözi, vagy azt, hogy a hálózati kiszolgáló nem tud válaszokat kapni az Azure AD-től. Ellenőrizze, hogy a tűzfalak kétirányúan `https://adnotifications.windowsazure.com` vannak-e nyitva a 80-as és 443-as portokról érkező és onnan érkező és `https://login.microsoftonline.com` használó forgalom számára. Azt is fontos ellenőrizni, hogy a Hálózati hozzáférési engedélyek TELEFONOS bevitel lapján a beállítás "hozzáférés vezérlése hálózati házirenden keresztül" beállítással van-e beállítva. Ez a hiba akkor is előidézhető, ha a felhasználóhoz nincs licenc rendelve. |
| **REGISTRY_CONFIG_ERROR** | Hiányzik egy kulcs az alkalmazás rendszerleíró adatbázisából, ami azért lehet, mert a [PowerShell-parancsfájl](howto-mfa-nps-extension.md#install-the-nps-extension) nem futott a telepítés után. A hibaüzenetnek tartalmaznia kell a hiányzó kulcsot. Győződjön meg arról, hogy a kulcs a HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa mappában van. |
| **REQUEST_FORMAT_ERROR** <br> A sugárkérelemből hiányzik a kötelező radius felhasználónév\Azonosító attribútum. Annak ellenőrzése, hogy a n-ps fogadott-e RADIUS-kérelmeket | Ez a hiba általában egy telepítési problémát tükröz. A nps kiterjesztést radius-kérelmek fogadására képes nps-kiszolgálókra kell telepíteni. Az RDG és rras szolgáltatások függőségeként telepített nps-kiszolgálók nem fogadnak radius-kérelmeket. A nps kiterjesztés nem működik, ha ilyen telepítésekre van telepítve, és a hibák kivannak, mivel nem tudja olvasni a hitelesítési kérelem részleteit. |
| **REQUEST_MISSING_CODE** | Győződjön meg arról, hogy a nps és a NAS-kiszolgálók közötti jelszótitkosítási protokoll támogatja a használt másodlagos hitelesítési módszert. **A PAP** támogatja az Azure MFA összes hitelesítési módszerét a felhőben: telefonhívás, egyirányú szöveges üzenet, mobilalkalmazás-értesítés és mobilalkalmazás-ellenőrző kód. **A CHAPV2** és **az EAP** támogatja a telefonhívást és a mobilalkalmazás-értesítéseket. |
| **USERNAME_CANONICALIZATION_ERROR** | Ellenőrizze, hogy a felhasználó jelen van-e a helyszíni Active Directory-példányban, és hogy a nps szolgáltatás rendelkezik-e a címtár eléréséhez szükséges engedélyekkel. Ha erdőközi bizalmi kapcsolatokat használ, további segítségért [forduljon](#contact-microsoft-support) az ügyfélszolgálathoz. |

### <a name="alternate-login-id-errors"></a>Alternatív bejelentkezési azonosítóhibák

| Hibakód | Hibaüzenet | Hibaelhárítási lépések |
| ---------- | ------------- | --------------------- |
| **ALTERNATE_LOGIN_ID_ERROR** | Hiba: a userObjectSid-keresés nem sikerült | Ellenőrizze, hogy a felhasználó létezik-e a helyszíni Active Directory-példányban. Ha erdőközi bizalmi kapcsolatokat használ, további segítségért [forduljon](#contact-microsoft-support) az ügyfélszolgálathoz. |
| **ALTERNATE_LOGIN_ID_ERROR** | Hiba: A másik bejelentkezési azonosító javallott | Ellenőrizze, hogy LDAP_ALTERNATE_LOGINID_ATTRIBUTE [érvényes active directory attribútumra](https://msdn.microsoft.com/library/ms675090(v=vs.85).aspx)van-e beállítva. <br><br> Ha LDAP_FORCE_GLOBAL_CATALOG értéke Igaz, vagy LDAP_LOOKUP_FORESTS nem üres értékkel van konfigurálva, ellenőrizze, hogy konfigurált-e globális katalógust, és hogy az AlternateLoginId attribútum hozzá lett-e adva. <br><br> Ha LDAP_LOOKUP_FORESTS nem üres értékkel van konfigurálva, ellenőrizze, hogy az érték helyes-e. Ha egynél több erdőnév van, a neveket pontosvesszővel kell elválasztani, nem szóközökkel. <br><br> Ha ezek a lépések nem oldják meg a problémát, további segítségért [forduljon](#contact-microsoft-support) az ügyfélszolgálathoz. |
| **ALTERNATE_LOGIN_ID_ERROR** | Hiba: Az alternatív loginId érték üres | Ellenőrizze, hogy az AlternateLoginId attribútum konfigurálva van-e a felhasználó számára. |

## <a name="errors-your-users-may-encounter"></a>A felhasználók által esetlegesen előforduló hibák

| Hibakód | Hibaüzenet | Hibaelhárítási lépések |
| ---------- | ------------- | --------------------- |
| **Hozzáférés megtagadva** | A hívó bérlője nem rendelkezik hozzáférési engedélyekkel a felhasználó hitelesítéséhez | Ellenőrizze, hogy a bérlői tartomány és az egyszerű felhasználónév (UPN) tartománya megegyezik-e. Győződjön meg például arról, hogy user@contoso.com a Contoso-bérlő hitelesítése történik. Az upn egy érvényes felhasználót jelöl a bérlő számára az Azure-ban. |
| **AuthenticationMethodNotConfigured** | A megadott hitelesítési módszer nincs konfigurálva a felhasználószámára | A felhasználó nak adja hozzá vagy ellenőrizze az ellenőrzési módszereit a [Beállítások kezelése a kétlépéses ellenőrzéshez](../user-help/multi-factor-authentication-end-user-manage-settings.md)című részben található utasításoknak megfelelően. |
| **AuthenticationMethodNotSupported** | A megadott hitelesítési módszer nem támogatott. | Gyűjtse össze a hibaüzenetet tartalmazó összes naplót, és [lépjen kapcsolatba az ügyfélszolgálattal.](#contact-microsoft-support) Amikor kapcsolatba lép az ügyfélszolgálattal, adja meg a felhasználónevet és a hibát kiváltó másodlagos ellenőrzési módszert. |
| **BecAccess Megtagadva** | MSODS Bec hívás visszamegtagadott hozzáférés megtagadva, valószínűleg a felhasználónév nincs definiálva a bérlőben | A felhasználó jelen van az Active Directory ban a helyszíni, de nem szinkronizálja az Azure AD az AD Connect. Vagy a felhasználó hiányzik a bérlő. Adja hozzá a felhasználót az Azure AD-hez, és vegye rá őket, hogy a [kétlépéses ellenőrzés beállításainak kezelése](../user-help/multi-factor-authentication-end-user-manage-settings.md)című utasításaiknak megfelelően adják hozzá ellenőrzési módszereiket. |
| **InvalidFormat** vagy **StrongAuthenticationServiceInvalidParameter** | A telefonszám felismerhetetlen formátumú | A felhasználó javítsa ki az ellenőrző telefonszámokat. |
| **Érvénytelen munkamenet** | A megadott munkamenet érvénytelen vagy lejárt | A munkamenet több mint három percet vett igénybe. A hitelesítési kérelem megírását követő három percen belül ellenőrizze, hogy a felhasználó beírja-e az ellenőrző kódot, vagy válaszol-e az alkalmazásértesítésre. Ha ez nem oldja meg a problémát, ellenőrizze, hogy nincsenek-e hálózati késések az ügyfél, a NAS Server, a Hálózati házirend-kiszolgáló és az Azure MFA-végpont között.  |
| **NoDefaultAuthenticationMethodisconfigured** | Nem volt beállítva alapértelmezett hitelesítési módszer a felhasználóhoz | A felhasználó nak adja hozzá vagy ellenőrizze az ellenőrzési módszereit a [Beállítások kezelése a kétlépéses ellenőrzéshez](../user-help/multi-factor-authentication-end-user-manage-settings.md)című részben található utasításoknak megfelelően. Ellenőrizze, hogy a felhasználó az alapértelmezett hitelesítési módszert választotta-e, és konfigurálta-e a metódust a fiókjához. |
| **OathCodePinIncorrect** | Hibás kód és pin beírva. | Ez a hiba nem várható a nps bővítményben. Ha a felhasználó ezt tapasztalja, forduljon az [ügyfélszolgálathoz](#contact-microsoft-support) hibaelhárítási segítségért. |
| **A ProofDataNotFound** | A próbanyomat-adatok nincsenek konfigurálva a megadott hitelesítési módszerhez. | A felhasználó próbálkozzon egy másik ellenőrzési módszerrel, vagy adjon hozzá új ellenőrzési módszereket a [Beállítások kezelése a kétlépéses ellenőrzéshez](../user-help/multi-factor-authentication-end-user-manage-settings.md)című utasításainak megfelelően. Ha a felhasználó továbbra is látja ezt a hibát, miután megerősítette, hogy az ellenőrzési módszer megfelelően van beállítva, [forduljon az ügyfélszolgálathoz.](#contact-microsoft-support) |
| **SMSAuthFailedWrongCodePinEntered** | Hibás kód és pin beírva. (1. | Ez a hiba nem várható a nps bővítményben. Ha a felhasználó ezt tapasztalja, forduljon az [ügyfélszolgálathoz](#contact-microsoft-support) hibaelhárítási segítségért. |
| **Bérlő blokkolva** | A bérlő le van tiltva | [Lépjen kapcsolatba](#contact-microsoft-support) a címtár-azonosítóval az Azure Portalon az Azure AD-tulajdonságok lapján. |
| **A felhasználó nem található** | A megadott felhasználó nem található. | A bérlő már nem látható aktívként az Azure AD-ben. Ellenőrizze, hogy az előfizetés aktív-e, és rendelkezik-e a szükséges első féltől származó alkalmazásokkal. Győződjön meg arról is, hogy a tanúsítvány tulajdonosában a bérlő a várt módon van elvárható, és a tanúsítvány továbbra is érvényes és regisztrálva van a szolgáltatásnév alatt. |

## <a name="messages-your-users-may-encounter-that-arent-errors"></a>A felhasználók által esetlegesen előforduló olyan üzenetek, amelyek nem hibák

Előfordulhat, hogy a felhasználók üzeneteket kapnak a többtényezős hitelesítéstől, mert a hitelesítési kérelmet nem sikerült. Ezek nem hibák a konfigurációs termékben, hanem szándékos figyelmeztetések, amelyek indokolják, hogy miért lett megtagadva egy hitelesítési kérelem.

| Hibakód | Hibaüzenet | Javasolt lépések | 
| ---------- | ------------- | ----------------- |
| **OathCodeIncorrect** | Hibás kód beírva\OATH kód helytelen | A felhasználó rossz kódot adott meg. Kérje meg őket, hogy próbálkozzanak újra új kód kérésével, vagy jelentkezzenbe újra. | 
| **SMSAuthFailedMaxAllowedCodeRetryReached** | Elérte a maximálisan megengedett kódismétlést | A felhasználó túl sokszor nem felelt meg az ellenőrzési kihíváson. A beállításoktól függően előfordulhat, hogy egy rendszergazdanak fel kell oldania a zárolást.  |
| **SMSAuthFailedWrongCodeEntered** | Hibás kód bevitele/Szöveges üzenet OTP helytelen | A felhasználó rossz kódot adott meg. Kérje meg őket, hogy próbálkozzanak újra új kód kérésével, vagy jelentkezzenbe újra. |

## <a name="errors-that-require-support"></a>Támogatást igénylő hibák

Ha a következő hibák valamelyikével találkozik, javasoljuk, hogy kérjen segítséget az [ügyfélszolgálattól.](#contact-microsoft-support) Nincs olyan szabványos lépéskészlet, amely képes lenne kezelni ezeket a hibákat. Amikor kapcsolatba lép az ügyfélszolgálattal, győződjön meg arról, hogy a lehető legtöbb információt adja meg a hibához vezető lépésekről és a bérlői adatokról.

| Hibakód | Hibaüzenet |
| ---------- | ------------- |
| **InvalidParameter paraméter** | A kérelem nem lehet null |
| **InvalidParameter paraméter** | Az ObjectId nem lehet null vagy üres a ReplicationScope esetében:{0} |
| **InvalidParameter paraméter** | A 0}\ \{vállalatnév hossza hosszabb, mint a megengedett maximális hossz.{1} |
| **InvalidParameter paraméter** | A UserPrincipalName nem lehet null vagy üres. |
| **InvalidParameter paraméter** | A megadott TenantId formátuma nem megfelelő |
| **InvalidParameter paraméter** | A munkamenet-azonosító nem lehet null vagy üres |
| **InvalidParameter paraméter** | Nem oldotta fel a ProofData a kérésből vagy az Msods-ból. A ProofData ismeretlen |
| **InternalError** |  |
| **OathCodePinIncorrect** |  |
| **VersionNotSupported** |  |
| **MFAPinNotBeállítás** |  |

## <a name="next-steps"></a>További lépések

### <a name="troubleshoot-user-accounts"></a>Felhasználói fiókok – problémamegoldás

Ha a felhasználóknak problémái vannak [a kétlépéses ellenőrzéssel,](../user-help/multi-factor-authentication-end-user-troubleshoot.md)segítsen nekik a problémák öndiagnosztizálásában.

### <a name="health-check-script"></a>Állapot-ellenőrző parancsfájl

Az [Azure MFA NFA NPS-bővítmény állapot-ellenőrzési parancsfájl](https://docs.microsoft.com/samples/azure-samples/azure-mfa-nps-extension-health-check/azure-mfa-nps-extension-health-check/) alapvető állapot-ellenőrzést hajt végre a hálózati híváshálózati bővítmény hibaelhárításakor. Futtassa a parancsfájlt, és válassza a 3.

### <a name="contact-microsoft-support"></a>Kapcsolatfelvétel a Microsoft ügyfélszolgálatával

Ha további segítségre van szüksége, forduljon egy támogatási szakemberhez az [Azure többtényezős hitelesítési kiszolgálótámogatásán](https://support.microsoft.com/oas/default.aspx?prid=14947)keresztül. Amikor kapcsolatba lép velünk, hasznos, ha a lehető legtöbb információt meg tudja mondani a problémáról. A megadott információk közé tartozik az oldal, ahol a hibát látta, a konkrét hibakód, az adott munkamenet-azonosító, a hibát megkezdő felhasználó azonosítója és a hibakeresési naplók.

A hibakeresési naplók támogatási diagnosztikához történő gyűjtéséhez hajtsa végre az alábbi lépéseket a nps-kiszolgálón:

1. A Rendszerleíróadatbázis-szerkesztő megnyitása és a HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa VERBOSE_LOG **true** értékre való tallózással **TRUE**
2. Nyisson meg egy Rendszergazda parancssort, és futtassa a következő parancsokat:

   ```
   Mkdir c:\NPS
   Cd NPS
   netsh trace start Scenario=NetConnection capture=yes tracefile=c:\NPS\nettrace.etl
   logman create trace "NPSExtension" -ow -o c:\NPS\NPSExtension.etl -p {7237ED00-E119-430B-AB0F-C63360C8EE81} 0xffffffffffffffff 0xff -nb 16 16 -bs 1024 -mode Circular -f bincirc -max 4096 -ets
   logman update trace "NPSExtension" -p {EC2E6D3A-C958-4C76-8EA4-0262520886FF} 0xffffffffffffffff 0xff -ets
   ```

3. Reprodukálja a hibát

4. Állítsa le a nyomkövetést az alábbi parancsokkal:

   ```
   logman stop "NPSExtension" -ets
   netsh trace stop
   wevtutil epl AuthNOptCh C:\NPS\%computername%_AuthNOptCh.evtx
   wevtutil epl AuthZOptCh C:\NPS\%computername%_AuthZOptCh.evtx
   wevtutil epl AuthZAdminCh C:\NPS\%computername%_AuthZAdminCh.evtx
   Start .
   ```

5. A Rendszerleíróadatbázis-szerkesztő megnyitása és a HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa **VERBOSE_LOG** false **értékre** való tallózással
6. Zip a C:\NPS mappa tartalmát, és csatolja a tömörített fájlt a támogatási esethez.
