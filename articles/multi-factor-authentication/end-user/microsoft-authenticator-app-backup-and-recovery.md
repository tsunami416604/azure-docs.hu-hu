---
title: Biztonsági mentését és helyreállítását a Microsoft Authenticator - Azure információ |} Microsoft Docs
description: 'Útmutató: biztonsági mentését és helyreállítását a fiók hitelesítő adatait, a Microsoft Authenticator alkalmazással.'
services: multi-factor-authentication
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2018
ms.author: lizross
ms.reviewer: olhaun
ms.custom: end-user
ms.openlocfilehash: e25ccdad5285bfaa96f538aca415746942523d85
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
ms.locfileid: "33896265"
---
# <a name="backup-and-recover-account-credentials-with-the-microsoft-authenticator-app"></a>Biztonsági mentés és helyreállítás fiók hitelesítő adatait a Microsoft Authenticator alkalmazással
**Vonatkozik:**

- iOS-eszközök

A Microsoft Authenticator alkalmazás biztonsági mentést készít a fiók hitelesítő adatait és a kapcsolódó alkalmazások beállításai, például a fiókok sorrendjét a felhőbe. Biztonsági mentés, után is használhatja az alkalmazás helyreállításához az Ön új eszköz, adatai potenciálisan elkerülve az első zárolva out való vagy hozza létre a fiókokat.

>[!IMPORTANT]
> Szükség van egy személyes Microsoft-fiók- és iCloud-fiókjával minden egyes biztonsági másolatok tárolóját. Azonban, hogy tárolási helyen készíthet biztonsági mentést különböző fiókok. Például egy személyes fiók, egy iskolai e-mail címének és a Facebook, Google, például egy harmadik fél fiókot, és így tovább.<br><br>Csak a személyes és a 3. fél fiók hitelesítő adatait tárolja, amely magában foglalja a felhasználónév és a fiók ellenőrző kódja, amely a személyazonosság szükséges. Bármely egyéb információk is társítva vannak a fiókok, beleértve az e-maileket és a fájlok nem tároljuk. Azt is nem hozzárendelése vagy bármely olyan módon, vagy a termék vagy szolgáltatás fiókjának megosztani. És végül a rendszergazda nem jelenik meg bármelyik semmilyen információt.

## <a name="back-up-your-account-credentials"></a>Készítsen biztonsági másolatot a hitelesítő adatait
Készíthet biztonsági másolatot a hitelesítő adatait, mielőtt is kell rendelkeznie:

- Személyes [Microsoft-fiók](https://account.microsoft.com/account) helyreállítási fiókja nevében járhasson el.

- Egy [iCloud-fiókjával](https://www.icloud.com/) a tényleges tárolási helyét. 

Nincs szükség együtt fiókot is bejelentkezhet a biztonsági mentési adatait erősebb biztonságot nyújt.

**Biztonsági mentési felhő bekapcsolása**
-   IOS-eszközön, válassza ki a **beállítások**, jelölje be **biztonsági mentési**, és kapcsolja be **automatikus biztonsági mentés**.

    A fiók hitelesítő adatainak biztonsági mentése a iCloud-fiókjával.

    ![beállítások iOS képernyőjén, azt a helyet, az automatikus biztonsági mentés beállításait](./media/authenticator-app-backup-and-recovery/backup-and-recovery-turn-on.png)

## <a name="recover-your-account-credentials-on-your-new-device"></a>Az új eszköz a fiók hitelesítő adatainak helyreállítása
Helyre lehet állítani a fiók hitelesítő adatait az iCloud-fiókjával, ugyanazzal a fiókkal Microsoft helyreállítási beállítása során készített biztonsági másolatot az adatairól.

**Adatainak helyreállítása**
1.  IOS-eszközön, nyissa meg a Microsoft Authenticator alkalmazást, és válassza ki **helyreállításához** a képernyő alján.

    ![Microsoft Authenticator alkalmazást, hová Begin helyreállítási kattintson megjelenítése](./media/authenticator-app-backup-and-recovery/backup-and-recovery-begin-recovery.png)

2.  Jelentkezzen be a helyreállítási fiókját, a személyes Microsoft-fiók a biztonsági mentési folyamat során használt.

    A fiók hitelesítő adatait az új eszköz helyreállított.

A helyreállítás befejezése után Észreveheti, hogy a személyes Microsoft fiók ellenőrző kódok kezelésére a Microsoft Authenticator alkalmazás különböznek a régi és új telefonok. A kód különböznek minden eszközhöz tartozik a saját egyedi hitelesítő adat, de is érvényes, és a munkahelyi társított segítségével a bejelentkezés során.

## <a name="recover-additional-accounts-requiring-more-verification"></a>További ellenőrzést igénylő további fiókok helyreállítása
Leküldéses értesítések az a személyes, munkahelyi vagy iskolai fiókok használatakor kap egy történjen a képernyőn látható riasztás, amely szerint a további ellenőrzési meg kell adnia, mielőtt helyreállíthatja az adatokat. Mivel a leküldéses értesítések igényel, amely rendelkezik az adott eszközhöz kötődik, és soha nem a hálózaton keresztül küldött hitelesítő adat segítségével, meg kell használnia a személyazonossága igazolásához az eszközön a hitelesítő adat létrehozása előtt.

Személyes Microsoft-fiók egy másodlagos e-mail cím vagy telefonszám számát és a jelszó megadásával igazolhatja a személyazonosságát. A munkahelyi vagy iskolai fiókok a fiók szolgáltató által biztosított QR-kódot kell beolvasni.

**Adja meg a további ellenőrzési személyes fiókok számára**
1.  Az a **fiókok** képernyőjén a Microsoft Authenticator alkalmazást, a fiók helyreállításához mellett válassza ki a legördülő nyílra.

    ![Microsoft Authenticator alkalmazást, a társított legördülő nyíllal elérhető fiókok megjelenítő](./media/authenticator-app-backup-and-recovery/backup-and-recovery-arrow.png)

2.  Válassza ki **helyreállítása bejelentkezés**, írja be a jelszavát, és erősítse meg az e-mail címet vagy telefonszámot számot, a további ellenőrzési.

    ![A Microsoft Authenticator alkalmazásról, adja meg a bejelentkezési adatok](./media/authenticator-app-backup-and-recovery/backup-and-recovery-sign-in.png)

**Adja meg a további ellenőrzési munkahelyi vagy iskolai fiókok számára**
1.  Az a **fiókok** képernyőjén a Microsoft Authenticator alkalmazást, a fiók helyreállításához mellett válassza ki a legördülő nyílra.

    ![Microsoft Authenticator alkalmazást, a társított legördülő nyíllal elérhető fiókok megjelenítő](./media/authenticator-app-backup-and-recovery/backup-and-recovery-additonal-accts.png)

2.  Válassza ki **vizsgálata QR-kódot a helyreállításához**, majd vizsgálja a QR-kódot, a rendszergazdája által megadott

    ![Microsoft Authenticator alkalmazást, lehetővé téve a QR-kód beolvasása](./media/authenticator-app-backup-and-recovery/backup-and-recovery-scan-qr-code.png)

## <a name="troubleshooting-backup-and-recovery-problems"></a>Biztonsági mentési és helyreállítási problémák elhárítása
Néhány oka miért a biztonsági másolat nem áll rendelkezésre:

-   **Operációs rendszerek módosítása.** A biztonsági másolat a felhő tárolási lehetőség a telefon operációs rendszer, ami azt jelenti, hogy a biztonsági mentés nem érhető el, Android és IOS rendszerek közötti váltás tárolja. Ebben a helyzetben manuálisan létre kell hoznia az alkalmazáson belüli fiókját.

-   **Hálózati vagy a jelszó problémákat.** Győződjön meg arról, hogy Ön csatlakozik a hálózathoz, és az iCloud-fiókjával, az utolsó iPhone használt azonos AppleID használata be van jelentkezve.

-   **Véletlen törlés.** Akkor lehet, hogy az előző eszközről, vagy a felhőalapú társzolgáltatás fiókja felügyelete során a biztonsági mentési fiók törölve. Ebben a helyzetben manuálisan létre kell hoznia az alkalmazáson belüli fiókját.

-   **Meglévő Authenticator a Microsoft-fiók.** Ha már beállította a Microsoft Authenticator alkalmazást a fiókokat, az alkalmazás nem állítható helyre a biztonsági másolat fiókjait. Megakadályozásához helyreállítási biztosíthatja, hogy a fiókadatok nem felül elavult adatokat. Ebben a helyzetben el kell távolítani a meglévő fiók adatait a meglévő fiókokat, állítsa be a saját hitelesítőalkalmazása állíthatja helyre a biztonsági mentés előtt.

## <a name="next-steps"></a>További lépések
Most, hogy biztonsági mentése és a fiók hitelesítő adatait az új eszköz helyre, továbbra is használja a Microsoft Authenticator alkalmazást a személyazonosságát.

## <a name="related-topics"></a>Kapcsolódó témakörök
- [Ismerkedés a Microsoft Authenticator alkalmazással](microsoft-authenticator-app-how-to.md)  

- [Bejelentkezés telefonnal](microsoft-authenticator-app-phone-signin-faq.md)

- [A Microsoft hitelesítő alkalmazást – gyakori kérdések](microsoft-authenticator-app-faq.md)

- [Többtényezős hitelesítés](https://docs.microsoft.com/azure/multi-factor-authentication/)
