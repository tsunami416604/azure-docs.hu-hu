---
title: Felhasználói profiladatainak hozzáadása vagy frissítése – Azure AD
description: Útmutató arról, hogyan adhat hozzá információkat a felhasználó profiljához az Azure Active Directoryban, beleértve a képet és a feladat részleteit.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ba51b0ad7f314058f34092dff4e26411020086a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422882"
---
# <a name="add-or-update-a-users-profile-information-using-azure-active-directory"></a>Felhasználói profil adatainak hozzáadása vagy frissítése az Azure Active Directory használatával
Adja meg a felhasználói profil adatait, beleértve a profilképet, a feladatspecifikus információkat és néhány beállítást az Azure Active Directory (Azure AD) használatával. Az új felhasználók hozzáadásáról további információt a Felhasználók hozzáadása és törlése az Azure Active Directoryban című [témakörben talál.](add-users-azure-active-directory.md)

## <a name="add-or-change-profile-information"></a>Profiladatok hozzáadása vagy módosítása
Amint látni fogja, több információ áll rendelkezésre a felhasználó profiljában, mint amit a felhasználó létrehozása során fel tud adni. Mindezek a további információk nem kötelezőek, és szükség szerint hozzáadható a szervezet.

## <a name="to-add-or-change-profile-information"></a>Profiladatok hozzáadása vagy módosítása
1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com/) a szervezet felhasználói rendszergazdájaként.

2. Válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd válasszon ki egy felhasználót. _Például, Alain Charon_.

    Megjelenik **az Alain Charon - Profil** oldal.

    ![A felhasználó profillapja, beleértve a szerkeszthető adatokat](media/active-directory-users-profile-azure-portal/user-profile-all-blade.png)

3. Válassza a **Szerkesztés** lehetőséget, ha az egyes elérhető szakaszokban szereplő információkat is hozzáadhatja vagy frissítheti.

    ![A felhasználó profillapja, amely a szerkeszthető területeket jeleníti meg](media/active-directory-users-profile-azure-portal/user-profile-edit.png)

    - **Profilkép.** Jelöljön ki egy miniatűr képet a felhasználó fiókjához. Ez a kép az Azure Active Directoryban és a felhasználó személyes lapjain, például a myapps.microsoft.com lapon jelenik meg.

    - **Identitás.** Adjon hozzá vagy frissítsen egy további identitásértéket a felhasználó számára, például egy házas vezetéknevet. Ezt a nevet a vezetéknév és a vezetéknév értékétől függetlenül is beállíthatja. Használhatja például monogram, vállalatnév felvételére vagy a megjelenített nevek sorrendjének módosítására. Egy másik példában két olyan felhasználó esetében, akiknek a neve "Chris Green", az Identitás karakterláncsegítségével a nevüket "Chris B. Green" 'Chris R. Green (Contoso) értékre állíthatja.

    - **Állásinformáció.** Adja meg a feladattal kapcsolatos információkat, például a felhasználó beosztását, részlegét vagy vezetőjét.

    - **Beállítások.** Döntse el, hogy a felhasználó bejelentkezhet-e az Azure Active Directory-bérlőbe. Megadhatja a felhasználó globális helyét is.

    - **Elérhetőség.** Adja meg a felhasználó számára a megfelelő kapcsolattartási adatokat, kivéve néhány felhasználó telefon- vagy mobilkapcsolattartási adatait (csak globális rendszergazda frissítheti a rendszergazdai szerepkörökben lévő felhasználókat).

    - **Hitelesítési kapcsolattartási adatok.** Ellenőrizze ezeket az adatokat, és győződjön meg arról, hogy aktív telefonszám és e-mail-cím van a felhasználó számára. Ezt az információt az Azure Active Directory használja annak érdekében, hogy a felhasználó valóban a felhasználó bejelentkezés közben. A hitelesítési kapcsolattartási adatokat csak globális rendszergazda frissítheti.

4. Kattintson a **Mentés** gombra.

    A rendszer minden módosítást a felhasználó számára ment.

    >[!Note]
    >A Windows Server Active Directory használatával frissítenie kell az identitás-, kapcsolattartási adatokat vagy feladatadatokat azon felhasználók számára, akiknek a forrása a Windows Server Active Directory. A frissítés befejezése után meg kell várnia a következő szinkronizálási ciklus befejezését, mielőtt megjelennek a módosítások.

## <a name="next-steps"></a>További lépések
A felhasználói profilok frissítése után a következő alapvető folyamatokat hajthatja végre:

- [Felhasználók hozzáadása vagy törlése](add-users-azure-active-directory.md)

- [Szerepkörök hozzárendelése felhasználókhoz](active-directory-users-assign-role-azure-portal.md)

- [Alapszintű csoport létrehozása és tagok hozzáadása](active-directory-groups-create-azure-portal.md)

Más felhasználókezelési feladatokat is végrehajthat, például meghatalmazottakat, házirendek használatával és felhasználói fiókok megosztásával. Az egyéb elérhető műveletekről az [Azure Active Directory felhasználói felügyeleti dokumentációjában](../users-groups-roles/index.yml)olvashat bővebben.
