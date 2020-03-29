---
title: Azure MFA-felhasználók adatgyűjtése – Azure Active Directory
description: Milyen adatokat használnak a felhasználók hitelesítéséhez az Azure többtényezős hitelesítése?
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3bc0f0dbcd08df887b2484be6ca8c92a85962c1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74848289"
---
# <a name="azure-multi-factor-authentication-user-data-collection"></a>Az Azure többtényezős hitelesítésfelhasználói adatgyűjtése

Ez a dokumentum bemutatja, hogyan keresheti meg az Azure Multi-Factor Authentication Server (MFA Server) és az Azure MFA (Cloud-based) által gyűjtött felhasználói adatokat abban az esetben, ha el szeretné távolítani.

[!INCLUDE [gdpr-hybrid-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="information-collected"></a>Összegyűjtött információk

Az MFA-kiszolgáló, a hálózati kiszolgáló bővítménye és a Windows Server 2016 Azure MFA AD FS adapter 90 napig gyűjti és tárolja a következő információkat.

Hitelesítési kísérletek (jelentéskészítésre és hibaelhárításra szolgál):

- Időbélyeg
- Felhasználónév
- Utónév
- Vezetéknév
- E-mail-cím
- Felhasználói csoport
- Hitelesítési módszer (telefonhívás, szöveges üzenet, mobilalkalmazás, OATH token)
- Telefonhívási mód (normál, PIN)
- Szöveges üzenet iránya (egyirányú, kétirányú)
- Szöveges üzenet mód (OTP, OTP + PIN)
- Mobilalkalmazás-mód (normál, PIN-kód)
- OATH token mód (normál, PIN)
- Hitelesítés típusa
- Alkalmazásnév
- Elsődleges hívás országkódja
- Elsődleges hívás telefonszáma
- Elsődleges hívásbővítmény
- Elsődleges hívás hitelesítve
- Elsődleges hívás eredménye
- Biztonsági hívás országkódja
- Visszahívási telefonszám
- Biztonsági hívás bővítménye
- Biztonsági másolat hívása hitelesítve
- Biztonsági hívás eredménye
- Összesen hitelesítve
- Összesített eredmény
- Results (Eredmények)
- Hitelesített
- Eredmény
- IP-cím megakozása
- Eszközök
- Eszköztoken
- Eszköz típusa
- Mobilalkalmazás verziója
- Operációs rendszer verziója
- Eredmény
- Használt értesítés ellenőrzése

Aktiválások (fiók aktiválására tett kísérletek a Microsoft Hitelesítő mobilalkalmazásban):
- Felhasználónév
- Fiók neve
- Időbélyeg
- Az aktiválási kód eredményének beszereznie
- Sikeres aktiválás
- Aktiválási hiba
- Aktiválási állapot eredménye
- Eszköz neve
- Eszköz típusa
- Alkalmazásverzió
- OATH token engedélyezve

Blokkok (a blokkolt állapot meghatározására és a jelentésre szolgál):

- Időbélyeg blokkolása
- Letiltás felhasználónév szerint
- Felhasználónév
- Országkód
- Telefonszám
- Telefonszámot formázva
- Mellék
- Tiszta kiterjesztés
- Blokkolva
- Ok letiltása
- Befejezési időbélyeg
- Befejezés oka
- Fiókzárolás
- Csalásriasztás
- Csalás riasztás nem blokkolt
- Nyelv

Megkerülések (jelentéskészítésre szolgálnak):

- Időbélyeg megkerülése
- Másodpercek megkerülése
- Megkerülés felhasználónév alapján
- Felhasználónév
- Országkód
- Telefonszám
- Telefonszámot formázva
- Mellék
- Tiszta kiterjesztés
- Ok kihagyása
- Befejezési időbélyeg
- Befejezés oka
- Használt megkerülés

Változások (a felhasználói módosítások szinkronizálására szolgál az MFA-kiszolgálón vagy az Azure AD-ben):

- Időbélyeg módosítása
- Felhasználónév
- Új országkód
- Új telefonszám
- Új bővítmény
- Új biztonsági mentés országkódja
- Új biztonsági másolat telefonszáma
- Új biztonsági mentési bővítmény
- Új PIN-kód
- PIN-kód módosítása szükséges
- Régi eszköztoken
- Új eszköztoken

## <a name="gather-data-from-mfa-server"></a>Adatok gyűjtése az MFA-kiszolgálóról

Az MFA Server 8.0-s vagy újabb verziójában a következő folyamat lehetővé teszi a rendszergazdák számára, hogy a felhasználók számára az összes adatot exportálják:

- Jelentkezzen be az MFA-kiszolgálóra, keresse meg a **Felhasználók** lapot, jelölje ki a kérdéses **felhasználót,** és kattintson a Szerkesztés gombra. Az egyes lapokképernyős (Alt-PrtScn) alkalmazásával adja meg a felhasználónak az aktuális MFA-beállításokat.
- Az MFA-kiszolgáló parancssorából futtassa a következő parancsot, amely a telepítésnek `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe export <username>` megfelelően módosítja az elérési utat egy JSON formátumú fájl létrehozásához.
- A rendszergazdák a webszolgáltatás SDK GetUserGdpr műveletét is használhatják egy adott felhasználó számára gyűjtött összes MFA-felhőszolgáltatás-információ exportálására, vagy egy nagyobb jelentéskészítési megoldásba való beépítésre.
- Keressen `C:\Program Files\Multi-Factor Authentication Server\Logs\MultiFactorAuthSvc.log` rá és készítsen\<biztonsági másolatokat a "felhasználónév>" kifejezésre (az idézőjelek szerepeljen a keresésben), hogy megtalálja a hozzáadott vagy módosított felhasználói rekord összes példányát.
   - Ezek a rekordok korlátozhatók (de nem szüntethetők meg) az MFA-kiszolgáló ux-ének naplózása, naplózás a Naplófájlok lap **"Felhasználói módosítások naplózása"** listájának bejelölésével.
   - Ha a syslog konfigurálva van, és a **"Felhasználói változások naplózása"** be van jelölve az MFA-kiszolgáló UX, Naplózás, Syslog (Syslog) szakaszában, akkor a naplóbejegyzések a syslog ból gyűjthetők össze.
- A felhasználónév más előfordulásai a MultiFactorAuthSvc.log fájlban és a hitelesítési kísérletekhez kapcsolódó Egyéb MFA-kiszolgálónapló-fájlok ban a MultiFactorAuthGdpr.exe export vagy webszolgáltatás SDK használatával megadott adatokkal operatívnak és duplikáltnak minősülnek. GetUserGdpr.

## <a name="delete-data-from-mfa-server"></a>Adatok törlése az MFA-kiszolgálóról

Az MFA-kiszolgáló parancssorából futtassa a következő parancsot, amely a telepítésnek `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe delete <username>` megfelelően módosítja az elérési utat a felhasználó számára gyűjtött összes MFA-felhőszolgáltatás-információ törléséhez.

- Az exportálásban szereplő adatokat valós időben törlik, de a működési vagy párhuzamos adatok teljes eltávolítása akár 30 napot is igénybe vehet.
- A rendszergazdák a webszolgáltatás SDK DeleteUserGdpr műveletét is használhatják egy adott felhasználó számára gyűjtött összes MFA-felhőszolgáltatás-információ törlésére, vagy egy nagyobb jelentéskészítési megoldásba való beépítésre.

## <a name="gather-data-from-nps-extension"></a>Adatok gyűjtése a nps-bővítményből

A [Microsoft adatvédelmi portálján](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) exportálási kérelmet nyújthat be.

- Az MFA-adatok szerepelnek az exportálásban, amely órákat vagy napokat vehet igénybe.
- A felhasználónév előfordulása az AzureMfa/AuthN/AuthNOptCh, AzureMfa/AuthZ/AuthZAdminCh és Az AzureMfa/AuthZ/AuthZOptCh eseménynaplókban az exportálásban megadott információkkal párhuzamosnak minősülnek.

## <a name="delete-data-from-nps-extension"></a>Adatok törlése a nps-bővítményből

A [Microsoft Adatvédelmi portál segítségével](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) kérheti a Fiók bezárása iránti kérelmet a felhasználó számára gyűjtött összes MFA felhőszolgáltatás-információ törléséhez.

- Az adatok teljes eltávolítása akár 30 napot is igénybe vehet.

## <a name="gather-data-from-windows-server-2016-azure-mfa-ad-fs-adapter"></a>Adatok gyűjtése a Windows Server 2016 Azure MFA AD FS adapterről

A [Microsoft adatvédelmi portálján](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) exportálási kérelmet nyújthat be. 

- Az MFA-adatok szerepelnek az exportálásban, amely órákat vagy napokat vehet igénybe.
- A felhasználónév előfordulása az AD FS nyomkövetési/hibakeresési eseménynaplókban (ha engedélyezve van) operatívnak és az exportálásban megadott információk nak való átfedésnek minősül.

## <a name="delete-data-from-windows-server-2016-azure-mfa-ad-fs-adapter"></a>Adatok törlése a Windows Server 2016 Azure MFA AD FS adapterről

A [Microsoft Adatvédelmi portál segítségével](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) kérheti a Fiók bezárása iránti kérelmet a felhasználó számára gyűjtött összes MFA felhőszolgáltatás-információ törléséhez.

- Az adatok teljes eltávolítása akár 30 napot is igénybe vehet.

## <a name="gather-data-for-azure-mfa"></a>Adatok gyűjtése az Azure MFA-hoz

A [Microsoft adatvédelmi portálján](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) exportálási kérelmet nyújthat be.

- Az MFA-adatok szerepelnek az exportálásban, amely órákat vagy napokat vehet igénybe.

## <a name="delete-data-for-azure-mfa"></a>Adatok törlése az Azure MFA-hoz

A [Microsoft Adatvédelmi portál segítségével](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) kérheti a Fiók bezárása iránti kérelmet a felhasználó számára gyűjtött összes MFA felhőszolgáltatás-információ törléséhez.

- Az adatok teljes eltávolítása akár 30 napot is igénybe vehet.

## <a name="next-steps"></a>További lépések

[MFA-kiszolgáló jelentése](howto-mfa-reporting.md)
