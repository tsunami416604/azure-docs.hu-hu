---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
author: rolyon
ms.service: active-directory
ms.topic: include
ms.date: 05/16/2019
ms.author: rolyon
ms.custom: include file
ms.openlocfilehash: 6711506c1e489dcbd50aedd36241affc3bbed80b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66113392"
---
### <a name="policy-for-users-in-your-directory"></a>Házirend: A címtárban lévő felhasználók számára

Kövesse az alábbi lépéseket, ha azt szeretné, hogy a felhasználók és csoportok a címtárban kérhetnek hozzáférést csomag házirend.

1. Az a **felhasználók, akik hozzáférést** szakaszban jelölje be **a felhasználók számára a címtár**.

1. Az a **válassza ki a felhasználók és csoportok** területén kattintson **felhasználók és csoportok hozzáadása**.

1. A kiválasztott felhasználók és csoportok panelen válassza ki a felhasználókat és csoportokat szeretne hozzáadni.

    ![Access-package - válassza ki a házirend - felhasználók és csoportok](./media/active-directory-entitlement-management-policy/policy-select-users-groups.png)

1. Kattintson a **kiválasztása** a felhasználók és csoportok hozzáadása.

1. Ugorjon le a [házirend: Kérelem](#policy-request) szakaszban.

### <a name="policy-for-users-not-in-your-directory"></a>Házirend: Nem a címtárban található felhasználók számára

Ha azt szeretné, hogy a házirend nem található a könyvtár kérhet hozzáférést csomag felhasználók számára, kövesse az alábbi lépéseket. Könyvtárak engedélyezettek kell állítani a **szervezeti kapcsolatok együttműködési korlátozások** beállításait.

> [!NOTE]
> A Vendég felhasználói fiókhoz létrejön egy felhasználó még nem található a címtárban, amelynek a kérés jóváhagyása vagy automatikusan elfogadja. A Vendég meghívjuk, de nem a meghívó e-mailt fog kapni. Ehelyett azokat a rendszer küld egy e-mailt a hozzáférési csomagok hozzárendelésével kézbesíti a rendszer. Alapértelmezés szerint újabb, ha a Vendég felhasználó nem rendelkezik minden olyan hozzáférési csomag hozzárendelések azok utolsó hozzárendelés lejárt, vagy meg lett szakítva, mert, hogy Vendég felhasználói fiókhoz bejelentkezés blokkolva lesznek, és törölni. Ha szeretné a könyvtárban határozatlan ideig maradnak vendég felhasználók rendelkeznek, még akkor is, ha nincsenek hozzáférési csomag hozzárendelések rendelkeznek, módosíthatja a beállításokat a jogosultság felügyeleti konfiguráció.

1. Az a **felhasználók, akik hozzáférést** szakaszban jelölje be **nem a címtárban található felhasználók**.

1. Az a **válassza ki azt az Azure AD külső könyvtár** területén kattintson **könyvtárak hozzáadása**.

1. Adjon meg egy tartománynevet és a egy külső keresése az Azure AD-címtárban.

1. Ellenőrizze, hogy a helyes-e a megadott könyvtár neve és a kezdeti tartomány könyvtár.

    > [!NOTE]
    > Minden felhasználó a könyvtárból a hozzáférés csomag igénylést lesz. Ez magában foglalja a felhasználók a címtárban, nem csak a használt keresési tartomány társított valamennyi altartományt.

    ![Access-package - válassza ki a házirend - könyvtárak](./media/active-directory-entitlement-management-policy/policy-select-directories.png)

1. Kattintson a **Hozzáadás** hozzáadása a címtárban.

1. Ismételje meg ezt a lépést minden további könyvtárak hozzáadása.

1. Minden címtár hozzáadását követően szeretné bele a szabályzatba, kattintson a **kiválasztása**.

1. Ugorjon le a [házirend: Kérelem](#policy-request) szakaszban.

### <a name="policy-none-administrator-direct-assignments-only"></a>Házirend: Egyik sem (rendszergazdai közvetlen hozzárendelések csak)

Kövesse az alábbi lépéseket, ha azt szeretné, hogy a szabályzat megkerüljék a hozzáférést, és lehetővé teszi a rendszergazdák számára, hogy közvetlenül az adott felhasználók hozzárendelése a hozzáférés-csomag. Felhasználók sem kell kérni a hozzáférés-csomag. Lejárati beállítások továbbra is beállíthatja, de nem kérelem beállításokat.

1. Az a **felhasználók, akik hozzáférést** szakaszban jelölje be **None (rendszergazda csak a közvetlen hozzárendelések**.

    Követően a hozzáférés csomagot hoz létre, hozzáférési csomag közvetlenül hozzárendelhet adott belső és külső felhasználókhoz. Ha egy külső felhasználót ad meg, a Vendég felhasználói fiókhoz a címtár létrejön.

1. Ugorjon le a [házirend: Lejárati](#policy-expiration) szakaszban.

### <a name="policy-request"></a>Házirend: Kérés

A kérelem a szakaszban megadhatja jóváhagyási beállítások, amikor a felhasználók a hozzáférés csomagban kérik.

1. A kijelölt felhasználók kéréseit jóváhagyás szükséges, állítsa be a **jóváhagyás megkövetelése,** kapcsolót **Igen**. Kérelmek jóváhagyása automatikus rendelkezik, állítsa be a váltógomb **nem**.

1. Ha szüksége van a jóváhagyást a **jóváhagyók kiválasztása** területén kattintson **jóváhagyók hozzáadása**.

1. Válassza ki a jóváhagyók ablaktábláján egy vagy több felhasználók és csoportok kiválasztása a jóváhagyók lennie.

    Csak az egyik a kijelölt jóváhagyók jóvá kell hagynia a kérelmet. Az összes a jóváhagyók a jóváhagyási, nem szükséges. A jóváhagyó döntésével bármelyik jóváhagyó felülvizsgálja a kérést először alapul.

    ![Access-package - szabályzat – válassza ki a jóváhagyók](./media/active-directory-entitlement-management-policy/policy-select-approvers.png)

1. Kattintson a **kiválasztása** a jóváhagyók hozzáadása.

1. Kattintson a **Speciális kérelem** további beállítások megjelenítéséhez.

    ![Access-package - válassza ki a házirend - könyvtárak](./media/active-directory-entitlement-management-policy/policy-advanced-request.png)

1. Állítsa be a felhasználóknak indokolniuk kérése a hozzáférés csomag kötelező, **indoklás megkövetelése** való **Igen**.

1. A jóváhagyó jóváhagyja a kérést a hozzáférés-csomag indokolniuk van szükség, állítsa **jóváhagyó indoklás megkövetelése** való **Igen**.

1. Az a **jóváhagyási kérés időtúllépése (nap)** adja meg, mennyi ideig kell a jóváhagyók tekintse át a kérést. Ha nincsenek jóváhagyók az ez idő alatt tekintse át, a kérelem lejár, és a hozzáférés-csomag egy másik kérelmet küldeni a felhasználónak kell.

### <a name="policy-expiration"></a>Házirend: lejárati

A lejárati szakaszban megadhatja, amikor a felhasználó-hozzárendelés a hozzáférés csomag érvényessége lejár.

1. Az a **lejárati** szakaszában **hozzáférés csomag lejár** való **dátumon**, **napok száma**, vagy **soha**.

    A **dátumon**, válassza ki a lejárati dátumot a jövőben.

    A **napok száma**, 0 és 3660 nap közötti számot adjon meg.

    A választott beállítás alapján a felhasználó-hozzárendelés a hozzáférés csomag lejár egy adott dátumon egy bizonyos számú nap, miután jóváhagyták őket, vagy soha nem.

1. Kattintson a **speciális lejárati beállítások megjelenítése** további beállítások megjelenítéséhez.

1. Állítsa be úgy, hogy a felhasználó a hozzárendeléseket kiterjeszteni, **engedélyezése a felhasználók számára a hozzáférés kiterjesztése** való **Igen**.

    Bővítmények a szabályzat engedélyezett, ha a felhasználó kap egy e-mailt 14 napos és szintén 1 nap, a hozzáférési csomagok hozzárendelésével lejár beállítása előtt kérő üzenet őket a hozzárendelés kiterjesztésére.

    ![Access-package - házirend - lejárati beállítások](./media/active-directory-entitlement-management-policy/policy-expiration.png)

### <a name="policy-enable-policy"></a>Házirend: Szabályzat engedélyezése

1. Ha azt szeretné, hogy a hozzáférés-csomag a házirendben a felhasználókat azonnal rendelkezésére bocsátani, kattintson a **Igen** szabályzatának engedélyezéséhez.

    Mindig engedélyezheti azt a jövőben a hozzáférés-csomag létrehozásának befejezése után.

    ![Access-package - házirend engedélyezése a házirend-beállítással](./media/active-directory-entitlement-management-policy/policy-enable.png)

1. Kattintson a **tovább** vagy **létrehozása**.
