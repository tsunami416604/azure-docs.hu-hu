---
title: Licencek hozzárendelése vagy eltávolítása – Azure Active Directory | Microsoft dokumentumok
description: Az Azure Active Directory-licencek hozzárendelésére vagy eltávolítására vonatkozó utasítások a felhasználóktól vagy csoportoktól.
services: active-directory
author: msaburnley
manager: daveba
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 20ec1d1909a53a9de29e12be33957acfd1643698
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128824"
---
# <a name="assign-or-remove-licenses-in-the-azure-active-directory-portal"></a>Licencek hozzárendelése vagy eltávolítása az Azure Active Directory portálon

Számos Azure Active Directory (Azure AD) szolgáltatás megköveteli, hogy az adott szolgáltatás hoz tartozó felhasználókat vagy csoportokat (és a társított tagokat) licencelje. Csak az aktív licenccel rendelkező felhasználók férhetnek hozzá és használhatják azokat a licencelt Azure AD-szolgáltatásokat, amelyekre ez igaz. A licencek bérlőnként kerülnek alkalmazásra, és nem kerülnek át más bérlőkre. 

## <a name="available-license-plans"></a>Elérhető licenccsomagok

Az Azure AD szolgáltatáshoz számos licenccsomag érhető el, többek között:

- Azure AD Free

- Prémium szintű Azure AD P1

- Prémium szintű Azure AD P2

Az egyes licenccsomagokkal és a kapcsolódó licencelési részletekkel kapcsolatos konkrét információt a [Milyen licencre van szükségem.](https://azure.microsoft.com/pricing/details/active-directory/)

Nem minden Microsoft-szolgáltatás érhető el minden helyen. Mielőtt egy licencet hozzá rendelhetne egy csoporthoz, meg kell adnia az összes tag **használati helyét.** Ezt az értéket az **Azure &gt; Active &gt; &gt; Directory – felhasználói profilbeállítások** területen állíthatja be az Azure AD-ben. Minden olyan felhasználó, akinek a használati helye nincs megadva örökli az Azure AD-szervezet helyét.

## <a name="view-license-plans-and-plan-details"></a>Licenccsomagok és tervrészletek megtekintése

Megtekintheti az elérhető szolgáltatási csomagokat, beleértve az egyes licenceket, ellenőrizheti a függőben lévő lejárati dátumokat, és megtekintheti az elérhető hozzárendelések számát.

### <a name="to-find-your-service-plan-and-plan-details"></a>A szolgáltatási csomag és a csomag részleteinek megkeresése

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com/) egy licencrendszergazdai fiók használatával az Azure AD-szervezetben.

1. Válassza az **Azure Active Directory**, majd a **Licencek**lehetőséget.

    ![Licencek lap a megvásárolt szolgáltatások és a hozzárendelt licencek számával](media/license-users-groups/license-details-blade.png)

1. A **Megvásárolt** hivatkozással megtekintheti a **Termékek** lapot, és megtekintheti a **Licencelicsomagok Hozzárendelt**, **Elérhető**és **Hamarosan lejáró** számait.

    ![szolgáltatások lap, szervizlicenc-csomagokkal és kapcsolódó licencadatokkal](media/license-users-groups/license-products-blade-with-products.png)

1. Válasszon ki egy tervnevet a licencelt felhasználók és csoportok megtekintéséhez.

## <a name="assign-licenses-to-users-or-groups"></a>Licencek hozzárendelése felhasználókhoz vagy csoportokhoz

Győződjön meg arról, hogy bárki, akinek szüksége van egy licencelt Azure AD-szolgáltatás használatára, rendelkezik a megfelelő licenccel. A licencelési jogokat hozzáadhatja a felhasználókhoz vagy egy teljes csoporthoz.

### <a name="to-assign-a-license-to-a-user"></a>Licenc hozzárendelése felhasználóhoz

1. A **Termékek** lapon válassza ki a felhasználóhoz rendelni kívánt licenccsomag nevét.

    ![szolgáltatások lap, kiemelt szolgáltatási licenccsomaggal](media/license-users-groups/license-products-blade-with-product-highlight.png)

1. A licencterv áttekintése lapon válassza a **Hozzárendelés lehetőséget.**

    ![szolgáltatások lap, kiemelt Hozzárendelés beállítással](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

1. A **Hozzárendelés** lapon válassza a **Felhasználók és csoportok**lehetőséget, majd keresse meg és jelölje ki a licencet hozzárendelő felhasználót.

    ![Licenclap hozzárendelése kiemelt kereséssel és Beállítások megadása](media/license-users-groups/assign-license-blade-with-highlight.png)

1. Válassza a **Hozzárendelési beállítások lehetőséget,** győződjön meg arról, hogy be van kapcsolva a megfelelő licencbeállítások, majd kattintson az **OK gombra.**

    ![Licenc opciólap, a licenccsomagban elérhető összes lehetőséggel](media/license-users-groups/license-option-blade-assignments.png)

    A **Licenclap frissítéseinek hozzárendelése** azt mutatja, hogy a felhasználó ki van jelölve, és hogy a hozzárendelések konfigurálva vannak.

    > [!NOTE]
    > Nem minden Microsoft-szolgáltatás érhető el minden helyen. Ahhoz, hogy egy licencet hozzá lehessen rendelni egy felhasználóhoz, meg kell **adnia**a Használati helyet . Ezt az értéket az **Azure &gt; Active &gt; &gt; Directory – felhasználói profilbeállítások** területen állíthatja be az Azure AD-ben. Minden olyan felhasználó, akinek a használati helye nincs megadva örökli az Azure AD-szervezet helyét.

1. Válassza a **Hozzárendelés** elemet.

    A felhasználó hozzáadódik a licencelt felhasználók listájához, és hozzáfér a mellékelt Azure AD-szolgáltatásokhoz.
    > [!NOTE]
    > A licencek közvetlenül a felhasználóhoz is hozzárendelhetők a felhasználó **Licencek** lapjáról. Ha egy felhasználó csoporttagságon keresztül rendelt licenccel rendelkezik, és ugyanazt a licencet közvetlenül a felhasználóhoz szeretné rendelni, akkor csak **az** 1.

### <a name="to-assign-a-license-to-a-group"></a>Licenc hozzárendelése csoporthoz

1. A **Termékek** lapon válassza ki a felhasználóhoz rendelni kívánt licenccsomag nevét.

    ![Termékek panel, kiemelt terméklicenc-csomaggal](media/license-users-groups/license-products-blade-with-product-highlight.png)

1. Az **Azure Active Directory Premium Plan 2** lapon válassza a Hozzárendelés **lehetőséget.**

    ![Termékek lap kiemelt Hozzárendelés beállítással](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

1. A **Hozzárendelés** lapon válassza a **Felhasználók és csoportok**lehetőséget, majd keresse meg és jelölje ki a licencet hozzárendelő csoportot.

    ![Licenclap hozzárendelése kiemelt kereséssel és Beállítások megadása](media/license-users-groups/assign-group-license-blade-with-highlight.png)

1. Válassza a **Hozzárendelési beállítások lehetőséget,** győződjön meg arról, hogy be van kapcsolva a megfelelő licencbeállítások, majd kattintson az **OK gombra.**

    ![Licenc opciólap, a licenccsomagban elérhető összes lehetőséggel](media/license-users-groups/license-option-blade-group-assignments.png)

    A **Licenclap frissítéseinek hozzárendelése** azt mutatja, hogy a felhasználó ki van jelölve, és hogy a hozzárendelések konfigurálva vannak.

1. Válassza a **Hozzárendelés** elemet.

    A csoport hozzáadódik a licencelt csoportok listájához, és az összes tag hozzáférhet a mellékelt Azure AD-szolgáltatásokhoz.

## <a name="remove-a-license"></a>Licenc eltávolítása

Eltávolíthat egy licencet egy felhasználó Azure AD felhasználói lapjáról, a csoportáttekintő lapról egy csoport-hozzárendeléshez, vagy az Azure AD **licencek** lapról kiindulva megtekintheti a licenc felhasználóiés csoportjait.

### <a name="to-remove-a-license-from-a-user"></a>Licenc eltávolítása egy felhasználótól

1. A szolgáltatási csomag **Licencelt felhasználók** lapján válassza ki azt a felhasználót, amelyiknek már nem kellene licenccel rendelkeznie. _Például, Alain Charon_.

1. Válassza **a Licenc eltávolítása**lehetőséget.

    ![A licencelt felhasználók lap kiemelt Licenc eltávolítása beállítással](media/license-users-groups/license-products-user-blade-with-remove-option-highlight.png)

> [!IMPORTANT]
> A felhasználók által egy csoportból örökölt licencek nem távolíthatók el közvetlenül. Ehelyett el kell távolítania a felhasználót abból a csoportból, amelyből a licencet öröklik.

### <a name="to-remove-a-license-from-a-group"></a>Licenc eltávolítása csoportból

1. A licenccsomag **Licencelt csoportok** lapján válassza ki azt a csoportot, amelynek már nem kellene licenccel rendelkeznie.

1. Válassza **a Licenc eltávolítása**lehetőséget.

    ![Licencelt csoportok lap kiemelt Licenc eltávolítása beállítással](media/license-users-groups/license-products-group-blade-with-remove-option-highlight.png)
    
    > [!NOTE]
    > Ha egy helyszíni felhasználói fiók az Azure AD-vel szinkronizált kiesik a szinkron hatókörén, vagy a szinkronizálás eltávolításakor a felhasználó helyreállíthatóan törlődik az Azure AD-ben. Ebben az esetben a felhasználóhoz közvetlenül vagy csoportalapú licencelésen keresztül hozzárendelt licencek nem törölt, **hanem felfüggesztettként** lesznek **megjelölve.**

## <a name="next-steps"></a>További lépések

Miután hozzárendelte a licenceket, a következő folyamatokat hajthatja végre:

- [Licenchozzárendelési problémák azonosítása és megoldása](../users-groups-roles/licensing-groups-resolve-problems.md)

- [Licencelt felhasználók hozzáadása csoporthoz licenceléshez](../users-groups-roles/licensing-groups-migrate-users.md)

- [Forgatókönyvek, korlátozások és ismert problémák, amelyek csoportok használatával kezelik a licencelést az Azure Active Directoryban](../users-groups-roles/licensing-group-advanced.md)

- [Profiladatok hozzáadása vagy módosítása](active-directory-users-profile-azure-portal.md)
