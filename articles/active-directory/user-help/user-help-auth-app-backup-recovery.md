---
title: Biztonsági mentés és helyreállítás Microsoft Authenticator app-Azure Active Directorysal | Microsoft Docs
description: Megtudhatja, hogyan készíthet biztonsági mentést és helyreállíthatja a fiók hitelesítő adatait a Microsoft Authenticator alkalmazás használatával.
services: active-directory
author: eross-msft
manager: daveba
ms.subservice: user-help
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba99001159277d9d221910cafa4f5165ae2bd812
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68880756"
---
# <a name="backup-and-recover-account-credentials-with-the-microsoft-authenticator-app"></a>Fiók hitelesítő adatainak biztonsági mentése és helyreállítása a Microsoft Authenticator alkalmazással

**A következőkre vonatkozik:**

- iOS-eszközök

A Microsoft Authenticator alkalmazás biztonsági mentést készít a fiók hitelesítő adatairól és a kapcsolódó Alkalmazásbeállítások, például a fiókok sorrendjéről a felhőhöz. A biztonsági mentést követően az alkalmazással helyreállíthatja az adatokat egy új eszközön, így elkerülhető a zárolás vagy a fiókok ismételt létrehozása.

> [!IMPORTANT]
> Minden biztonsági mentési tárolási helyhez egy személyes Microsoft-fiók és egy iCloud-fiókra van szükség. A tárolási helyükön belül azonban több fiókról is készíthet biztonsági mentést. Létrehozhat például egy személyes fiókot, egy iskolai fiókot és egy harmadik féltől származó fiókot, például a Facebookot, a Google-t stb.
>
> A rendszer csak a személyes és harmadik féltől származó fiók hitelesítő adatait tárolja, beleértve a felhasználónevét és az identitásának igazolásához szükséges fiók-ellenőrző kódot. Nem tárolunk semmilyen más, a fiókjához társított információt, például az e-maileket és a fájlokat. A fiókokat semmilyen módon vagy más termékkel vagy szolgáltatással sem társítjuk és nem osztjuk meg. Végül pedig a rendszergazda nem kap semmilyen információt ezekről a fiókokról.

## <a name="back-up-your-account-credentials"></a>A fiók hitelesítő adatainak biztonsági mentése

A hitelesítő adatok biztonsági mentése előtt a következőket kell tartalmaznia:

- Személyes [Microsoft-fiók](https://account.microsoft.com/account) , amely helyreállítási fiókként működik.

- [ICloud-Fiók](https://www.icloud.com/) a tényleges tárolási helyhez.

Ha mindkét fiókba be kell jelentkeznie, a biztonsági mentési adatok biztonsága nagyobb biztonságot nyújt.

### <a name="to-turn-on-cloud-backup"></a>A felhő biztonsági mentésének bekapcsolása

- Az iOS-eszközön válassza a **Beállítások**, majd a **biztonsági mentés**lehetőséget, majd kapcsolja be az **iCloud biztonsági mentést**.

    A fiók hitelesítő adatai biztonsági mentést kapnak az iCloud-fiókjába.

    ![iOS-beállítások képernyő, amely az iCloud biztonsági mentési beállításainak helyét jeleníti meg](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on.png)

## <a name="recover-your-account-credentials-on-your-new-device"></a>A fiók hitelesítő adatainak helyreállítása az új eszközön

A fiók hitelesítő adatait az iCloud-fiókjából is helyreállíthatja, ha ugyanazt a Microsoft helyreállítási fiókot használja, amelyet az adatok biztonsági mentésekor beállított.

### <a name="to-recover-your-information"></a>Az adatok helyreállítása

1. Az iOS-eszközön nyissa meg a Microsoft Authenticator alkalmazást, és kattintson a képernyő alján található **helyreállítás indítása** elemre.

    ![Microsoft Authenticator alkalmazás, amely megmutatja, hová kattintson a helyreállítás megkezdése elemre.](./media/user-help-auth-app-backup-recovery/backup-and-recovery-begin-recovery.png)

2. Jelentkezzen be a helyreállítási fiókjába ugyanazzal a személyes Microsoft-fiók használatával, amelyet a biztonsági mentési folyamat során használt.

    A fiók hitelesítő adatait az új eszközre állítja vissza.

A helyreállítás befejezése után észreveheti, hogy a Microsoft Authenticator alkalmazásban a személyes Microsoft-fiók ellenőrző kódok különböznek a régi és az új telefonok között. A kódok különböznek, mert minden eszköz saját egyedi hitelesítő adatokkal rendelkezik, de mindkettő érvényes és működik, miközben a társított telefon használatával jelentkezik be.

## <a name="recover-additional-accounts-requiring-more-verification"></a>További ellenőrzéseket igénylő további fiókok helyreállítása

Ha leküldéses értesítéseket használ a személyes, munkahelyi vagy iskolai fiókokkal, akkor egy képernyőn megjelenő riasztás jelenik meg, amely szerint további ellenőrzéseket kell megadnia az adatok helyreállítása előtt. Mivel a leküldéses értesítések olyan hitelesítő adatokat igényelnek, amely az adott eszközhöz van kötve, és soha nem küldi el a hálózaton keresztül, a hitelesítő adatoknak az eszközön való létrehozása előtt bizonyítania kell az identitását.

Személyes Microsoft-fiókok esetén a jelszó megadásával, valamint egy másodlagos e-mail-cím vagy telefonszám használatával igazolhatja a személyazonosságát. Munkahelyi vagy iskolai fiókok esetében a fiók szolgáltatója által megadott QR-kódot kell beolvasnia.

### <a name="to-provide-additional-verification-for-personal-accounts"></a>A személyes fiókok további ellenőrzésének biztosítása

1. A Microsoft Authenticator alkalmazás **fiókok** képernyőjén válassza ki a helyreállítani kívánt fiók melletti legördülő nyilat.

    ![Microsoft Authenticator alkalmazás, amely az elérhető fiókokat jeleníti meg a hozzájuk tartozó legördülő nyilakkal](./media/user-help-auth-app-backup-recovery/backup-and-recovery-arrow.png)

2. A helyreállításhoz válassza a **Bejelentkezés**lehetőséget, írja be a jelszót, majd erősítse meg az e-mail-címet vagy a telefonszámot további ellenőrzésként.

    ![Microsoft Authenticator alkalmazás, amely lehetővé teszi a bejelentkezési adatok megadását](./media/user-help-auth-app-backup-recovery/backup-and-recovery-sign-in.png)

### <a name="to-provide-additional-verification-for-work-or-school-accounts"></a>További ellenőrzés biztosítása munkahelyi vagy iskolai fiókokhoz

1. A Microsoft Authenticator alkalmazás **fiókok** képernyőjén válassza ki a helyreállítani kívánt fiók melletti legördülő nyilat.

    ![Microsoft Authenticator alkalmazás, amely az elérhető fiókokat jeleníti meg a hozzájuk tartozó legördülő nyilakkal](./media/user-help-auth-app-backup-recovery/backup-and-recovery-additional-accts.png)

2. Jelölje be a **QR-kód bevizsgálása a helyreállításhoz**lehetőséget, majd vizsgálja meg a QR-kódot.

    ![Microsoft Authenticator alkalmazás, amely lehetővé teszi a QR-kód vizsgálatát](./media/user-help-auth-app-backup-recovery/backup-and-recovery-scan-qr-code.png)

    >[!NOTE]
    >Ha első alkalommal állítja be a Microsoft Authenticator alkalmazást, megkérdezheti, hogy engedélyezi-e az alkalmazásnak a kamera (iOS) elérését, vagy hogy az alkalmazás képeket készítsen, és videót rögzítsen (Android). Az **Engedélyezés lehetőséget** kell választania, hogy a hitelesítő alkalmazás hozzáférhessen a kamerához, hogy a következő lépésben képet készítsen a QR-kódból. Ha nem engedélyezi a kamerát, akkor továbbra is beállíthatja a hitelesítő alkalmazást, de a kód adatait manuálisan kell hozzáadnia. A kód manuális hozzáadásával kapcsolatos további információkért lásd: [fiók manuális hozzáadása az alkalmazáshoz](user-help-auth-app-add-account-manual.md).
    >
    >A QR-kódok beszerzésével kapcsolatos további információkért lásd: Ismerkedés [a Microsoft Authenticator alkalmazással](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install) vagy a [biztonsági adatok beállítása a hitelesítő alkalmazás használatára](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-auth-app), attól függően, hogy a rendszergazda bekapcsolta-e a biztonsági adatokat.

## <a name="troubleshooting-backup-and-recovery-problems"></a>Biztonsági mentési és helyreállítási problémák elhárítása

Előfordulhat, hogy a biztonsági mentésnek néhány oka van:

- **Operációs rendszerek módosítása.** A biztonsági mentést a telefon operációs rendszere által biztosított felhőalapú tárolási lehetőség tárolja, ami azt jelenti, hogy a biztonsági mentés nem érhető el, ha az Android és az iOS között vált. Ebben az esetben manuálisan kell újból létrehoznia a fiókját az alkalmazáson belül.

- **Hálózati vagy jelszavas problémák.** Győződjön meg arról, hogy csatlakozik egy hálózathoz, és jelentkezzen be az iCloud-fiókjába az utolsó iPhone-on használt AppleID használatával.

- **Véletlen törlés.** Lehetséges, hogy törölte a biztonsági mentési fiókot az előző eszközről, vagy a felhőalapú Storage-fiókját kezeli. Ebben az esetben manuálisan kell újból létrehoznia a fiókját az alkalmazáson belül.

- **Meglévő Microsoft Authenticator fiókok.** Ha már beállított fiókokat a Microsoft Authenticator alkalmazásban, az alkalmazás nem fogja tudni helyreállítani a biztonsági másolatban szereplő fiókokat. A helyreállítás megakadályozása révén biztosíthatja, hogy a fiók adatai ne legyenek felülírva az elavult információkkal. Ebben az esetben a biztonsági mentés helyreállítása előtt el kell távolítania a meglévő fiók adatait a hitelesítő alkalmazásban beállított meglévő fiókokról.

## <a name="next-steps"></a>További lépések

Most, hogy biztonsági mentést készített, és visszaállította a fiókja hitelesítő adatait az új eszközre, továbbra is használhatja a Microsoft Authenticator alkalmazást a személyazonosságának ellenőrzéséhez. További információ: bejelentkezés a [fiókba a Microsoft Authenticator alkalmazás használatával](user-help-sign-in.md).

## <a name="related-topics"></a>Kapcsolódó témakörök

- [Mi a Microsoft Authenticator alkalmazás?](user-help-auth-app-overview.md)

- [A Microsoft Authenticator alkalmazással kapcsolatos gyakori kérdések](user-help-auth-app-faq.md)

- [Többtényezős hitelesítés](https://docs.microsoft.com/azure/multi-factor-authentication/)
