---
title: 'Oktatóanyag:  Beállítása nál biztonsági az AD FS az Azure AD Connectben |} A Microsoft Docs'
description: Bemutatja, hogyan kapcsolja be a Jelszókivonat-szinkronizálás, biztonsági és az AD FS-hez.
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
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64919008"
---
# <a name="tutorial--setting-up-phs-as-backup-for-ad-fs-in-azure-ad-connect"></a>Oktatóanyag:  Beállítása nál biztonsági az AD FS az Azure AD Connectben

A következő oktatóanyag végigvezeti egy biztonsági másolat és az AD FS-hez a feladatátvétel Jelszókivonat-szinkronizálás beállítása.  Ez a dokumentum is mutatják be, hogyan Jelszókivonat-szinkronizálás engedélyezése az elsődleges hitelesítési módszerként, ha az AD FS nem sikerült vagy már nem érhető el.

>[!NOTE] 
>Bár ezek a lépések végrehajtása általában történik vészhelyzet vagy leállás esetén, javasoljuk, hogy tesztelje ezeket a lépéseket, és ellenőrizze az eljárások leállás előtt.

>[!NOTE]
>Abban az esetben, ha nem rendelkezik Azure AD Connect-kiszolgáló elérését, vagy a kiszolgáló nem rendelkezik internet-hozzáférést, forduljon [Support](https://support.microsoft.com/en-us/contactus/) , amelyek segítik a módosításokat az Azure ad-ben oldalán.

## <a name="prerequisites"></a>Előfeltételek
Ebben az oktatóanyagban épít az [oktatóanyag: Egy AD egyetlen erdővel rendelkező környezetben a felhőbe való összevonásához](tutorial-federation.md) és előtt ebben az oktatóanyagban egy per-követelmény.  Ha még nem ment ebben az oktatóanyagban, tegye a jelen dokumentumban leírt lépések megkísérlése előtt.

>[!IMPORTANT]
>Mielőtt PHS-ben az AD FS-környezet biztonsági kell létrehoznia.  Ehhez használja a [AD FS gyors visszaállítási eszköz](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool#how-to-use-the-tool).

## <a name="enable-phs-in-azure-ad-connect"></a>NÁL engedélyezése az Azure AD Connect
Most, hogy az Azure AD Connect-environment által használt összevonási, az első lépés, hogy kapcsolja be a Jelszókivonat-szinkronizálás és a kivonatok szinkronizálását az Azure AD Connect engedélyezése.

Tegye a következőket:

1.  Az Azure AD Connect ikonra az asztalon létrehozott
2.  Kattintson a **Configure** (Konfigurálás) elemre.
3.  További feladatok lapon válassza ki a **szinkronizálási beállítások testreszabása** kattintson **tovább**.
4.  Adja meg a felhasználónevet és jelszót a globális rendszergazdához.  Ez a fiók létrejött [Itt](tutorial-federation.md#create-a-global-administrator-in-azure-ad) az előző oktatóanyagban.
5.  Az a **csatlakoztassa a címtárakat** kattintson **tovább**.
6.  Az a **tartomány és szervezeti egységek szűrése** kattintson **tovább**.
7.  Az a **választható funkciók** képernyőn ellenőrizze **Jelszókivonat-szinkronizálás** kattintson **tovább**.
![Kiválasztás](media/tutorial-phs-backup/backup1.png)</br>
8.  Az a **konfigurálásra kész** kattintson képernyő **konfigurálása**.
9.  A konfiguráció befejezését követően kattintson **kilépési**.
10. Ennyi az egész!  Elkészült.  A Jelszókivonat-szinkronizálás most történik, és biztonsági is használható, ha az AD FS nem érhető el.

## <a name="switch-to-password-hash-synchronization"></a>Váltson a Jelszókivonat-szinkronizálás
Most hogy bemutatja, hogyan vált át a Jelszókivonat-szinkronizálás. Mielőtt elkezdené, gondolja át, milyen feltételek mellett, győződjön meg a kapcsoló. Ne ideiglenes okokból, mint például a hálózati leállások, kisebb az AD FS hiba vagy probléma, amely a felhasználók egy részhalmazát érinti a kapcsolót. Ha úgy dönt, hogy a kapcsoló, mivel a probléma elhárítása túl sokáig tart, tegye a következőket:

> [!IMPORTANT]
> Vegye figyelembe, hogy ez hosszabb időt vesz igénybe a jelszó kivonatok szinkronizálását az Azure ad-hez.  Ez azt jelenti, hogy a szinkronizálás végrehajtásához 3 órát igénybe vehet, és a jelszavak kivonatait segítségével történő hitelesítésről indítása előtt.

1. Az Azure AD Connect ikonra az asztalon létrehozott
2.  Kattintson a **Configure** (Konfigurálás) elemre.
3.  Válassza ki **felhasználói bejelentkezés módosítása** kattintson **tovább**.
![Módosítása](media/tutorial-phs-backup/backup2.png)</br>
4.  Adja meg a felhasználónevet és jelszót a globális rendszergazdához.  Ez a fiók létrejött [Itt](tutorial-federation.md#create-a-global-administrator-in-azure-ad) az előző oktatóanyagban.
5.  Az a **felhasználói bejelentkezés** képernyőn válassza ki **Jelszókivonat-szinkronizálás** , és helyezze be a **felhasználói fiókok konvertálásának** mezőbe.  
6.  Hagyja meg az alapértelmezett **egyszeri bejelentkezés engedélyezése** kiválasztva, majd kattintson **tovább**.
7.  Az a **egyszeri bejelentkezés engedélyezése** kattintson képernyő **tovább**.
8.  Az a **konfigurálásra kész** kattintson **konfigurálása**.
9.  Kattintson a konfigurálás elvégzését követően **kilépési**.
10. Felhasználók most már használhatja a jelszavukat, jelentkezzen be az Azure és az Azure szolgáltatások.

## <a name="test-signing-in-with-one-of-our-users"></a>Hogy a felhasználók bejelentkezés tesztelése

1. Keresse meg a [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Jelentkezzen be egy felhasználói fiókot, amely az új bérlőt létrehozták.  Jelentkezzen be a következő formátumban kell: (user@domain.onmicrosoft.com). A felhasználó által használt helyszíni bejelentkezéshez ugyanazt a jelszót használja.</br>
   ![Ellenőrizze](media/tutorial-password-hash-sync/verify1.png)</br>

## <a name="switch-back-to-federation"></a>Váltson vissza az összevonási
Most hogy bemutatja, hogyan váltson vissza az összevonási.  Ehhez tegye a következőket:

1.  Az Azure AD Connect ikonra az asztalon létrehozott
2.  Kattintson a **Configure** (Konfigurálás) elemre.
3.  Válassza ki **felhasználói bejelentkezés módosítása** kattintson **tovább**.
4.  Adja meg a felhasználónevet és jelszót a globális rendszergazdához.  Ez az a fiók létrehozásának [Itt](tutorial-federation.md#create-a-global-administrator-in-azure-ad) az előző oktatóanyagban.
5.  Az a **felhasználói bejelentkezés** képernyőn válassza ki **és az AD FS összevonási** kattintson **tovább**.  
6. A tartományi rendszergazda hitelesítő adatai oldalon írja be a contoso\rendszergazda felhasználónévvel és jelszóval, és kattintson a **tovább.**
7. Az AD FS farm képernyőn kattintson a **tovább**.
8. Az a **Azure AD-tartomány** képernyőn válassza ki a tartományt a legördülő menüből, kattintson a **tovább**.
9. Az a **konfigurálásra kész** kattintson **konfigurálása**.
10. Kattintson a konfigurálás elvégzését követően **tovább**.
![Konfigurálás](media/tutorial-phs-backup/backup4.png)</br>
11. Az a **összevonási kapcsolat ellenőrzése** kattintson **ellenőrizze**.  Szükség lehet a DNS-rekordjait (adja hozzá a és AAAA rekord) hajtsa végre a sikeres konfigurálásához.
![Ellenőrizze](media/tutorial-phs-backup/backup5.png)</br>
12. Kattintson a **kilépési**.

## <a name="reset-the-ad-fs-and-azure-trust"></a>Az AD FS és az Azure-megbízhatóság alaphelyzetbe állítása
Most meg kell az AD FS és az Azure közötti megbízhatósági kapcsolat alaphelyzetbe állítása.

1.  Az Azure AD Connect ikonra az asztalon létrehozott
2.  Kattintson a **Configure** (Konfigurálás) elemre.
3.  Válassza ki **kezelése összevonási** kattintson **tovább**.
4.  Válassza ki **alaphelyzetbe állítása az Azure AD-megbízhatóság** kattintson **tovább**.
![Alaphelyzetbe állítása](media/tutorial-phs-backup/backup6.png)</br>
5.  Az a **az Azure AD Connect** képernyőn adja meg a felhasználónevet és jelszót a globális rendszergazdához.
6.  Az a **csatlakozhat az AD FS** képernyőn írja be a contoso\rendszergazda felhasználónévvel és jelszóval, kattintson a **tovább.**
7.  Az a **tanúsítványok** kattintson **tovább**.

## <a name="test-signing-in-with-one-of-our-users"></a>Hogy a felhasználók bejelentkezés tesztelése

1.  Keresse meg a [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Jelentkezzen be az új bérlő létrehozott felhasználói fiókkal.  Kell jelentkezzen be a következő formátumban: (user@domain.onmicrosoft.com). A felhasználó által használt a bejelentkezéshez ugyanazt a jelszót a helyszínen.
![Ellenőrizze](media/tutorial-password-hash-sync/verify1.png)

Most már sikeresen befejeződött a telepítő egy hibrid identitás környezet, amellyel tesztelheti, és ismerje meg az Azure által biztosított lehetőségeket.

## <a name="next-steps"></a>További lépések


- [Hardver és előfeltételek](how-to-connect-install-prerequisites.md) 
- [Gyorsbeállítások](how-to-connect-install-express.md)
- [Jelszókivonat szinkronizálása](how-to-connect-password-hash-synchronization.md)
