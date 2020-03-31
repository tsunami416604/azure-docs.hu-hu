---
title: Fiókok biztonsági és helyreállítási biztonsági kapcsolata és helyreállítása a Microsoft Authenticator alkalmazással – Azure AD
description: A Microsoft Authenticator alkalmazással megtudhatja, hogyan készülhet biztonsági másolatot a biztonsági másolatoról, és hogyan állíthatja vissza a biztonsági másolatot.
services: active-directory
author: curtand
manager: daveba
ms.subservice: user-help
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 2ac761355fc528fe3e4b4b5f05ee0a6e317ff99a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78297980"
---
# <a name="back-up-and-recover-account-credentials-using-the-microsoft-authenticator-app"></a>A fiók hitelesítő adatainak biztonsági és helyreállítási biztonsági szolgáltatása a Microsoft Authenticator alkalmazással

**A következőkre vonatkozik:**

- iOS-eszközök, 5.7.0-s vagy újabb verzióval

- 6.6.0-s vagy újabb verziójú Android-eszközök

A Microsoft Authenticator alkalmazás biztonsági másolatot készít a fiók hitelesítő adatairól és a kapcsolódó alkalmazásbeállításokról, például a fiókok sorrendjéről a felhőbe. A biztonsági mentés után az alkalmazás segítségével is helyreállíthatja az adatokat egy új eszközön, így elkerülheti a kizárt adatokat, vagy újra létre veheti a fiókokat.

Minden biztonsági mentési tárolási helyhez egy személyes Microsoft-fiók szükséges, míg az iOS-hez iCloud-fiók szükséges. Az adott helyen több fiókot is tárolhat. Lehet például személyes, munkahelyi vagy iskolai fiókja, valamint személyes, nem Microsoft-fiókja, például a Facebook, a Google és így tovább.

> [!IMPORTANT]
> A rendszer csak az Ön személyes és harmadik féltől származó fiókjának hitelesítő adatait tárolja, beleértve a felhasználónevét és a személyazonossága igazolásához szükséges fiókellenőrző kódot. A fiókjaihoz kapcsolódó egyéb adatokat nem tárolunk, beleértve az e-maileket és a fájlokat. A fiókjait semmilyen módon vagy más termékkel vagy szolgáltatással nem társítjuk és nem osztjuk meg. És végül, az informatikai rendszergazda nem kap semmilyen információt ezekről a fiókokról.

## <a name="back-up-your-account-credentials"></a>A fiók hitelesítő adatainak biztonsági és biztonsági és biztonsági másolatot kell kapnia

A hitelesítő adatok biztonsági leéséhez a következőkre van szüksége:

- Személyes [Microsoft-fiók,](https://account.microsoft.com/account) amely helyreállítási fiókként szolgál.

- **Csak iOS esetén** [iCloud-fiókkal](https://www.icloud.com/) kell rendelkeznie a tényleges tárolási helyhez.

### <a name="to-turn-on-cloud-backup-for-ios-devices"></a>A felhőalapú biztonsági mentés bekapcsolása iOS-eszközökön

- Az iOS-készüléken válassza a **Beállítások**lehetőséget, válassza a **Biztonsági mentés**lehetőséget, majd kapcsolja be az **iCloud biztonsági mentést**.

    A fiók hitelesítő adatairól biztonsági másolatot készít az iCloud-fiókjában.

    ![iOS-beállítások képernyő, amely az iCloud biztonsági mentési beállításainak helyét mutatja](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on.png)

### <a name="to-turn-on-cloud-backup-for-android-devices"></a>A felhőalapú biztonsági mentés bekapcsolása Android-eszközökön

- Android-eszközén válassza a **Beállítások**lehetőséget, válassza a **Biztonsági mentés**lehetőséget, majd kapcsolja be a **felhőalapú biztonsági mentést.**

    A fiók hitelesítő adatairól biztonsági másolatot készít a felhőalapú fiókjában.

    ![Android beállítások képernyő, amely megmutatja a helyét a biztonsági mentési beállítások](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on-android.png)

## <a name="recover-your-account-credentials-on-your-new-device"></a>A fiók hitelesítő adatainak helyreállítása az új eszközön

A fiók hitelesítő adatait a felhőfiókból állíthatja helyre, de először meg kell győződnie arról, hogy a visszahegedt fiók nem létezik a Microsoft Authenticator alkalmazásban. Ha például a személyes Microsoft-fiókját állítja helyre, meg kell győződnie arról, hogy nincs már beállítva személyes Microsoft-fiókja a hitelesítő alkalmazásban. Ez az ellenőrzés fontos, így biztosak lehetünk abban, hogy nem véletlenül írunk felül vagy töröljenek egy meglévő fiókot.

### <a name="to-recover-your-information"></a>Az adatok helyreállítása

1. Mobileszközén nyissa meg a Microsoft Authenticator alkalmazást, és válassza a **Helyreállítás megkezdése lehetőséget** a képernyő alján.

    ![Microsoft Authenticator alkalmazás, amely megmutatja, hogy hová kattintson a Helyreállítás megkezdése gombra](./media/user-help-auth-app-backup-recovery/backup-and-recovery-begin-recovery.png)

2. Jelentkezzen be a helyreállítási fiókba, ugyanazt a személyes Microsoft-fiókot használva, amelyet a biztonsági mentési folyamat során használt.

    A fiók hitelesítő adatait a készülék helyreáll az új eszközön.

A helyreállítás befejezése után észreveheti, hogy a Microsoft Hitelesítő alkalmazásban lévő személyes Microsoft-fiók-ellenőrző kódok különböznek a régi és az új telefonok között. A kódok különbözőek, mivel minden eszköz saját egyedi hitelesítő adatokkal rendelkezik, de mindkettő érvényes és működik, miközben bejelentkezik a társított telefonon keresztül.

## <a name="recover-additional-accounts-requiring-more-verification"></a>További, további ellenőrzést igénylő fiókok helyreállítása

Ha leküldéses értesítéseket használ személyes, munkahelyi vagy iskolai fiókjaihoz, a képernyőn megjelenő értesítés ben megjelenik, amely szerint további ellenőrzést kell biztosítania az adatok helyreállítása előtt. Mivel a leküldéses értesítések hez az adott eszközhöz kapcsolódó hitelesítő adatokat kell használni, és soha nem kell a hálózaton keresztül elküldeni, igazolnia kell a személyazonosságát, mielőtt a hitelesítő adatok létrejönnek az eszközön.

Személyes Microsoft-fiókok esetén a jelszavát egy másodlagos e-mail-cím vagy telefonszám megadásával bizonyíthatja. Munkahelyi vagy iskolai fiókok esetén be kell szoknunk a fiókszolgáltatója által megadott QR-kódot.

### <a name="to-provide-additional-verification-for-personal-accounts"></a>A személyes fiókok további ellenőrzése

1. A Microsoft Authenticator alkalmazás **Fiókok** képernyőjén válassza ki a visszaállítani kívánt fiókot. Android-eszközön válassza a helyreállítani kívánt fiók melletti nyilat.

    ![Microsoft Authenticator alkalmazás, amely a rendelkezésre álló fiókokat a hozzájuk tartozó legördülő nyilakkal jeleníti meg](./media/user-help-auth-app-backup-recovery/backup-and-recovery-arrow.png)

    IOS-eszközön koppintson a visszaállítani kívánt fiókra a fiók teljes képernyős nézetének megnyitásához.

    ![Microsoft Authenticator alkalmazás, amely a rendelkezésre álló fiókokat a hozzájuk tartozó legördülő nyilakkal jeleníti meg](./media/user-help-auth-app-backup-recovery/ios-select-account-to-recover.png)

2. Jelentkezzen be a fiók helyreállításához. Android-eszközön válassza a Bejelentkezés lehetőséget **a helyreállításhoz.**

    ![Microsoft Hitelesítő alkalmazás a bejelentkezési adatok megadásához Android-eszközön](./media/user-help-auth-app-backup-recovery/backup-and-recovery-sign-in.png)

    IOS-eszközön koppintson a visszaállítani kívánt fiókcsempére, majd a bejelentkezés és a helyreállítás lehetőségére. Ezután írja be a jelszavát, majd erősítse meg e-mail címét vagy telefonszámát további verification.unt.

    ![Microsoft Hitelesítő alkalmazás a bejelentkezési adatok megadásához iOS rendszeren](./media/user-help-auth-app-backup-recovery/ios-sign-in-to-recover.png)

### <a name="to-provide-additional-verification-for-work-or-school-accounts"></a>További ellenőrzés biztosítása a munkahelyi vagy iskolai fiókokesetében

1. Jelentkezzen be a fiók helyreállításához. Android-eszközön válassza a Bejelentkezés lehetőséget **a helyreállításhoz.**

    ![A Microsoft Authenticator alkalmazás munkahelyi vagy iskolai fiókot állít helyre Androidon](./media/user-help-auth-app-backup-recovery/backup-and-recovery-additional-accts.png)

    IOS-eszközön koppintson a visszaállítani kívánt fiókra a fiók teljes képernyős nézetének megnyitásához.

    ![A Microsoft Authenticator alkalmazás munkahelyi vagy iskolai fiók helyreállítása iOS rendszeren](./media/user-help-auth-app-backup-recovery/ios-recover-work-or-school-account.png)

2. A QR-kód segítségével helyreállíthatja a fiókot. A **helyreállítandó QR-kód becsúsztatása**lehetőséget, majd a QR-kód bejelölését.

    Androidon:

    ![Microsoft Authenticator alkalmazás Androidon, amely lehetővé teszi a QR-kód beszkaporítását](./media/user-help-auth-app-backup-recovery/backup-and-recovery-scan-qr-code.png)

    iOS rendszeren:

    ![Microsoft Authenticator alkalmazás iOS rendszeren, amely lehetővé teszi a QR-kód beszkapora](./media/user-help-auth-app-backup-recovery/ios-scan-qr-code.png)

    >[!NOTE]
    >A QR-kódokról és azok bekéréséről az [Első lépések a Microsoft Authenticator alkalmazással](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install) vagy a Biztonsági adatok beállítása a hitelesítő alkalmazás [használatához](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-auth-app)című témakörben talál további információt attól függően, hogy a rendszergazda bekapcsolta-e a biztonsági adatokat.
    >
    >Ha ez az első alkalom, hogy beállítja a Microsoft Authenticator alkalmazást, előfordulhat, hogy egy kérdés jelenik meg arról, hogy engedélyezi-e az alkalmazásnak a kamera (iOS) elérését, vagy hogy az alkalmazás képeket készíthessen és videót (Android) rögzíthessen. Az **Engedélyezés** lehetőséget kell választania, hogy a hitelesítő alkalmazás hozzáférhessen a kamerához, hogy a következő lépésben képet ad a QR-kódról. Ha nem engedélyezi a kamerát, továbbra is beállíthatja a hitelesítő alkalmazást, de manuálisan kell megadnia a kódadatokat. A kód manuális hozzáadásáról a [Fiók hozzáadása az alkalmazáshoz című témakörben](user-help-auth-app-add-account-manual.md)talál további információt.

## <a name="troubleshoot-backup-and-recovery-problems"></a>Biztonsági mentési és helyreállítási problémák elhárítása

Több oka is lehet annak, hogy a biztonsági mentés nem érhető el

- **Operációs rendszerek módosítása**: A biztonsági mentés az iCloud for iOS és a Microsoft androidos felhőalapú tárhelyszolgáltatójában található. Ez azt jelenti, hogy a biztonsági mentés nem érhető el, ha androidos és iOS-eszközök között vált. Ha megcsinálja a váltást, manuálisan kell újra létrehoznia a fiókokat a Microsoft Authenticator alkalmazásban.

- **Hálózati problémák:** Ha hálózati problémákat tapasztal, ellenőrizze, hogy csatlakozik-e a hálózathoz, és megfelelően jelentkezett-e be a fiókjába.

- **Fiókproblémák:** Ha fiókkal kapcsolatos problémákat tapasztal, ellenőrizze, hogy megfelelően jelentkezett-e be a fiókjába. IOS esetén ez azt jelenti, hogy az iPhone-nal azonos AppleID-fiókkal kell bejelentkeznie az iCloudba.

- **Véletlen törlés:** Lehetséges, hogy törölte a biztonsági mentési fiókját az előző eszközéről, vagy a felhőalapú tárfiók kezelése közben. Ebben az esetben manuálisan kell újra létrehoznia a fiókot az alkalmazáson belül.

- **Meglévő Microsoft Hitelesítő-fiókok:** Ha már beállított fiókokat a Microsoft Authenticator alkalmazásban, az alkalmazás nem tudja visszaállítani a biztonsági másolatot. A helyreállítás megakadályozása segít annak biztosításában, hogy a fiókadatait ne írja felül az elavult adatok. Ebben az esetben a biztonsági mentés helyreállítása előtt el kell távolítania a meglévő fiókadatokat a Hitelesítő alkalmazásban beállított meglévő fiókokból.

- **A biztonsági mentés elavult:** Ha a biztonsági mentési adatok elavultak, előfordulhat, hogy az adatok frissítésére kérik, ha ismét bejelentkezik a Microsoft Recovery-fiókba. A helyreállítási fiók az a személyes Microsoft-fiók, amelyet eredetileg a biztonsági mentés tárolására használt. Ha szükség van egy bejelentkezésre, a menüben vagy a műveletsávban egy piros pont jelenik meg, vagy egy felkiáltójel jelenik meg, amely arra kéri, hogy jelentkezzen be a biztonsági mentésből történő visszaállítás befejezéséhez. Miután kiválasztotta az ikont, a rendszer kéri, hogy jelentkezzen be újra az adatok frissítéséhez.

## <a name="next-steps"></a>További lépések

Most, hogy biztonsági másolatot tett a rról, és visszaszerezte a fiók hitelesítő adatait az új eszközre, továbbra is használhatja a Microsoft Authenticator alkalmazást személyazonosságának ellenőrzésére. További információt a [Bejelentkezés a fiókokba a Microsoft Authenticator alkalmazással című témakörben](user-help-sign-in.md)talál.

## <a name="related-articles"></a>Kapcsolódó cikkek

- [Mi a Microsoft Authenticator alkalmazás?](user-help-auth-app-overview.md)

- [A Microsoft Authenticator alkalmazással kapcsolatos gyakori kérdések](user-help-auth-app-faq.md)

- [Többtényezős hitelesítés](https://docs.microsoft.com/azure/multi-factor-authentication/)
