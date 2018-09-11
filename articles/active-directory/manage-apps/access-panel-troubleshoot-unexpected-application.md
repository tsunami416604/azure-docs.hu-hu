---
title: Hogyan alkalmazások jelennek meg a hozzáférési panelen |} A Microsoft Docs
description: Egy alkalmazás jelenik-e meg a hozzáférési panelen okozó hibák elhárításához
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewr: japere
ms.openlocfilehash: 85e0eccf8ed30f5fc91bd892463fe6f1bd835d75
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44356627"
---
# <a name="how-applications-appear-on-the-access-panel"></a>Hogyan alkalmazások jelennek meg a hozzáférési panelen

A hozzáférési panelen egy webes portál, amely lehetővé teszi, hogy egy felhasználó munkahelyi vagy iskolai fiókkal az Azure Active Directoryban (Azure AD), megtekintheti, és indítsa el a felhőalapú alkalmazásokat, hogy az Azure AD-rendszergazda hozzáférést adott őket. Ezeket az alkalmazásokat úgy vannak konfigurálva, az Azure AD portálon a felhasználó nevében. A rendszergazdák üzembe helyezhetnek a felhasználónak az alkalmazás közvetlenül, vagy csoporthoz egy felhasználó tagja eredményez az alkalmazás a felhasználó hozzáférési panelen jelennek meg.

## <a name="general-issues-to-check-first"></a>Először ellenőrizze a általános problémák

-   Ha egy alkalmazás egy felhasználót vagy csoportot, a felhasználó a tagja, el lett távolítva, próbáljon meg bejelentkezni be és ki újra a felhasználó hozzáférési Panel be néhány perc múlva megtekintheti, ha a rendszer eltávolítja az alkalmazást.

-   Ha egy licencet a felhasználó vagy csoport el lett távolítva az a felhasználó nem tagja ennek a mérete és összetettsége kell tenni a módosításokat a csoport hosszú időt is igénybe vehet. Lehetővé teszi további időpont előtt jelentkezzen be a hozzáférési panelen.

## <a name="problems-related-to-assigning-applications-to-users"></a>A felhasználók számára alkalmazások hozzárendelése kapcsolatos problémák

A felhasználó lehet annak, hogy egy alkalmazás a hozzáférési panelen láthatnak, mert azok volt korábban hozzárendelt. Az alábbiakban néhány módszer ellenőrzése:

-   [Ha egy felhasználó hozzá van rendelve az alkalmazás ellenőrzése](#check-if-a-user-is-assigned-to-the-application)

-   [Ellenőrizze, hogy van-e egy felhasználó egy licenc, az alkalmazással kapcsolatos](#check-if-a-user-is-under-a-license-related-to-the-application)


### <a name="check-if-a-user-is-assigned-to-the-application"></a>Ha egy felhasználó hozzá van rendelve az alkalmazás ellenőrzése

Ha egy felhasználó hozzá van rendelve az alkalmazás ellenőrzéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

6.  **Keresés** a kérdéses alkalmazás neve.

7.  Kattintson a **felhasználók és csoportok**.

8.  Ellenőrizze, hogy ha a felhasználó az alkalmazáshoz van hozzárendelve.

  * Ha a felhasználó eltávolítja az alkalmazásból, **kattintson az adott sorra** a felhasználó és a select **törlése**.

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>Ellenőrizze, hogy van-e egy felhasználó egy licenc, az alkalmazással kapcsolatos

A felhasználó hozzárendelt licencek ellenőrzéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **minden felhasználó**.

6.  **Keresés** az Önt érdeklő felhasználó és **kattintson az adott sorra** kiválasztásához.

7.  Kattintson a **licencek** megtekintéséhez, amelyek licencek, a felhasználó jelenleg hozzá van rendelve.

   * Ha a felhasználó hozzá van rendelve egy Office-licencet, ez lehetővé teszi a felhasználó hozzáférési panelen megjelenő első fél Office alkalmazások.

## <a name="problems-related-to-assigning-applications-to-groups"></a>Az alkalmazások csoportokhoz való hozzárendelésével kapcsolatos problémák

A felhasználó lehet annak, hogy egy alkalmazás a hozzáférési panelen láthatnak, mert egy csoportot, amely az alkalmazás részét képezik. Az alábbiakban néhány módszer ellenőrzése:

-   [A vendégfelhasználói csoporttagságok ellenőrzése](#check-a-users-group-memberships)

-   [Ellenőrizze, hogy ha egy felhasználó tagja-e egy licencet rendelt csoport](#check-if-a-user-is-a-member-of-a-group-assigned-to-a-license)

### <a name="check-a-users-group-memberships"></a>A vendégfelhasználói csoporttagságok ellenőrzése

Ellenőrizze a csoport tagságát, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **minden felhasználó**.

6.  **Keresés** az Önt érdeklő felhasználó és **kattintson az adott sorra** kiválasztásához.

7.  Kattintson a **csoportok.**

8.  Ellenőrizze, hogy ha a felhasználó az alkalmazáshoz hozzárendelt csoport része.

   * Ha azt szeretné, a felhasználó eltávolítása a csoportból, **kattintson az adott sorra** , a csoport- és válassza a törlés.

### <a name="check-if-a-user-is-a-member-of-a-group-assigned-to-a-license"></a>Ellenőrizze, hogy ha egy felhasználó tagja-e egy licencet rendelt csoport

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **minden felhasználó**.

6.  **Keresés** az Önt érdeklő felhasználó és **kattintson az adott sorra** kiválasztásához.

7.  Kattintson a **csoportok.**

8.  Kattintson az adott csoportban sorát.

9.  Kattintson a **licencek** megtekintéséhez, amely licencek, a csoporthoz van hozzárendelve.

  * A csoport hozzá van rendelve egy Office-licencet, ha ez lehetséges, hogy lehetővé teszik az bizonyos első fél Office alkalmazások jelennek meg a felhasználó hozzáférési paneljén.


## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Ha ezeket a hibaelhárítási lépéseket végre nem a hárítsa el a problémát

Nyisson meg egy támogatási jegyet a következő adatokat, ha rendelkezésre áll:

-   Megfelelési hiba azonosítója

-   Egyszerű felhasználónév (felhasználó e-mail-címe)

-   Bérlőazonosító

-   Böngésző típusa

-   Időzóna és idő/időkeret során hiba történik.

-   Fiddler-nyomkövetés

## <a name="next-steps"></a>További lépések
[Alkalmazások kezelése az Azure Active Directoryval](what-is-application-management.md)
