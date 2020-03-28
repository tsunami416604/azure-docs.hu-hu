---
title: 'Oktatóanyag: PhS beállítása az AD FS biztonsági mentéseként az Azure AD Connectben | Microsoft dokumentumok'
description: Bemutatja, hogyan kapcsolhatja be a jelszókivonat-szinkronizálást biztonsági másolatként és az AD FS esetében.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e5ad7badfa44a006fd7e71d3b0e42ee95ac698d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "64919008"
---
# <a name="tutorial--setting-up-phs-as-backup-for-ad-fs-in-azure-ad-connect"></a>Oktatóanyag: PhS beállítása az AD FS biztonsági mentéseként az Azure AD Connectben

A következő oktatóanyag végigvezeti a jelszókivonat-szinkronizálás beállításán biztonsági másolatként és feladatátvételen az AD FS-hez.  Ez a dokumentum azt is bemutatja, hogyan engedélyezheti a jelszókivonat-szinkronizálást elsődleges hitelesítési módszerként, ha az AD FS meghibásodott vagy elérhetetlenné vált.

>[!NOTE] 
>Bár ezeket a lépéseket általában vészhelyzeti vagy kimaradási helyzetekben hajtják végre, ajánlott tesztelni ezeket a lépéseket, és ellenőrizze az eljárásokat, mielőtt egy kimaradás bekövetkezne.

>[!NOTE]
>Abban az esetben, ha nem fér hozzá az Azure AD Connect-kiszolgálóhoz, vagy a kiszolgáló nem fér hozzá az internethez, az Azure AD-oldal változásainak segítése érdekében forduljon a [Microsoft támogatási szolgálatához.](https://support.microsoft.com/en-us/contactus/)

## <a name="prerequisites"></a>Előfeltételek
Ez az oktatóanyag az [oktatóanyagra épül: Egyetlen AD-erdőkörnyezet összefedése a felhőbe,](tutorial-federation.md) és az oktatóanyag megkísérlése előtt elengedhetetlen.  Ha még nem fejezte be az oktatóanyagot, tegye meg ezt a dokumentum lépései előtt.

>[!IMPORTANT]
>A PHS-re való váltás előtt készítsen biztonsági másolatot az AD FS-környezetről.  Ezt az [AD FS Gyors-visszaállítás eszközzel](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool#how-to-use-the-tool)teheti meg.

## <a name="enable-phs-in-azure-ad-connect"></a>PhS engedélyezése az Azure AD Connectben
Az első lépés, most, hogy van egy Azure AD Connect környezet, amely összevonási, bekapcsolja a jelszókivonat-szinkronizálást, és lehetővé teszi az Azure AD Connect szinkronizálni a kivonatokat.

Tegye a következőket:

1.  Kattintson duplán az asztalon létrehozott Azure AD Connect ikonra
2.  Kattintson a **Configure** (Konfigurálás) elemre.
3.  A További feladatok lapon válassza a **Szinkronizálási beállítások testreszabása lehetőséget,** és kattintson a **Tovább**gombra.
4.  Adja meg a globális rendszergazda felhasználónevét és jelszavát.  Ez a fiók [itt](tutorial-federation.md#create-a-global-administrator-in-azure-ad) jött létre az előző oktatóanyagban.
5.  A **Könyvtárak csatlakoztatása** képernyőn kattintson a **Tovább gombra.**
6.  A **Tartomány- és szervezetiegység-szűrés** képernyőn kattintson a **Tovább gombra.**
7.  A **Választható szolgáltatások** képernyőn jelölje be a **Jelszó-kivonat szinkronizálása jelölőnégyzetet,** és kattintson a **Tovább**gombra.
![Kiválasztás](media/tutorial-phs-backup/backup1.png)</br>
8.  A **Konfigurálásra kész** képernyőn kattintson a **Konfigurálás gombra.**
9.  A konfiguráció befejezése után kattintson a **Kilépés**gombra.
10. Ennyi az egész!  Kész is van.  A jelszókivonat-szinkronizálás mostantól megtörténik, és biztonsági másolatként is használható, ha az AD FS elérhetetlenné válik.

## <a name="switch-to-password-hash-synchronization"></a>Váltás jelszókivonat-szinkronizálásra
Most megmutatjuk, hogyan válthat át a jelszókivonat-szinkronizálásra. Mielőtt elkezdené, fontolja meg, hogy milyen feltételek mellett kell a kapcsolót. Ne váltson ideiglenes okokból, például hálózati kimaradás, kisebb AD FS-probléma vagy a felhasználók egy részét érintő probléma miatt. Ha úgy dönt, hogy a kapcsolót, mert a probléma megoldása túl sokáig tart, tegye a következőket:

> [!IMPORTANT]
> Ne feledje, hogy a jelszókivétek szinkronizálása eltart egy ideig.  Ez azt jelenti, hogy a szinkronizálások befejezése és a jelszókivétek hitelesítésének megkezdése előtt akár 3 órát is igénybe vehet.

1. Kattintson duplán az asztalon létrehozott Azure AD Connect ikonra
2.  Kattintson a **Configure** (Konfigurálás) elemre.
3.  Válassza **a Felhasználói bejelentkezés módosítása** lehetőséget, és kattintson a **Tovább**gombra.
![Módosítás](media/tutorial-phs-backup/backup2.png)</br>
4.  Adja meg a globális rendszergazda felhasználónevét és jelszavát.  Ez a fiók [itt](tutorial-federation.md#create-a-global-administrator-in-azure-ad) jött létre az előző oktatóanyagban.
5.  A **Felhasználó bejelentkezése** képernyőn jelölje be **a Jelszókivonat-szinkronizálás jelölőnégyzetet,** és helyezzen be a **Felhasználói fiókok konvertálása** párbeszédpanelt.  
6.  Hagyja meg az alapértelmezett **Egyszeri bejelentkezés engedélyezése** lehetőséget, és kattintson a **Tovább**gombra.
7.  Az **Egyszeri bejelentkezés engedélyezése** képernyőn kattintson a **Tovább gombra.**
8.  A **Konfigurálásra kész** képernyőn kattintson a **Konfigurálás gombra.**
9.  A konfiguráció befejezése után kattintson **a Kilépés**gombra.
10. A felhasználók mostantól használhatják a jelszavukat az Azure- és Azure-szolgáltatásokba való bejelentkezéshez.

## <a name="test-signing-in-with-one-of-our-users"></a>Bejelentkezés tesztelése az egyik felhasználónkkal

1. Tallózás a[https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Jelentkezzen be az új bérlőben létrehozott felhasználói fiókkal.  A következő formátumban kell bejelentkeznie: (user@domain.onmicrosoft.com). Használja ugyanazt a jelszót, amelyet a felhasználó a helyszíni bejelentkezéshez használ.</br>
   ![Ellenőrzés](media/tutorial-password-hash-sync/verify1.png)</br>

## <a name="switch-back-to-federation"></a>Váltás vissza az összevonásra
Most megmutatjuk, hogyan válthat vissza a Föderációra.  Ehhez tegye a következőket:

1.  Kattintson duplán az asztalon létrehozott Azure AD Connect ikonra
2.  Kattintson a **Configure** (Konfigurálás) elemre.
3.  Válassza **a Felhasználói bejelentkezés módosítása** lehetőséget, és kattintson a **Tovább**gombra.
4.  Adja meg a globális rendszergazda felhasználónevét és jelszavát.  Ez az a fiók, amely [itt](tutorial-federation.md#create-a-global-administrator-in-azure-ad) jött létre az előző oktatóanyagban.
5.  A **Felhasználó bejelentkezése** képernyőn válassza az **Összevonás a AD FS-sel** lehetőséget, majd kattintson a **Tovább**gombra.  
6. A Tartományi rendszergazda hitelesítő adatai lapon adja meg a contoso\Rendszergazda felhasználónevet és jelszót, majd kattintson a **Tovább gombra.**
7. Az AD FS farm képernyőjén kattintson a **Tovább**gombra.
8. Az **Azure AD tartomány** képernyőjén válassza ki a tartományt a legördülő menüből, és kattintson a **Tovább**gombra.
9. A **Konfigurálásra kész** képernyőn kattintson a **Konfigurálás gombra.**
10. A konfiguráció befejezése után kattintson a **Tovább**gombra.
![Konfigurálás](media/tutorial-phs-backup/backup4.png)</br>
11. Az **Összevonási kapcsolat ellenőrzése** képernyőn kattintson az **Ellenőrzés gombra.**  Előfordulhat, hogy a sikeres befejezéshez be kell állítania a DNS-rekordokat (A és AAAA rekordok hozzáadása).
![Ellenőrzés](media/tutorial-phs-backup/backup5.png)</br>
12. Kattintson **a Kilépés gombra.**

## <a name="reset-the-ad-fs-and-azure-trust"></a>Az AD FS és az Azure megbízhatóságának alaphelyzetbe állítása
Most vissza kell állítanunk az AD FS és az Azure közötti megbízhatóságot.

1.  Kattintson duplán az asztalon létrehozott Azure AD Connect ikonra
2.  Kattintson a **Configure** (Konfigurálás) elemre.
3.  Válassza **az Összevonás kezelése lehetőséget,** és kattintson a **Tovább gombra.**
4.  Válassza **az Azure AD megbízhatóságának alaphelyzetbe állítása** lehetőséget, és kattintson a **Tovább**gombra.
![Alaphelyzetbe állítás](media/tutorial-phs-backup/backup6.png)</br>
5.  Az **Azure AD-hez való csatlakozás** képernyőn adja meg a globális rendszergazda felhasználónevét és jelszavát.
6.  A Csatlakozás az **AD FS-hez** képernyőn adja meg a contoso\Rendszergazda felhasználónevet és jelszót, majd kattintson a **Tovább gombra.**
7.  A **Tanúsítványok** képernyőn kattintson a **Tovább gombra.**

## <a name="test-signing-in-with-one-of-our-users"></a>Bejelentkezés tesztelése az egyik felhasználónkkal

1.  Tallózás a[https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Bejelentkezés az új bérlőben létrehozott felhasználói fiókkal.  A következő formátumban kell bejelentkeznie: (user@domain.onmicrosoft.com). Használja ugyanazt a jelszót, amelyet a felhasználó a helyszíni bejelentkezéshez használ.
![Ellenőrzés](media/tutorial-password-hash-sync/verify1.png)

Most már sikeresen beállított egy hibrid identitáskörnyezetet, amely segítségével tesztelheti és megismerkedhet az Azure ajánlatával.

## <a name="next-steps"></a>További lépések


- [Hardver és előfeltételek](how-to-connect-install-prerequisites.md) 
- [Gyorsbeállítások](how-to-connect-install-express.md)
- [Jelszókivonat szinkronizálása](how-to-connect-password-hash-synchronization.md)
