---
title: Az Azure multi-factor Authentication felhasználói adatok gyűjtése
description: Milyen adatok segítségével a felhasználók hitelesítése az Azure multi-factor Authentication?
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.openlocfilehash: b96e1d6cfd476d90afaecd44eaa9861de34130ce
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2019
ms.locfileid: "55081585"
---
# <a name="azure-multi-factor-authentication-user-data-collection"></a>Az Azure multi-factor Authentication felhasználói adatok gyűjtése

Ez a dokumentum ismerteti, hogyan találhatja meg abban az esetben, ha szeretné eltávolítani, az Azure multi-factor Authentication (MFA-kiszolgáló) és az Azure MFA (felhőalapú) által gyűjtött felhasználói adatok.

[!INCLUDE [gdpr-hybrid-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="information-collected"></a>Összegyűjtött adatok

MFA-kiszolgáló, az NPS-bővítményt és a Windows Server 2016 az Azure MFA AD FS-Adapter gyűjteni, és 90 napig a következő adatokat tárolja.

Hitelesítési kísérlet (használt jelentések és hibaelhárítás):

- Időbélyeg
- Felhasználónév
- Utónév
- Vezetéknév
- E-mail-cím
- Felhasználói csoport
- Hitelesítési módszert (telefonhívás, szöveges üzenet, mobilalkalmazás OATH-jogkivonat)
- Telefonhívás módját (Standard, PIN-kód)
- SMS-üzenet irányát (egyirányú, kétirányú)
- SMS-üzenet módját (OTP, OTP + PIN-kód)
- Mobile App Mode (Standard, PIN)
- OATH-Token mód (Standard, PIN-kód)
- Hitelesítés típusa
- Alkalmazásnév
- Elsődleges hívás országkód:
- Elsődleges hívás telefonszám
- Elsődleges hívás bővítmény
- Hitelesített elsődleges hívás
- Elsődleges hívás eredménye
- Biztonsági mentési hívás országkód:
- Biztonsági mentési hívás telefonszám
- Biztonsági mentési hívás bővítmény
- Biztonsági mentési hívás hitelesítése
- Biztonsági mentési hívás eredménye
- A teljes hitelesítése
- Összesített eredmény
- Results (Eredmények)
- Hitelesített
- Eredmény
- Initiating IP Address
- Eszközök
- Eszköztoken
- Az eszköz típusa
- Mobile App-verzió
- Operációs rendszer verziója
- Eredmény
- Értesítési használt keresése

Aktiválások (kísérlet egy fiókot a Microsoft Authenticator mobilalkalmazás aktiválása):
- Felhasználónév
- Fiók neve
- Időbélyeg
- Aktiválási kód eredményének beolvasása
- Sikeres aktiválása
- Hiba aktiválása
- Aktiválási állapot eredménye
- Eszköz neve
- Az eszköz típusa
- Alkalmazásverzió
- OATH Token engedélyezése

Blokkok (tiltott állapot meghatározásához használt és a jelentéskészítési):

- Időbélyeg letiltása
- Felhasználónév letiltása
- Felhasználónév
- Országkód
- Telefonszám
- Formázott telefonszám
- Mellék
- Bővítmény törlése
- Letiltva
- Blokkolás oka
- Befejezés időbélyeg
- Befejezés oka
- Fiókzárolás
- Visszaélési riasztás
- Visszaélési riasztás nincs letiltva
- Nyelv

Körgyűrűk (jelentéskészítéshez használt):

- Mellőzés időbélyeg
- Mellőzés (mp)
- Mellőzés felhasználónév szerint
- Felhasználónév
- Országkód
- Telefonszám
- Formázott telefonszám
- Mellék
- Bővítmény törlése
- Mellőzés oka
- Befejezés időbélyeg
- Befejezés oka
- Mellőzést használt

A módosítások (MFA-kiszolgáló és az AAD felhasználói módosítása szinkronizálása használt):

- Időbélyeg módosítása
- Felhasználónév
- Új országkód
- Új telefonszám
- Új mellék
- Új tartalék országkód
- Új biztonsági mentési telefonszám
- Új biztonsági mentési bővítményt
- Új PIN kód
- Meg kell változtatnia a PIN-kódot
- Régi Eszköztoken
- Új eszköztoken

## <a name="gather-data-from-mfa-server"></a>Gyűjthet velük adatokat az MFA-kiszolgáló

MFA Server 8.0-s vagy újabb verzióját a következő folyamat lehetővé teszi a rendszergazdák számára, hogy a felhasználók számára az összes adat exportálása:

- Jelentkezzen be az MFA-kiszolgáló, keresse meg a **felhasználók** lapra, válassza ki az adott felhasználót, majd kattintson a **szerkesztése** gombra. Képernyőfelvételek (Alt-PrtScn), az egyes fülekre kattintva adja meg a felhasználó MFA beállításaikat.
- A parancssorból az MFA-kiszolgáló, az alábbi parancsot a telepítés megfelelően elérési útvonalának megváltoztatása `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe export <username>` előállításához egy JSON formátumú fájlba.
- A rendszergazdák is használja a Web Service SDK GetUserGdpr műveletet beállítás exportálása minden MFA felhőalapú szolgáltatás adott felhasználó gyűjtött információk, vagy nagyobb jelentéskészítési megoldását építsen be.
- Keresés `C:\Program Files\Multi-Factor Authentication Server\Logs\MultiFactorAuthSvc.log` és minden olyan biztonsági mentéseit "<username>" (az idézőjelek között a keresési) található összes példányát, a felhasználói rekord hozzáadása vagy módosítani.
   - Ezeket a rekordokat korlátozott (de nem kiszűrni) legyen eszközhitelesítést **"Felhasználói változások naplózása"** MFA kiszolgáló felhasználói, naplózás terület, a naplófájlok fület.
   - Ha syslog van konfigurálva, és **"Felhasználói változások naplózása"** jelölőnégyzet be van jelölve az MFA kiszolgáló felhasználói, naplózás terület, a Syslog fülre, majd a naplóbejegyzéseket is begyűjthető syslog helyette.
- A felhasználónév a MultiFactorAuthSvc.log és más MFA-kiszolgáló más előfordulását naplófájlok tartozó hitelesítési kísérletek számítanak, üzemeltetési és más közölt MultiFactorAuthGdpr.exe exportálási vagy a Web Service SDK használatával GetUserGdpr.

## <a name="delete-data-from-mfa-server"></a>Adatok törlése az MFA-kiszolgáló

A parancssorból az MFA-kiszolgáló, az alábbi parancsot a telepítés megfelelően elérési útvonalának megváltoztatása `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe delete <username>` törli az összes MFA felhőalapú szolgáltatás gyűjtött információk a felhasználó számára.

- Az Exportálás szerepel adat törlődik, valós idejű, de akár teljesen el kell távolítani a működési vagy más adatok 30 napig is eltarthat.
- A rendszergazdák is használja a Web Service SDK DeleteUserGdpr műveletet lehetőségként összes MFA felhőalapú szolgáltatás adott felhasználó gyűjtött információk törlése vagy építhet be nagyobb jelentéskészítési megoldását.

## <a name="gather-data-from-nps-extension"></a>Gyűjthet velük adatokat az NPS-bővítményének

Használja a [a Microsoft adatvédelmi Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) az exportálási kérelem legyen.

- MFA-információk is szerepelnek az exportálás, ami órák vagy napok végrehajtásához is igénybe vehet.
- A felhasználónév a AzureMfa/AuthN/AuthNOptCh, AzureMfa/AuthZ/AuthZAdminCh és AzureMfa/AuthZ/AuthZOptCh eseménynaplók előfordulását működési és más, az Exportálás közölt minősülnek.

## <a name="delete-data-from-nps-extension"></a>Adatok NPS-bővítményének törlése

Használja a [a Microsoft adatvédelmi Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) használatával indítson egy fiók bezárása törli az összes MFA felhőalapú szolgáltatás gyűjtött információk a felhasználó számára.

- Az adatok teljesen el kell távolítani 30 napig is eltarthat.

## <a name="gather-data-from-windows-server-2016-azure-mfa-ad-fs-adapter"></a>Adatok gyűjtését a Windows Server 2016 az Azure MFA AD FS-Adapter

Használja a [a Microsoft adatvédelmi Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) az exportálási kérelem legyen. 

- MFA-információk is szerepelnek az exportálás, ami órák vagy napok végrehajtásához is igénybe vehet.
- A felhasználónevet (Ha engedélyezve van) az AD FS nyomkövetési és hibakeresési eseménynaplók előfordulását minősülnek, üzemeltetési és más, az Exportálás megadott adatokat.

## <a name="delete-data-from-windows-server-2016-azure-mfa-ad-fs-adapter"></a>Adatok törlése a Windows Server 2016 az Azure MFA AD FS-Adapter

Használja a [a Microsoft adatvédelmi Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) használatával indítson egy fiók bezárása törli az összes MFA felhőalapú szolgáltatás gyűjtött információk a felhasználó számára.

- Az adatok teljesen el kell távolítani 30 napig is eltarthat.

## <a name="gather-data-for-azure-mfa"></a>Adatgyűjtés Azure MFA-kiszolgáló

Használja a [a Microsoft adatvédelmi Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) az exportálási kérelem legyen.

- MFA-információk is szerepelnek az exportálás, ami órák vagy napok végrehajtásához is igénybe vehet.

## <a name="delete-data-for-azure-mfa"></a>Adatok törlése az Azure MFA-hoz

Használja a [a Microsoft adatvédelmi Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) használatával indítson egy fiók bezárása törli az összes MFA felhőalapú szolgáltatás gyűjtött információk a felhasználó számára.

- Az adatok teljesen el kell távolítani 30 napig is eltarthat.

## <a name="next-steps"></a>További lépések

[MFA-kiszolgáló reporting](howto-mfa-reporting.md)
