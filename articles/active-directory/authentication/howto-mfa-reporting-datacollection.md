---
title: Az Azure multi-factor Authentication felhasználói adatok gyűjtése
description: Milyen adatok segítségével a felhasználók hitelesítésére Azure multi-factor Authentication által?
services: multi-factor-authentication
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.service: multi-factor-authentication
ms.workload: identity
ms.topic: article
ms.date: 05/01/2018
ms.openlocfilehash: 2281a35d1616aa88b0c646fb96cb9f95c3272536
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264368"
---
# <a name="azure-multi-factor-authentication-user-data-collection"></a>Az Azure multi-factor Authentication felhasználói adatok gyűjtése

Ez a dokumentum azt ismerteti, hogyan abban az esetben, ha szeretné, hogy eltávolítsa a Azure multi-factor Authentication (MFA kiszolgáló) és az Azure MFA (felhőalapú) által gyűjtött felhasználói adatok kereséséhez.

[!INCLUDE [gdpr-hybrid-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="information-collected"></a>Összegyűjtött adatok

MFA-kiszolgáló, a hálózati házirend-kiszolgáló bővítmény és a Windows Server 2016 Azure MFA AD FS-Adapter gyűjthet, és 90 nap a következő információkat tárolja.

Hitelesítési kísérlet (használt jelentések és hibaelhárítás):

- Időbélyeg
- Felhasználónév
- Utónév
- Vezetéknév
- E-mail-cím
- Felhasználói csoport
- Hitelesítési módszer (telefonhívás, szöveges üzenet, mobilalkalmazás, OATH Token)
- Telefonhívás módját (Standard, PIN-kód)
- SMS-üzenet irányát (egyirányú, kétirányú)
- SMS-üzenet módját (OTP, OTP + PIN-kód)
- Mobilalkalmazás mód (Standard, PIN-kód)
- OATH-Token mód (Standard, PIN-kód)
- Hitelesítés típusa
- Alkalmazás neve
- Elsődleges hívás országhívószám
- Elsődleges hívás telefonszám
- Elsődleges hívás bővítmény
- Hitelesített elsődleges hívás
- Elsődleges hívás eredménye
- Biztonsági mentési hívás országhívószám
- Biztonsági mentési hívás telefonszám
- Biztonsági mentési hívás bővítmény
- Biztonsági mentési hívás hitelesítése
- Biztonsági mentési hívás eredménye
- A teljes hitelesítése
- Összesített eredmény
- Results (Eredmények)
- Hitelesített
- Eredmény
- IP-cím kezdeményezése
- Eszközök
- Eszköz jogkivonatát
- Az eszköz típusa
- Mobil verziója
- Operációs rendszer verziója
- Eredmény
- Értesítési használt ellenőrzése

Aktiválás (kísérlet egy fiókot a Microsoft Authenticator mobilalkalmazás aktiválása):
- Felhasználónév
- Fiók neve
- Időbélyeg
- Aktiválási kódot eredmény
- Sikeres aktiválása
- Hiba aktiválása
- Aktiválási állapota eredménye
- Eszköz neve
- Az eszköz típusa
- Alkalmazásverzió
- OATH jogkivonat engedélyezve

Blokkok (határozza meg a tiltott állapot és a jelentési):

- Timestamp típusú letiltása
- Felhasználónév letiltása
- Felhasználónév
- Országkód
- Telefonszám
- A telefonszám formázása
- Mellék
- Bővítmény törlése
- Letiltva
- Blokkolás oka
- Befejezési időbélyeg
- Befejezési OK
- Fiókzárolás
- Visszaélési riasztás
- Csalási riasztás nincs letiltva
- Nyelv

A Mellőzés (jelentéskészítéshez használt):

- Áthidaló időbélyeg
- Mellőzés (mp)
- Felhasználónév figyelmen kívül hagyása
- Felhasználónév
- Országkód
- Telefonszám
- A telefonszám formázása
- Mellék
- Bővítmény törlése
- Mellőzés oka
- Befejezési időbélyeg
- Befejezési OK
- Használt figyelmen kívül hagyása

A módosításokat (MFA kiszolgáló vagy egy aad-ben a felhasználói módosítások szinkronizálásához használt):

- Időbélyeg módosítása
- Felhasználónév
- Új országkód
- Új telefonszám
- Új mellék
- Új tartalék országhívószám
- Új tartalék telefonszám
- Új tartalék mellék
- Új PIN kód
- Meg kell változtatnia a PIN-kódot
- Régi eszköz jogkivonatát
- Új eszköztoken

## <a name="gather-data-from-mfa-server"></a>Adatokat gyűjt a multi-factor Authentication kiszolgáló

A következő folyamat MFA kiszolgáló 8.0-s vagy újabb verzió lehetővé teszi a rendszergazdák számára, hogy a felhasználók számára az összes adat exportálása:

- Jelentkezzen be az MFA kiszolgálóra, nyissa meg a **felhasználók** lapra, válassza ki az adott felhasználót, majd kattintson a **szerkesztése** gombra. Az egyes fülekre kattintva adja meg a felhasználó az aktuális többtényezős hitelesítési beállítások (Alt-PrtScn) képernyőfelvételek készítésének.
- A parancssorból az MFA-kiszolgáló, a következő parancsot a telepítés megfelelően elérési útjának módosítása `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe export <username>` eredményezett JSON formátumú fájlba.
- A rendszergazdák is használja a Web Service SDK GetUserGdpr műveletet beállításként minden MFA felhőalapú szolgáltatás adott felhasználó számára gyűjtött információ exportálása vagy beépítse nagyobb jelentéskészítési megoldását.
- Keresési `C:\Program Files\Multi-Factor Authentication Server\Logs\MultiFactorAuthSvc.log` és a biztonsági mentés "<username>" (idézőjelek között a keresés) található összes példányát a felhasználói rekord hozzáadni, illetve megváltozott.
   - Ezeket a rekordokat lehet korlátozott (, de nem szüntetni) jelének **"Felhasználói változások naplózása"** az az MFA kiszolgáló UX, a naplózás terület, a naplófájlok fület.
   - Ha syslog van konfigurálva, és **"Felhasználói változások naplózása"** be nem a multi-factor Authentication kiszolgáló UX, a naplózás terület, a Syslog fülre, majd a naplóbejegyzések gyűjthetők a syslog helyette.
- Más felhasználónév MultiFactorAuthSvc.log és egyéb MFA kiszolgáló előfordulását naplófájlok vonatkozó hitelesítési kísérletek minősülnek működési és ismétlődő MultiFactorAuthGdpr.exe exportálás vagy a webszolgáltatás SDK-val megadott adatokat GetUserGdpr.

## <a name="delete-data-from-mfa-server"></a>Adatok törléséhez a multi-factor Authentication kiszolgáló

A parancssorból az MFA-kiszolgáló, a következő parancsot a telepítés megfelelően elérési útjának módosítása `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe delete <username>` összes MFA felhőalapú szolgáltatás összegyűjtött adatokat a felhasználó törlése.

- Valós idejű bekerül az exportált adatok törlődik, de teljesen eltávolítani kívánt működési vagy ismétlődő adatok akár 30 napig is eltarthat.
- A rendszergazdák is használja a Web Service SDK DeleteUserGdpr műveletet lehetőség törli az összes MFA felhőalapú szolgáltatás adott felhasználó gyűjtött információk vagy beépítse nagyobb jelentéskészítési megoldását.

## <a name="gather-data-from-nps-extension"></a>Gyűjt adatokat a hálózati házirend-kiszolgáló bővítmény

Használja a [Microsoft adatvédelmi Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) használatával indítson egy lekérdezést az exportált.

- Többtényezős hitelesítés adatai megjelennek az exportálást, ami eltarthat órák vagy napok befejezéséhez.
- A felhasználónév a AzureMfa/AuthN/AuthNOptCh, AzureMfa/engedélyezési/AuthZAdminCh és AzureMfa/engedélyezési/AuthZOptCh eseménynaplók előfordulását működési és az exportált közölt információkban ismétlődő minősülnek.

## <a name="delete-data-from-nps-extension"></a>Adatok törléséhez a hálózati házirend-kiszolgáló bővítmény

Használja a [Microsoft adatvédelmi Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) használatával indítson egy lekérdezést a fiók bezárása összes MFA felhőalapú szolgáltatás összegyűjtött adatokat a felhasználó törlése.

- Az adatok teljesen eltávolítani kívánt akár 30 napig is eltarthat.

## <a name="gather-data-from-windows-server-2016-azure-mfa-ad-fs-adapter"></a>Gyűjt adatokat a Windows Server 2016 Azure MFA AD FS-Adapter

Használja a [Microsoft adatvédelmi Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) használatával indítson egy lekérdezést az exportált. 

- Többtényezős hitelesítés adatai megjelennek az exportálást, ami eltarthat órák vagy napok befejezéséhez.
- Az AD FS nyomkövetés/Debug eseménynaplókban (Ha engedélyezve van), a felhasználónév előfordulását működési és az exportált közölt információkban ismétlődő minősülnek.

## <a name="delete-data-from-windows-server-2016-azure-mfa-ad-fs-adapter"></a>Adatok törléséhez a Windows Server 2016 Azure MFA AD FS-Adapter

Használja a [Microsoft adatvédelmi Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) használatával indítson egy lekérdezést a fiók bezárása összes MFA felhőalapú szolgáltatás összegyűjtött adatokat a felhasználó törlése.

- Az adatok teljesen eltávolítani kívánt akár 30 napig is eltarthat.

## <a name="gather-data-for-azure-mfa"></a>Adatgyűjtés az Azure MFA számára

Használja a [Microsoft adatvédelmi Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) használatával indítson egy lekérdezést az exportált.

- Többtényezős hitelesítés adatai megjelennek az exportálást, ami eltarthat órák vagy napok befejezéséhez.

## <a name="delete-data-for-azure-mfa"></a>Adatok törlése az Azure MFA használatára

Használja a [Microsoft adatvédelmi Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) használatával indítson egy lekérdezést a fiók bezárása összes MFA felhőalapú szolgáltatás összegyűjtött adatokat a felhasználó törlése.

- Az adatok teljesen eltávolítani kívánt akár 30 napig is eltarthat.

## <a name="next-steps"></a>További lépések

[MFA kiszolgáló reporting](howto-mfa-reporting.md)
