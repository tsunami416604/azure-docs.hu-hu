---
title: Biztonsági mentése és helyreállítása a Microsoft Authenticator alkalmazás – Azure ad-ben |} A Microsoft Docs
description: Megtudhatja, hogyan biztonsági mentését és helyreállítását a fiók hitelesítő adatait, a Microsoft Authenticator alkalmazás használatával.
services: multi-factor-authentication
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/28/2018
ms.author: lizross
ms.reviewer: olhaun
ms.custom: end-user
ms.openlocfilehash: a9c950ecafd2eb5f3aed1bee3707f57be6ec3b62
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39060102"
---
# <a name="backup-and-recover-account-credentials-with-the-microsoft-authenticator-app"></a>Biztonsági mentés és helyreállítás fiók hitelesítő adatait a Microsoft Authenticator alkalmazással

**A következőkre vonatkozik:**

- iOS-eszközök

A Microsoft Authenticator alkalmazás biztonsági másolatot készít a fiók hitelesítő adatait, és a kapcsolódó alkalmazás beállításait, például a fiókok sorrendje a felhőbe. Biztonsági mentés, után használhatja az alkalmazás használatával állítsa helyre a információkat egy új eszközön, potenciálisan elkerülve az első zárolva ki vagy hozza létre újra a fiókok kellene.

>[!IMPORTANT]
> Szükség van egy személyes Microsoft-fiók és a egy iCloud-fiókjával minden egyes biztonsági mentési tárhelyet. De a tárolási helyen készíthet biztonsági mentést különböző fiókok. Ha például egy személyes fiók, egy iskolai fiókot és egy külső fiók például Facebook, Google, és így tovább.<br><br>Csak a személyes, mind a 3. fél fiók hitelesítő adatai találhatók, amely tartalmazza a felhasználónevet és a fiók a személyazonossága igazolásához szükséges ellenőrző kódot. A fiókok, beleértve az e-mailek vagy fájlok társított minden olyan információt nem tároljuk. Azt is nem hozzárendelése vagy megoszthatja a fiókok bármilyen módon vagy a termék vagy szolgáltatás. És végül a rendszergazda nem ezeket a fiókokat bármelyikét semmilyen információt.

## <a name="back-up-your-account-credentials"></a>Készítsen biztonsági másolatot a hitelesítő adatait
Készíthet biztonsági másolatot a hitelesítő adatokat, mielőtt is kell rendelkeznie:

- Egy személyes [Microsoft-fiók](https://account.microsoft.com/account) segítségével a helyreállítási fiókba.

- Egy [iCloud-fiókjával](https://www.icloud.com/) tényleges tárolási helyét. 

A biztonsági mentési adatok erősebb biztonságot igénylő, hogy jelentkezzen be a fiókot is együtt biztosít.

**Felhőalapú biztonsági mentési bekapcsolása**
-   Válassza ki az iOS-eszközön, **beállítások**válassza **biztonsági mentési**, majd kapcsolja be **automatikus biztonsági mentés**.

    A fiók hitelesítő adatainak biztonsági mentése az iCloud-fiókban.

    ![iOS-es beállítások képernyő helyét, az automatikus biztonsági mentés beállításait](./media/microsoft-authenticator-app-backup-and-recovery/backup-and-recovery-turn-on.png)

## <a name="recover-your-account-credentials-on-your-new-device"></a>A fiók hitelesítő adatait az új eszköz helyreállítása
A fiók hitelesítő adatait az iCloud-fiók segítségével helyreállítható Microsoft recovery-fiók beállítása során, amelyről biztonsági másolatot az adatait.

**Az adatok helyreállítása**
1.  Az iOS-eszközön nyissa meg a Microsoft Authenticator alkalmazást, és válassza ki **helyreállítás megkezdése** a képernyő alján.

    ![A Microsoft Authenticator alkalmazás képe, amelyen hol kezdje helyreállítási kattintson](./media/microsoft-authenticator-app-backup-and-recovery/backup-and-recovery-begin-recovery.png)

2.  Jelentkezzen be a helyreállítási fiókba, a személyes Microsoft-fiók a biztonsági mentési folyamat során használt használatával.

    A fiók hitelesítő adatait az új eszköz állíthatók vissza.

Miután elvégezte a helyreállítás, Észreveheti, hogy a személyes Microsoft fiók ellenőrző kódok kezelésére a Microsoft Authenticator alkalmazásban különböznek a régi és új telefonokon. A kódok eltérőek, mivel minden eszköz rendelkezik a saját egyedi hitelesítő adatokat, de mindkettő olyan érvényes, és a munkahelyi használata a kapcsolódó telefonos bejelentkezés során.

## <a name="recover-additional-accounts-requiring-more-verification"></a>További ellenőrzést igénylő további fiókok helyreállítása
Ha leküldéses értesítések küldése a személyes, munkahelyi vagy iskolai fiókokat használ, kap egy képernyőn látható riasztás, amely arról tájékoztat, akkor az adatok helyreállítása előtt meg kell adnia további ellenőrzés. Mivel a leküldéses értesítések igényel, amely rendelkezik az adott eszköz kapcsolódik, és soha ne a hálózaton keresztül továbbított hitelesítő adatok használatával, az eszközön a hitelesítő adat létrehozása előtt igazolnia kell személyazonosságát.

Személyes Microsoft-fiókok igazolja az identitását a jelszót és a egy másodlagos e-mail cím vagy telefonszám szám megadásával. Munkahelyi vagy iskolai fiókok esetében a fiók szolgáltatója által biztosított QR-kódot kell vizsgálnia.

**A további ellenőrzési személyes fiókok**
1.  Az a **fiókok** képernyőjén a Microsoft Authenticator alkalmazást, válassza ki a legördülő nyílra a helyreállítani kívánt fiók mellett.

    ![A Microsoft Authenticator alkalmazás képe, amelyen azok társított lefelé mutató nyíllal elérhető fiókok](./media/microsoft-authenticator-app-backup-and-recovery/backup-and-recovery-arrow.png)

2.  Válassza ki **helyreállítás bejelentkezéssel**, írja be a jelszót és erősítse meg az e-mail címet vagy telefonszámot a számot további ellenőrzés.

    ![A Microsoft Authenticator alkalmazás képe, lehetővé teszi, hogy adja meg a bejelentkezési adatok](./media/microsoft-authenticator-app-backup-and-recovery/backup-and-recovery-sign-in.png)

**A további ellenőrzési munkahelyi vagy iskolai fiókok esetében**
1.  Az a **fiókok** képernyőjén a Microsoft Authenticator alkalmazást, válassza ki a legördülő nyílra a helyreállítani kívánt fiók mellett.

    ![A Microsoft Authenticator alkalmazás képe, amelyen azok társított lefelé mutató nyíllal elérhető fiókok](./media/microsoft-authenticator-app-backup-and-recovery/backup-and-recovery-additonal-accts.png)

2.  Válassza ki **vizsgálata QR-kód helyreállítása**, majd vizsgálja meg a QR-kódot a rendszergazda által megadott

    ![A Microsoft Authenticator alkalmazás, amely lehetővé teszi a QR-kód](./media/microsoft-authenticator-app-backup-and-recovery/backup-and-recovery-scan-qr-code.png)

    >[!NOTE]
    >QR-kód beolvasásával kapcsolatos további információkért tekintse meg a [Ismerkedés a Microsoft Authenticator alkalmazás](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/end-user/microsoft-authenticator-app-how-to) cikk.

## <a name="troubleshooting-backup-and-recovery-problems"></a>Biztonsági mentési és helyreállítási problémák hibaelhárítása
Néhány oka miért a biztonsági mentés nem feltétlenül érhető el:

-   **Operációs rendszerek módosítása.** A felhőalapú tárolási megoldás az azt jelenti, hogy a biztonsági mentés nem érhető el, ha az Android és IOS rendszerek közötti váltás a telefon operációs rendszer által biztosított tárolja a biztonsági mentés. Ebben a helyzetben manuálisan létre kell hoznia a fiókot az alkalmazáson belül.

-   **Hálózati vagy a jelszó problémákat.** Győződjön meg arról, hogy Ön csatlakozik a hálózathoz, és az iCloud-fiókban, használja az ugyanazon Apple-azonosító az utolsó iPhone-on használt be van jelentkezve.

-   **Véletlen törlés.** Akkor lehet, hogy az előző eszközről, vagy a felhős társzolgáltatás fiókjába felügyelete során a biztonsági mentési fiók törölve. Ebben a helyzetben manuálisan létre kell hoznia a fiókot az alkalmazáson belül.

-   **A Microsoft Authenticator meglévő fiókok.** Ha már beállította a Microsoft Authenticator alkalmazást a fiókok, az alkalmazás nem állítható helyre a biztonsági másolat-fiókok. Amely megakadályozza, hogy recovery segítségével, győződjön meg arról, hogy a saját fiókja adataira felül nem elavult adatokkal. Ebben az esetben el kell távolítania minden olyan meglévő fiók adatait a meglévő fiókokat, akkor a biztonsági mentés helyreállítása előtt az Authenticator alkalmazás beállítása a.

## <a name="next-steps"></a>További lépések
Most, hogy készíteni és helyreállítani a fiók hitelesítő adatait az új eszközt, továbbra is a Microsoft Authenticator alkalmazás használatához a személyazonosságát.

## <a name="related-topics"></a>Kapcsolódó témakörök
- [A Microsoft Authenticator alkalmazás használatának első lépései](microsoft-authenticator-app-how-to.md)  

- [Bejelentkezés telefonnal](microsoft-authenticator-app-phone-signin-faq.md)

- [A Microsoft Authenticator alkalmazás – gyakori kérdések](microsoft-authenticator-app-faq.md)

- [Többtényezős hitelesítés](https://docs.microsoft.com/azure/multi-factor-authentication/)
