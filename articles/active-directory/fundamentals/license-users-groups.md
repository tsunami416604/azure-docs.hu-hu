---
title: Rendelje hozzá, vagy távolítsa el a licencek – Azure Active Directory |} A Microsoft Docs
description: Rendelje hozzá, vagy távolítsa el az Azure Active Directory-licenceket a felhasználók vagy csoportok kapcsolatos utasításokat.
services: active-directory
author: msaburnley
manager: daveba
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 869713967a6a89611ab2ea9ca7b7516f090a5f92
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72034284"
---
# <a name="assign-or-remove-licenses-in-the-azure-active-directory-portal"></a>Licencek kiosztása vagy eltávolítása a Azure Active Directory portálon

Számos Azure Active Directory (Azure AD) szolgáltatáshoz az adott szolgáltatáshoz tartozó felhasználók vagy csoportok (és a társított tagok) licence szükséges. Csak az aktív licenccel rendelkező felhasználók férhetnek hozzá és használhatják a licencelt Azure AD-szolgáltatásokat, amelyekre igaz.

## <a name="available-license-plans"></a>Rendelkezésre álló licenc csomagok

Az Azure AD szolgáltatáshoz több engedélyezési csomag is elérhető, többek között:

- Azure AD Free

- Prémium szintű Azure AD P1

- Prémium szintű Azure AD P2

Az egyes licencelési csomagokkal és a hozzájuk kapcsolódó licencelési adatokkal kapcsolatos információkért lásd: [milyen licencre van szükségem?](https://azure.microsoft.com/pricing/details/active-directory/).

Az összes hely nem minden Microsoft-szolgáltatások érhetők el. Mielőtt egy úgy lehet licencet egy csoporthoz, meg kell adnia a **a felhasználási hely** összes tagjához. Ez az érték állítható be a **Azure Active Directory &gt; felhasználók &gt; profil &gt; beállítások** terület az Azure ad-ben. Minden olyan felhasználó, akinek a használati helye nincs megadva, örökli az Azure AD-szervezet helyét.

## <a name="view-license-plans-and-plan-details"></a>A licencelési csomagok és a csomag részleteinek megtekintése

Megtekintheti az elérhető szolgáltatási csomagokat, beleértve az egyes licenceket, ellenőrizhető a lejárati dátumokat, és megtekintheti az elérhető hozzárendelések számát.

### <a name="to-find-your-service-plan-and-plan-details"></a>A szolgáltatási csomag és a terv részleteinek megkeresése

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) az Azure ad-szervezetben található licenc-rendszergazdai fiók használatával.

1. Válassza ki **Azure Active Directory**, majd válassza ki **licencek**.

    ![Licencek lap, a megvásárolt szolgáltatások és a hozzárendelt licencek számával](media/license-users-groups/license-details-blade.png)

1. A **megvásárolt** hivatkozásra kattintva megtekintheti a **termékek** lapot, és megtekintheti a licencekhez **rendelt**, **elérhető**és **hamarosan lejáró** számokat.

    ![szolgáltatások lap, a szolgáltatási licencszerződésekkel és a kapcsolódó licenccel kapcsolatos információkkal](media/license-users-groups/license-products-blade-with-products.png)

1. Válassza ki a csomag nevét a licenccel rendelkező felhasználók és csoportok megtekintéséhez.

## <a name="assign-licenses-to-users-or-groups"></a>Licencek hozzárendelése a felhasználókhoz vagy csoportokhoz

Győződjön meg arról, hogy bárki használja egy licencelt kellene az Azure AD szolgáltatás rendelkezik a megfelelő licenccel. A licencelési jogosultságokat hozzáadhatja a felhasználókhoz vagy egy teljes csoporthoz is.

### <a name="to-assign-a-license-to-a-user"></a>Licenc kiosztása egy felhasználóhoz

1. A **termékek** lapon válassza ki a felhasználóhoz hozzárendelni kívánt licencelési csomag nevét.

    ![szolgáltatások lap, a Kiemelt szolgáltatási licencelési csomaggal](media/license-users-groups/license-products-blade-with-product-highlight.png)

1. A licencelési terv áttekintése lapon válassza a **hozzárendelés**lehetőséget.

    ![szolgáltatások lap, Kiemelt hozzárendelési lehetőséggel](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

1. Az a **hozzárendelése** lapon jelölje be **felhasználók és csoportok**, és keressen rá, és válassza ki a felhasználót, még a licenc hozzárendelésével.

    ![Licenc oldala, amelyen a kiemelt keresést és választhat hozzárendelése](media/license-users-groups/assign-license-blade-with-highlight.png)

1. Válassza ki **hozzárendelési beállítások**, ellenőrizze, hogy a megfelelő licencet a beállítások engedélyezve van, és válassza ki **OK**.

    ![Licencelési lehetőség lap, a licencelési csomagban elérhető összes lehetőséggel](media/license-users-groups/license-option-blade-assignments.png)

    A **licenc hozzárendelése** frissítések jeleníti meg, hogy egy felhasználó van kiválasztva, valamint, hogy vannak-e konfigurálva a hozzárendeléseket lapon.

    > [!NOTE]
    > Az összes hely nem minden Microsoft-szolgáltatások érhetők el. Mielőtt egy úgy lehet licencet a felhasználóhoz, meg kell adnia a **a felhasználási hely**. Ez az érték állítható be a **Azure Active Directory &gt; felhasználók &gt; profil &gt; beállítások** terület az Azure ad-ben. Minden olyan felhasználó, akinek a használati helye nincs megadva, örökli az Azure AD-szervezet helyét.

1. Válassza a **Hozzárendelés** elemet.

    A felhasználó bekerül a licenccel rendelkező felhasználók listáját, és a benne foglalt hozzáféréssel rendelkezik az Azure AD-szolgáltatások.

### <a name="to-assign-a-license-to-a-group"></a>Licenc társítása egy csoporthoz

1. A **termékek** lapon válassza ki a felhasználóhoz hozzárendelni kívánt licencelési csomag nevét.

    ![Termékek panel, Kiemelt termék licencelési csomaggal](media/license-users-groups/license-products-blade-with-product-highlight.png)

1. Az a **Azure Active Directory Premium 2. csomag** lapon jelölje be **hozzárendelése**.

    ![Termékek oldala, amelyen kiemelt hozzárendelése lehetőség](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

1. Az a **hozzárendelése** lapon jelölje be **felhasználók és csoportok**, és keressen rá, és válassza ki a csoportot, akkor még hozzárendelése a licenc.

    ![Licenc oldala, amelyen a kiemelt keresést és választhat hozzárendelése](media/license-users-groups/assign-group-license-blade-with-highlight.png)

1. Válassza ki **hozzárendelési beállítások**, ellenőrizze, hogy a megfelelő licencet a beállítások engedélyezve van, és válassza ki **OK**.

    ![Licencelési lehetőség lap, a licencelési csomagban elérhető összes lehetőséggel](media/license-users-groups/license-option-blade-group-assignments.png)

    A **licenc hozzárendelése** frissítések jeleníti meg, hogy egy felhasználó van kiválasztva, valamint, hogy vannak-e konfigurálva a hozzárendeléseket lapon.

1. Válassza a **Hozzárendelés** elemet.

    A csoport bekerül a licenccel rendelkező csoportok listáját, és minden tagot fér hozzá a csomagban foglalt Azure AD-szolgáltatások.

## <a name="remove-a-license"></a>Licenc eltávolítása

A licenceket eltávolíthatja egy felhasználó Azure AD-felhasználó oldaláról, a csoport áttekintés lapjáról, vagy az Azure AD- **licencek** lapról, és megtekintheti a licenchez tartozó felhasználókat és csoportokat.

### <a name="to-remove-a-license-from-a-user"></a>Licenc eltávolítása a felhasználótól

1. A szolgáltatási csomag **licenccel rendelkező felhasználók** lapján válassza ki azt a felhasználót, akinek már nem lesz a licence. Ha például _Alain Charon_.

1. Válassza ki **Remove licenc**.

    ![A licenccel rendelkező felhasználók oldal az Eltávolítás licenc lehetőség kiemelésével](media/license-users-groups/license-products-user-blade-with-remove-option-highlight.png)

> [!IMPORTANT]
> A felhasználó által egy csoporttól örökölt licencek nem távolíthatók el közvetlenül. Ehelyett lehetősége van a felhasználó eltávolítása a csoportból, amelyről azokat Ön örökli a licenc.

### <a name="to-remove-a-license-from-a-group"></a>Licenc eltávolítása egy csoportból

1. A licencelési csomag **licencelt csoportok** lapján válassza ki azt a csoportot, amelynek már nem lesz a licence.

1. Válassza ki **Remove licenc**.

    ![A Remove-licenc opció kiemelésével licenccel rendelkező csoportok lap](media/license-users-groups/license-products-group-blade-with-remove-option-highlight.png)

## <a name="next-steps"></a>További lépések

Miután hozzárendelte a licenceket, végezheti el a következő eljárásokat:

- [Azonosítani és megoldani a licenc-hozzárendelési problémák](../users-groups-roles/licensing-groups-resolve-problems.md)

- [A licenccel rendelkező felhasználók felvétele egy csoportba licenckezeléshez](../users-groups-roles/licensing-groups-migrate-users.md)

- [Forgatókönyvek, korlátait és ismert problémák csoportok használata kezelheti az Azure Active Directory licencelése](../users-groups-roles/licensing-group-advanced.md)

- [Profil adatok hozzáadása vagy módosítása](active-directory-users-profile-azure-portal.md)
