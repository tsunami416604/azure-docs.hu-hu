---
title: Hogyan lehet hozzáadni vagy frissíteni a felhasználói profil adatait az Azure Active Directoryban |} A Microsoft Docs
description: További információk hozzáadása a felhasználói profil, beleértve a profilhoz tartozó fotó, feladat-specifikus információkat és bizonyos beállítások az Azure Active Directoryban.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.openlocfilehash: d4e2ebd88430cab1fe0734e342b1c9ba4a29af1b
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2018
ms.locfileid: "45728946"
---
# <a name="how-to-add-or-update-a-users-profile-information-using-azure-active-directory"></a>Hogyan: hozzáadása vagy frissítése egy felhasználói profil adatait az Azure Active Directoryval
Adja hozzá a felhasználói profil információit, beleértve a profilképét, feladat-specifikus információkat és bizonyos beállítások, az Azure Active Directory (Azure AD) használatával. Új felhasználók hozzáadásáról szóló további információkért lásd: [hozzáadása vagy törlése az Azure Active Directory felhasználók](add-users-azure-active-directory.md).

## <a name="add-or-change-profile-information"></a>Profil adatok hozzáadása vagy módosítása
Látni fogja, ahogy érhető további információt a felhasználói profil, mint amit Ön a felhasználó létrehozása során adhat hozzá. Minden további információként ez nem kötelező, és a szervezet által igény szerint adhatók hozzá.

## <a name="to-add-or-change-profile-information"></a>Hozzáadásához vagy módosításához a profil adatait
1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) globális rendszergazdai vagy a címtár felhasználói rendszergazdaként.

2. Válassza ki **Azure Active Directory**válassza **felhasználók**, majd válassza ki a felhasználó. Ha például _Alain Charon_.

    A **Alain Charon - profil** lap jelenik meg.

    ![Felhasználói profil nézetkonfigurációját, beleértve a szerkeszthető információ](media/active-directory-users-profile-azure-portal/user-profile-all-blade.png)

3. Válassza ki **szerkesztése** igény szerint adja hozzá, vagy frissíteni az adatokat az egyes szakaszok elérhetőségét tartalmazza.

    ![A felhasználó profillapján, a szerkeszthető területeket megjelenítő](media/active-directory-users-profile-azure-portal/user-profile-edit.png)

    - **Profil kép.** Válasszon ki egy miniatűrképet, a felhasználói fiók. Ezt a képet az Azure Active Directoryban, és a felhasználó személyes oldalain, például a myapps.microsoft.com lap jelenik meg.

    - **Identitás.** Adjon hozzá bármilyen fiók-kapcsolatos információkat, például egy házas Vezetéknév vagy egy módosított felhasználónevet. 

    - **Munkakör adatai.** Adjon hozzá bármilyen feladat kapcsolatos információkat, például a felhasználó beosztása, részleg vagy kezelő.

    - **Beállítások.** Döntse el, hogy a felhasználó bejelentkezhet az Azure Active Directory-bérlővel. A felhasználó globális helyén is megadhatja.

    - **Kapcsolattartási adatok.** Adja hozzá a felhasználó megfelelő elérhetőségi adatait. Ha például egy utca, házszám vagy Mobiltelefonszám.

    - **Hitelesítési kapcsolattartási adatok.** Győződjön meg arról, hogy van egy aktív telefonszámát és az e-mail címét a felhasználó ezt az információt. Ez az információ az Azure Active Directory használják, hogy a felhasználó valójában a felhasználói bejelentkezés során. Hitelesítési kapcsolattartási adatok csak a globális rendszergazda is frissíthető.

4. Kattintson a **Mentés** gombra.

    A felhasználó összes a módosítások mentése.

    >[!Note]
    >Az identitás, a kapcsolattartási adatok vagy a feladat adatainak a felhasználók számára, akiknek mérvadó forrás a Windows Server Active Directory frissítéséhez a Windows Server Active Directory kell használnia. Miután elvégezte a frissítést, meg kell várnia a következő szinkronizálási ciklus befejezését, mielőtt a módosítások láthatja.

## <a name="next-steps"></a>További lépések
A felhasználói profilok frissítése után az alábbi alapszintű folyamatok hajthatja végre:

- [Adja hozzá, vagy a felhasználók törlése](add-users-azure-active-directory.md)

- [Szerepkörök hozzárendelése felhasználókhoz](active-directory-users-assign-role-azure-portal.md)

- [Hozzon létre egy alapszintű csoportot, és tagokat vehet fel](active-directory-groups-create-azure-portal.md)

Vagy más felhasználói felügyeleti feladatokat, például a Microsoftra hozzárendelése, házirendekkel és megosztása, felhasználói fiókok is végezhet. Egyéb elérhető műveletekkel kapcsolatos további információkért lásd: [Azure Active Directory felhasználói felügyeleti dokumentáció](../users-groups-roles/index.yml).
