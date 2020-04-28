---
title: Hogyan jelennek meg az alkalmazások a hozzáférési panelen | Microsoft Docs
description: A hozzáférési panelen megjelenő alkalmazások okának megoldása
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: mimart
ms.reviewr: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fa8ea75cc7fda05326c802c25a91d025b66b5ce
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "65784416"
---
# <a name="how-applications-appear-on-the-access-panel"></a>Hogyan jelennek meg az alkalmazások a hozzáférési panelen

A hozzáférési panel egy webalapú portál, amely lehetővé teszi, hogy a felhasználó munkahelyi vagy iskolai fiókkal Azure Active Directory (Azure AD) megtekintse és indítsa el azokat a felhőalapú alkalmazásokat, amelyekhez az Azure AD-rendszergazda hozzáférést kapott. Ezek az alkalmazások az Azure AD-portálon a felhasználó nevében konfigurálhatók. A rendszergazda közvetlenül vagy egy felhasználó számára is kiépítheti az alkalmazást a felhasználó számára, hogy az alkalmazás megjelenjen a felhasználó hozzáférési paneljén.

## <a name="general-issues-to-check-first"></a>Általános problémák az első kereséshez

-   Ha egy alkalmazás egy olyan felhasználótól vagy csoporttól lett eltávolítva, amely tagja a felhasználónak, néhány perc múlva próbálja meg bejelentkezni a felhasználó hozzáférési paneljére, és ellenőrizze, hogy az alkalmazás el lett-e távolítva.

-   Ha egy licencet eltávolítottak egy felhasználónak vagy csoportnak, akkor a felhasználó tagja ennek a csoportnak, amely a módosítások elvégzéséhez szükséges mérettől és összetettségtől függően hosszú időt vehet igénybe. A hozzáférési panelre való bejelentkezés előtt engedélyezze a további időt.

## <a name="problems-related-to-assigning-applications-to-users"></a>Alkalmazások felhasználókhoz való hozzárendelésével kapcsolatos problémák

Előfordulhat, hogy egy felhasználó egy alkalmazást lát a hozzáférési panelen, mert korábban hozzá lett rendelve. A következő módszerekkel ellenőrizhető:

-   [Ellenőrizze, hogy van-e hozzárendelve felhasználó az alkalmazáshoz](#check-if-a-user-is-assigned-to-the-application)

-   [Ellenőrizze, hogy a felhasználó rendelkezik-e az alkalmazáshoz kapcsolódó licenccel](#check-if-a-user-is-under-a-license-related-to-the-application)


### <a name="check-if-a-user-is-assigned-to-the-application"></a>Ellenőrizze, hogy van-e hozzárendelve felhasználó az alkalmazáshoz

Az alábbi lépéseket követve ellenőrizheti, hogy van-e hozzárendelve egy felhasználó az alkalmazáshoz:

1. Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4. kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.

5. kattintson a **minden alkalmazás** elemre az alkalmazások listájának megtekintéséhez.

6. **Keresse** meg a kérdéses alkalmazás nevét.

7. kattintson **a felhasználók és csoportok**elemre.

8. Ellenőrizze, hogy a felhasználó hozzá van-e rendelve az alkalmazáshoz.

   * Ha el szeretné távolítani a felhasználót az alkalmazásból, **kattintson a felhasználó sorára** , majd válassza a **Törlés**lehetőséget.

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>Ellenőrizze, hogy a felhasználó rendelkezik-e az alkalmazáshoz kapcsolódó licenccel

A felhasználóhoz hozzárendelt licencek vizsgálatához kövesse az alábbi lépéseket:

1. Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4. a navigációs menüben kattintson a **felhasználók és csoportok** elemre.

5. kattintson **a minden felhasználó**elemre.

6. **Keresse** meg az Önt érdeklő felhasználót, és **kattintson a** kijelölni kívánt sorra.

7. a **licencek** elemre kattintva megtekintheti, hogy a felhasználó mely licenceket rendeli hozzá jelenleg.

   * Ha a felhasználó egy Office-licenchez van rendelve, ez lehetővé teszi, hogy az első felek Office-alkalmazásai megjelenjenek a felhasználó hozzáférési paneljén.

## <a name="problems-related-to-assigning-applications-to-groups"></a>Alkalmazások csoportokhoz való hozzárendelésével kapcsolatos problémák

Előfordulhat, hogy egy felhasználó egy alkalmazást lát a hozzáférési panelen, mert egy olyan csoport részét képezik, amely hozzá lett rendelve az alkalmazáshoz. A következő módszerekkel ellenőrizhető:

-   [Felhasználó csoport-tagságának keresése](#check-a-users-group-memberships)

-   [Ellenőrizze, hogy a felhasználó tagja-e egy licenchez rendelt csoportnak](#check-if-a-user-is-a-member-of-a-group-assigned-to-a-license)

### <a name="check-a-users-group-memberships"></a>Felhasználó csoport-tagságának keresése

A csoport tagságának vizsgálatához kövesse az alábbi lépéseket:

1. Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4. a navigációs menüben kattintson a **felhasználók és csoportok** elemre.

5. kattintson **a minden felhasználó**elemre.

6. **Keresse** meg az Önt érdeklő felhasználót, és **kattintson a** kijelölni kívánt sorra.

7. kattintson a csoportok elemre **.**

8. Ellenőrizze, hogy a felhasználó tagja-e az alkalmazáshoz rendelt csoportnak.

   * Ha el szeretné távolítani a felhasználót a csoportból, **kattintson a csoport sorára** , majd válassza a Törlés lehetőséget.

### <a name="check-if-a-user-is-a-member-of-a-group-assigned-to-a-license"></a>Ellenőrizze, hogy a felhasználó tagja-e egy licenchez rendelt csoportnak

1. Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4. a navigációs menüben kattintson a **felhasználók és csoportok** elemre.

5. kattintson **a minden felhasználó**elemre.

6. **Keresse** meg az Önt érdeklő felhasználót, és **kattintson a** kijelölni kívánt sorra.

7. kattintson a csoportok elemre **.**

8. kattintson egy adott csoport sorára.

9. kattintson a **licencek** elemre, és ellenőrizze, hogy a csoport melyik licencekhez van rendelve.

   * Ha a csoport egy Office-licenchez van rendelve, akkor előfordulhat, hogy bizonyos első féltől származó Office-alkalmazások megjelennek a felhasználó hozzáférési paneljén.


## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Ha ezek a hibaelhárítási lépések nem a probléma megoldására szolgálnak

Ha elérhető, nyisson meg egy támogatási jegyet a következő információkkal:

-   Korrelációs hiba azonosítója

-   UPN (felhasználói e-mail-cím)

-   Bérlőazonosító

-   Böngésző típusa

-   Az időzóna és az idő/időkeret a hiba bekövetkezésekor

-   Hegedűs nyomkövetései

## <a name="next-steps"></a>További lépések
[Alkalmazások kezelése Azure Active Directory](what-is-application-management.md)
