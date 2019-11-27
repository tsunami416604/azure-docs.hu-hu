---
title: Azure MFA felhasználói adatgyűjtés – Azure Active Directory
description: Milyen információkat használ a felhasználók Azure Multi-Factor Authentication általi hitelesítéséhez?
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 09aa95e55b3d253ef41724298247694b9883e31f
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381765"
---
# <a name="azure-multi-factor-authentication-user-data-collection"></a>Azure Multi-Factor Authentication felhasználói adatgyűjtés

Ez a dokumentum ismerteti az Azure Multi-Factor Authentication-kiszolgáló (MFA-kiszolgáló) és az Azure MFA (felhőalapú) által gyűjtött felhasználói adatok megkeresését abban az esetben, ha el szeretné távolítani.

[!INCLUDE [gdpr-hybrid-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="information-collected"></a>Összegyűjtött információk

Az MFA-kiszolgáló, az NPS-bővítmény és a Windows Server 2016 Azure MFA AD FS adapter a következő adatokat gyűjti és tárolja 90 napig.

Hitelesítési kísérletek (jelentéskészítéshez és hibaelhárításhoz használatos):

- Időbélyeg
- Felhasználónév
- Utónév
- Vezetéknév
- E-mail cím
- Felhasználói csoport
- Hitelesítési módszer (telefonhívás, szöveges üzenet, Mobile App, eskü token)
- Telefonhívási mód (standard, PIN)
- Szöveges üzenet iránya (egyirányú, kétirányú)
- Szöveges üzenet mód (OTP, OTP + PIN)
- Mobile App Mode (standard, PIN)
- ESKÜ token mód (standard, PIN)
- Hitelesítés típusa
- Alkalmazásnév
- Elsődleges hívási országkód
- Elsődleges hívás telefonszáma
- Elsődleges hívási bővítmény
- Elsődleges hívás hitelesítve
- Elsődleges hívás eredménye
- Biztonsági mentési hívás országkód
- Biztonságimásolat-hívás telefonszáma
- Biztonságimásolat-hívások bővítménye
- Biztonsági mentési hívás hitelesítve
- Biztonsági mentési hívás eredménye
- Teljes hitelesítés
- Teljes eredmény
- Results (Eredmények)
- Hitelesített
- Eredmény
- IP-cím kezdeményezése
- Eszközök
- Eszköz jogkivonata
- Eszköz típusa
- Mobile App-verzió
- Operációs rendszer verziója
- Eredmény
- Értesítés használatban

Aktiválások (a Microsoft Authenticator Mobile alkalmazásban lévő fiók aktiválását kísérli meg):
- Felhasználónév
- Fiók neve
- Időbélyeg
- Aktivációs kód eredményének beolvasása
- Sikeres aktiválás
- Aktiválási hiba
- Aktiválási állapot eredménye
- Eszköz neve
- Eszköz típusa
- Alkalmazásverzió
- ESKÜ-token engedélyezve

Blokkok (a blokkolt állapot és a jelentéskészítés megállapításához használatos):

- Időbélyeg letiltása
- Blokkolás Felhasználónév szerint
- Felhasználónév
- Országkód:
- Telefonszám
- Telefonszám formázva
- Mellék
- Tiszta bővítmény
- Blokkolt
- Blokkolás oka
- Befejezés időbélyeg
- Befejezés oka
- Fiókzárolás
- Csalási riasztás
- Csalási riasztás nincs letiltva
- Nyelv

Megkerülések (jelentéskészítéshez használatos):

- Mellőzési időbélyeg
- Mellőzési másodpercek
- Mellőzés Felhasználónév alapján
- Felhasználónév
- Országkód:
- Telefonszám
- Telefonszám formázva
- Mellék
- Tiszta bővítmény
- Mellőzés oka
- Befejezés időbélyeg
- Befejezés oka
- Használat mellőzése

Módosítások (az MFA-kiszolgálóra vagy az Azure AD-ra történő felhasználói változások szinkronizálásához használatos):

- Időbélyeg módosítása
- Felhasználónév
- Új országkód
- Új telefonszám
- Új bővítmény
- Új biztonságimásolat-országkód
- Új tartalék telefonszám
- Új biztonsági mentési bővítmény
- Új PIN-kód
- PIN-kód módosítása kötelező
- Régi eszköz tokenje
- Új eszköz jogkivonata

## <a name="gather-data-from-mfa-server"></a>Adatok összegyűjtése az MFA-kiszolgálóról

Az MFA-kiszolgáló 8,0-es vagy újabb verziója esetén az alábbi folyamat lehetővé teszi a rendszergazdák számára az összes felhasználó exportálását a felhasználók számára:

- Jelentkezzen be az MFA-kiszolgálóra, lépjen a **felhasználók** lapra, válassza ki a szóban forgó felhasználót, majd kattintson a **Szerkesztés** gombra. Készítsen képernyőképeket (Alt-PrtScn) az egyes lapokon, hogy a felhasználó az aktuális MFA-beállításait adja meg.
- Az MFA-kiszolgáló parancssorából futtassa a következő parancsot a telepítési `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe export <username>` alapján, hogy egy JSON formátumú fájlt hozzon létre.
- A rendszergazdák a Web Service SDK GetUserGdpr műveletet is használhatják az adott felhasználó számára gyűjtött összes MFA Cloud Service-információ exportálására, vagy egy nagyobb jelentéskészítési megoldásba való beépítésre.
- Keressen `C:\Program Files\Multi-Factor Authentication Server\Logs\MultiFactorAuthSvc.log` és minden biztonsági mentést a "\<username >" (a keresésben szereplő idézőjelekkel együtt), hogy megkeresse az összes hozzáadott vagy módosított felhasználói rekord összes példányát.
   - Ezek a rekordok korlátozottak (de nem távolíthatók el) a **"felhasználói változások naplózása"** lehetőség törlésével az MFA-kiszolgáló UX-ben, a naplózás szakaszban, a naplófájlok lapon.
   - Ha a syslog konfigurálva van, és a **"felhasználói változások naplózása"** be van JELÖLVE az MFA-kiszolgáló UX-ben, a naplózási szakaszban, a syslog lapon, a naplóbejegyzések begyűjthetők a syslog használatával.
- A MultiFactorAuthSvc. log fájlban és a hitelesítési kísérletekhez tartozó más MFA-kiszolgáló naplófájljaiban található Felhasználónév egyéb előfordulása működési és duplikált a MultiFactorAuthGdpr. exe export vagy Web Service SDK használatával biztosított információknak. GetUserGdpr.

## <a name="delete-data-from-mfa-server"></a>Adatok törlése az MFA-kiszolgálóról

Az MFA-kiszolgáló parancssorából futtassa az alábbi parancsot a telepítési `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe delete <username>` alapján, hogy törölje az összes MFA Cloud Service-információ a felhasználó számára gyűjtött adatait.

- Az exportálásban szereplő adategységeket valós időben törli a rendszer, de akár 30 napig is eltarthat, amíg az operatív vagy a duplikált adatfeldolgozás teljesen el nem távolítható.
- A rendszergazdák a Web Service SDK DeleteUserGdpr műveletet is használhatják az adott felhasználó számára gyűjtött összes MFA Cloud Service-információ törlésére, vagy egy nagyobb jelentéskészítési megoldásba való beépítésre.

## <a name="gather-data-from-nps-extension"></a>Adatok összegyűjtése az NPS-bővítményből

Az exportálási kérelem elvégzéséhez használja a [Microsoft adatvédelmi portálját](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) .

- Az MFA-információk az Exportálás részét képezik, amely órákat vagy napokat is igénybe vehet.
- Az AzureMfa/AuthN/AuthNOptCh, a AzureMfa/AuthZ/AuthZAdminCh, valamint a AzureMfa/AuthZ/AuthZOptCh eseménynaplókban előforduló Felhasználónév az exportálás során megadott információnak megfelelően működik és duplikált.

## <a name="delete-data-from-nps-extension"></a>Adatok törlése az NPS-bővítményből

A [Microsoft adatvédelmi portálon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) a fiókhoz tartozó, a felhasználó számára gyűjtött összes MFA Cloud Service-információ törlésére vonatkozó kérést hozhat.

- Akár 30 napig is eltarthat, amíg a rendszer teljesen el nem távolítja az adatgyűjtést.

## <a name="gather-data-from-windows-server-2016-azure-mfa-ad-fs-adapter"></a>Adatok összegyűjtése a Windows Server 2016 Azure MFA AD FS adapterről

Az exportálási kérelem elvégzéséhez használja a [Microsoft adatvédelmi portálját](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) . 

- Az MFA-információk az Exportálás részét képezik, amely órákat vagy napokat is igénybe vehet.
- Az AD FS nyomkövetési/hibakeresési eseménynaplókban (ha engedélyezve vannak) a Felhasználónév előfordulása az exportálás során megadott információk működésének és ismétlődőnek tekintendő.

## <a name="delete-data-from-windows-server-2016-azure-mfa-ad-fs-adapter"></a>Adatok törlése a Windows Server 2016 Azure MFA AD FS adapterről

A [Microsoft adatvédelmi portálon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) a fiókhoz tartozó, a felhasználó számára gyűjtött összes MFA Cloud Service-információ törlésére vonatkozó kérést hozhat.

- Akár 30 napig is eltarthat, amíg a rendszer teljesen el nem távolítja az adatgyűjtést.

## <a name="gather-data-for-azure-mfa"></a>Adatok gyűjtése az Azure MFA-hoz

Az exportálási kérelem elvégzéséhez használja a [Microsoft adatvédelmi portálját](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) .

- Az MFA-információk az Exportálás részét képezik, amely órákat vagy napokat is igénybe vehet.

## <a name="delete-data-for-azure-mfa"></a>Az Azure MFA-hoz tartozó adattörlés

A [Microsoft adatvédelmi portálon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) a fiókhoz tartozó, a felhasználó számára gyűjtött összes MFA Cloud Service-információ törlésére vonatkozó kérést hozhat.

- Akár 30 napig is eltarthat, amíg a rendszer teljesen el nem távolítja az adatgyűjtést.

## <a name="next-steps"></a>További lépések

[MFA-kiszolgáló jelentése](howto-mfa-reporting.md)
