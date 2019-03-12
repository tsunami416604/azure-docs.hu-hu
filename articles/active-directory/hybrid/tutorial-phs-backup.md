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
ms.date: 01/30/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 03e91726dd8f37fb2c3b8ffe2c8e89762e8d3dd6
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57546381"
---
# <a name="tutorial--setting-up-phs-as-backup-for-ad-fs-in-azure-ad-connect"></a>Oktatóanyag:  Beállítása nál biztonsági az AD FS az Azure AD Connectben

A következő oktatóanyag végigvezeti egy biztonsági másolat és az AD FS-hez a feladatátvétel Jelszókivonat-szinkronizálás beállítása.  Ez a dokumentum is mutatják be, hogyan Jelszókivonat-szinkronizálás engedélyezése az elsődleges hitelesítési módszerként, ha az AD FS nem sikerült vagy már nem érhető el.

## <a name="prerequisites"></a>Előfeltételek
Ebben az oktatóanyagban épít az [oktatóanyag: Egy AD egyetlen erdővel rendelkező környezetben a felhőbe való összevonásához](tutorial-federation.md) és előtt ebben az oktatóanyagban egy per-követelmény.  Ha még nem ment ebben az oktatóanyagban, tegye a jelen dokumentumban leírt lépések megkísérlése előtt.

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

1.  Keresse meg a [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Jelentkezzen be egy felhasználói fiókot, amely az új bérlőt létrehozták.  Jelentkezzen be a következő formátumban kell: (user@domain.onmicrosoft.com). A felhasználó által használt helyszíni bejelentkezéshez ugyanazt a jelszót használja.</br>
![Ellenőrizze](media/tutorial-password-hash-sync/verify1.png)</br>

## <a name="next-steps"></a>További lépések


- [Hardver és előfeltételek](how-to-connect-install-prerequisites.md) 
- [Gyorsbeállítások](how-to-connect-install-express.md)
- [Jelszókivonat szinkronizálása](how-to-connect-password-hash-synchronization.md)|
