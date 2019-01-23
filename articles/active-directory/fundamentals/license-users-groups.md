---
title: Rendelje hozzá, vagy távolítsa el a licencek – Azure Active Directory |} A Microsoft Docs
description: Rendelje hozzá, vagy távolítsa el az Azure Active Directory-licenceket a felhasználók vagy csoportok kapcsolatos utasításokat.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.openlocfilehash: b7abcb4820dde221f17efa1fcded05df41fa7bed
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54449486"
---
# <a name="assign-or-remove-licenses-using-the-azure-active-directory-portal"></a>Rendelje hozzá, vagy távolítsa el a licencek használata az Azure Active Directory portálon
Számos Azure Active Directory (Azure AD) szolgáltatás az adott termék szükséges, hogy aktiválja az Azure AD-termékre, és a felhasználók vagy csoportok (és társított tagok) minden egyes licenc. Csak az aktív licenccel rendelkező felhasználók hozzáférhetnek, és a licencelt használata az Azure AD-szolgáltatások.

## <a name="available-product-editions"></a>Elérhető termékazonosító kiadások
Nincsenek elérhető az Azure AD-termék néhány kiadásai.

- Azure AD Free

- Azure AD Basic

- Az Azure AD Premium 1 (az Azure AD P1)

- Az Azure AD Premium 2 (Azure AD P2)

Részletes információ az egyes termék edition és a társított licencelési részleteket lásd: [milyen licenc van szükségem?](../authentication/concept-sspr-licensing.md).

## <a name="view-your-product-edition-and-license-details"></a>A termék edition és a licenc részleteinek megtekintése
Megtekintheti az elérhető termékek, többek között az egyes licencek ellenőrzése minden függőben lévő lejárati dátumát és az elérhető hozzárendelések száma.

### <a name="to-find-your-product-and-license-details"></a>A termék- és licenc részletei keresése
1. A címtár eléréséhez globális rendszergazdai fiókkal jelentkezzen be az [Azure portálra](https://portal.azure.com/).

2. Válassza ki **Azure Active Directory**, majd válassza ki **licencek**.

    A **licencek** lap jelenik meg.

    ![Licencek lapon azokról a megvásárolt termékek és licencek hozzárendelése](media/license-users-groups/license-details-blade.png)
    
3. Válassza ki a **vásárolt termékek** hivatkozásra kattintva a **termékek** lapot, és tekintse meg a **hozzárendelt**, **elérhető**, és  **Hamarosan lejáró** minden egyes megadott termék kiadásának részleteit.

    ![Termékek oldala, amelyen a termék kiadására és társított licencelési információ](media/license-users-groups/license-products-blade-with-products.png)

4. Jelöljön ki egy kiadást a licenccel rendelkező felhasználók és csoportok megtekintéséhez.

## <a name="assign-licenses-to-users-or-groups"></a>Licencek hozzárendelése a felhasználókhoz vagy csoportokhoz
Győződjön meg arról, hogy bárki használja egy licencelt kellene az Azure AD szolgáltatás rendelkezik a megfelelő licenccel. Arra, hogy adja hozzá a licencelési jogokat, egyéni felhasználók számára vagy egy teljes csoport szeretné.

>[!Note]
>Csoportalapú licencelés nyilvános előzetes verziójú funkció az Azure AD és az összes rendelkezésre álló fizetős Azure AD-licenccsomag. További információ az előzetes verziókról: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).<br><br>Felhasználók hozzáadásával kapcsolatos részletes információkért lásd: [hozzáadása vagy törlése az Azure Active Directory felhasználók](add-users-azure-active-directory.md). Csoportok létrehozása és tagok hozzáadása kapcsolatos részletes információkért lásd: [létrehozásához, és tagokat vehet fel](active-directory-groups-create-azure-portal.md).

### <a name="to-assign-a-license-to-a-specific-user"></a>Licenc hozzárendelése egy adott felhasználó
1. Az a **termékek** lapon, válassza ki a nevét, a felhasználóhoz rendelni kívánt kiadást. Ha például _Azure Active Directory Premium 2. csomag_.

    ![Termékek oldala, amelyen kiemelve termék edition](media/license-users-groups/license-products-blade-with-product-highlight.png)

2. Az a **Azure Active Directory Premium 2. csomag** lapon jelölje be **hozzárendelése**.

    ![Termékek oldala, amelyen kiemelt hozzárendelése lehetőség](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

3. Az a **hozzárendelése** lapon jelölje be **felhasználók és csoportok**, és keressen rá, és válassza ki a felhasználót, még a licenc hozzárendelésével. Ha például _Mary Parker_.

    ![Licenc oldala, amelyen a kiemelt keresést és választhat hozzárendelése](media/license-users-groups/assign-license-blade-with-highlight.png)

4. Válassza ki **hozzárendelési beállítások**, ellenőrizze, hogy a megfelelő licencet a beállítások engedélyezve van, és válassza ki **OK**.

    ![Licenc lehetőséget oldal összes kiadásában rendelkezésre álló beállítások megjelenítése](media/license-users-groups/license-option-blade-assignments.png)

    A **licenc hozzárendelése** frissítések jeleníti meg, hogy egy felhasználó van kiválasztva, valamint, hogy vannak-e konfigurálva a hozzárendeléseket lapon.

    >[!NOTE]
    >Az összes hely nem minden Microsoft-szolgáltatások érhetők el. Mielőtt egy úgy lehet licencet a felhasználóhoz, meg kell adnia a **a felhasználási hely**. Ez az érték állítható be a **Azure Active Directory &gt; felhasználók &gt; profil &gt; beállítások** terület az Azure ad-ben.

5. Válassza a **Hozzárendelés** elemet.

    A felhasználó bekerül a licenccel rendelkező felhasználók listáját, és a benne foglalt hozzáféréssel rendelkezik az Azure AD-szolgáltatások.

### <a name="to-assign-a-license-to-an-entire-group"></a>Licenc hozzárendelése egy teljes csoport
1. Az a **termékek** lapon, válassza ki a nevét, a felhasználóhoz rendelni kívánt kiadást. Ha például _Azure Active Directory Premium 2. csomag_.

    ![Termékek panelen kiemelt termék Edition](media/license-users-groups/license-products-blade-with-product-highlight.png)

2. Az a **Azure Active Directory Premium 2. csomag** lapon jelölje be **hozzárendelése**.

    ![Termékek oldala, amelyen kiemelt hozzárendelése lehetőség](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

3. Az a **hozzárendelése** lapon jelölje be **felhasználók és csoportok**, és keressen rá, és válassza ki a csoportot, akkor még hozzárendelése a licenc. Ha például _mobileszköz-kezelési szabályzat – Nyugat-India_.

    ![Licenc oldala, amelyen a kiemelt keresést és választhat hozzárendelése](media/license-users-groups/assign-group-license-blade-with-highlight.png)

4. Válassza ki **hozzárendelési beállítások**, ellenőrizze, hogy a megfelelő licencet a beállítások engedélyezve van, és válassza ki **OK**.

    ![Licenc lehetőséget oldal összes kiadásában rendelkezésre álló beállítások megjelenítése](media/license-users-groups/license-option-blade-group-assignments.png)

    A **licenc hozzárendelése** frissítések jeleníti meg, hogy egy felhasználó van kiválasztva, valamint, hogy vannak-e konfigurálva a hozzárendeléseket lapon.

    >[!NOTE]
    >Az összes hely nem minden Microsoft-szolgáltatások érhetők el. Mielőtt egy úgy lehet licencet egy csoporthoz, meg kell adnia a **a felhasználási hely** összes tagjához. Ez az érték állítható be a **Azure Active Directory &gt; felhasználók &gt; profil &gt; beállítások** terület az Azure ad-ben. Bármely felhasználó, akinek a felhasználási hely nincs megadva örökli a bérlő helyét.

5. Válassza a **Hozzárendelés** elemet.

    A csoport bekerül a licenccel rendelkező csoportok listáját, és minden tagot fér hozzá a csomagban foglalt Azure AD-szolgáltatások.


## <a name="remove-a-license"></a>Licenc eltávolítása
Licenc eltávolítása egy felhasználót vagy csoportot a **licencek** lapot.

### <a name="to-remove-a-license-from-a-specific-user"></a>Licenc eltávolítása egy adott felhasználó
1. Az a **licenccel rendelkező felhasználók** a termék kiadás lapon, válassza ki a felhasználót, hogy a licenc már nem rendelkeznek. Ha például _Alain Charon_.

2. Válassza ki **Remove licenc**.

    ![A licenccel rendelkező felhasználók oldal az Eltávolítás licenc lehetőség kiemelésével](media/license-users-groups/license-products-user-blade-with-remove-option-highlight.png)

### <a name="to-remove-a-license-from-a-group"></a>Licenc eltávolítása egy csoportból
1. Az a **licenccel rendelkező csoportok** a termék kiadás lapra, jelölje be a csoport, a licenc már nem rendelkeznek. Ha például _mobileszköz-kezelési szabályzat – Nyugat-India_.

2. Válassza ki **Remove licenc**.

    ![A Remove-licenc opció kiemelésével licenccel rendelkező csoportok lap](media/license-users-groups/license-products-group-blade-with-remove-option-highlight.png)

>[!Important]
>Egy felhasználó egy csoportból örökölt licenceket nem távolítható el közvetlenül. Ehelyett lehetősége van a felhasználó eltávolítása a csoportból, amelyről azokat Ön örökli a licenc.

## <a name="next-steps"></a>További lépések
Miután hozzárendelte a licenceket, végezheti el a következő eljárásokat:

- [Azonosítani és megoldani a licenc-hozzárendelési problémák](../users-groups-roles/licensing-groups-resolve-problems.md)

- [A licenccel rendelkező felhasználók felvétele egy csoportba licenckezeléshez](../users-groups-roles/licensing-groups-migrate-users.md)

- [Forgatókönyvek, korlátait és ismert problémák csoportok használata kezelheti az Azure Active Directory licencelése](../users-groups-roles/licensing-group-advanced.md)

- [Profil adatok hozzáadása vagy módosítása](active-directory-users-profile-azure-portal.md)
