---
title: Alkalmazások megjelenésének a hozzáférési panel |} Microsoft Docs
description: Ezért az alkalmazás jelenik-e meg a hozzáférési panelen hibaelhárítása
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.reviewr: japere
ms.openlocfilehash: 0d4203ea39adf027d783a482198f523e18cbc246
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
---
# <a name="how-applications-appear-on-the-access-panel"></a>Alkalmazások megjelenésének a hozzáférési panel

A hozzáférési Panel egy webes portál, amely lehetővé teszi, hogy a felhasználó munkahelyi vagy iskolai fiókkal az Azure Active Directory (Azure AD) a megtekintése, és indítsa el a felhőalapú alkalmazások, hogy az Azure AD-rendszergazda engedélyezte őket hozzáférés. Ezeket az alkalmazásokat úgy vannak konfigurálva, az Azure AD portálon a felhasználó nevében. A rendszergazda közvetlenül hozhat létre az alkalmazás a felhasználó vagy csoport számára a felhasználó, ami azt eredményezi, az alkalmazás a felhasználó hozzáférési Panel szereplő.

## <a name="general-issues-to-check-first"></a>Először ellenőrizze a általános problémák

-   Ha egy alkalmazás egy felhasználó vagy a felhasználó tagja csoport el lett távolítva, próbáljon meg bejelentkezni és újra a felhasználó hozzáférési panelre néhány perc múlva megjelenítéséhez, ha a rendszer eltávolítja az alkalmazást.

-   Ha licencet a felhasználó vagy csoport el lett távolítva a felhasználó nem tagja ennek attól függően, hogy méretét és összetettségét, a csoport változásait elvégzendő hosszú ideig is eltarthat. Lehetővé teszi további idő előtt jelentkezik be a hozzáférési Panel.

## <a name="problems-related-to-assigning-applications-to-users"></a>Felhasználók hozzárendelése kapcsolatos problémák

Egy felhasználó lehet annak, hogy egy alkalmazás a hozzáférési Panel a mert azok volt korábban hozzárendelt. Az alábbiakban néhány ellenőrizze módjai:

-   [Ellenőrizze, hogy ha a felhasználó hozzá van rendelve az alkalmazáshoz](#check-if-a-user-is-assigned-to-the-application)

-   [Annak ellenőrzése, hogy a felhasználó a licenc, az alkalmazással kapcsolatos](#check-if-a-user-is-under-a-license-related-to-the-application)


### <a name="check-if-a-user-is-assigned-to-the-application"></a>Ellenőrizze, hogy ha a felhasználó hozzá van rendelve az alkalmazáshoz

Ellenőrizze, hogy ha a felhasználó hozzá van rendelve az alkalmazást, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure-portálon** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **összes alkalmazás** az alkalmazások listájának megtekintéséhez.

6.  **Keresési** a szóban forgó alkalmazás nevét.

7.  Kattintson a **felhasználók és csoportok**.

8.  Ellenőrizze, hogy ha a felhasználó hozzá van rendelve az alkalmazáshoz.

  * Ha a felhasználó eltávolítja az alkalmazásból **sorára kattintson** a felhasználó, és válassza ki **törlése**.

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>Annak ellenőrzése, hogy a felhasználó a licenc, az alkalmazással kapcsolatos

A felhasználó licenc-hozzárendeléseket ellenőrzéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure-portálon** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **minden felhasználó**.

6.  **Keresési** érdekli, felhasználó és **sorára kattintson** kiválasztásához.

7.  Kattintson a **licencek** megtekintéséhez, amely licencek, a felhasználó jelenleg hozzá van rendelve.

   * Ha a felhasználó hozzá van rendelve egy Office-licencet, ezzel első fél Office alkalmazások jelennek meg a felhasználó hozzáférési Panel.

## <a name="problems-related-to-assigning-applications-to-groups"></a>Csoportok alkalmazásoké kapcsolatos problémák

Egy felhasználó lehet annak, hogy egy alkalmazás a hozzáférési Panel a mert, amelyet az alkalmazáshoz rendelt csoportba. Az alábbiakban néhány ellenőrizze módjai:

-   [A felhasználói csoporttagság ellenőrzése](#check-a-users-group-memberships)

-   [Annak ellenőrzése, hogy egy felhasználó egy licencet rendelt csoport tagja](#check-if-a-user-is-a-member-of-a-group-assigned-to-a-license)

### <a name="check-a-users-group-memberships"></a>A felhasználói csoporttagság ellenőrzése

Ellenőrizze a csoport tagságát, kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **Azure-portálon** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **minden felhasználó**.

6.  **Keresési** érdekli, felhasználó és **sorára kattintson** kiválasztásához.

7.  Kattintson a **csoportok.**

8.  Ellenőrizze, hogy a felhasználók egy csoportjának az alkalmazás része.

   * Ha a felhasználó a csoportból eltávolítani kívánt **sorára kattintson** a csoport- és select tulajdonosával.

### <a name="check-if-a-user-is-a-member-of-a-group-assigned-to-a-license"></a>Annak ellenőrzése, hogy egy felhasználó egy licencet rendelt csoport tagja

1.  Nyissa meg a [ **Azure-portálon** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdája.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" a szűrő keresési mezőbe, és válasszon a **Azure Active Directory** elemet.

4.  Kattintson a **felhasználók és csoportok** a navigációs menü.

5.  Kattintson a **minden felhasználó**.

6.  **Keresési** érdekli, felhasználó és **sorára kattintson** kiválasztásához.

7.  Kattintson a **csoportok.**

8.  egy adott csoport sorára kattintson.

9.  Kattintson a **licencek** megtekintéséhez, amely licencek, a csoport van rendelve.

  * A csoport van rendelve egy Office-licencet, ha ez lehetséges, hogy lehetővé teszik a bizonyos első fél Office alkalmazások jelennek meg a felhasználó hozzáférési Panel.


## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Ha ezek a hibaelhárítási lépéseket nem a hárítsa el a problémát

támogatási jegy megnyitása a következő információkat, ha rendelkezésre áll:

-   Megfelelési hiba azonosítója

-   Egyszerű felhasználónév (felhasználó e-mail címe)

-   Bérlőazonosító

-   Böngésző típusa

-   Időzóna és idő/időkeretre során hiba történik.

-   Fiddler nyomkövetések

## <a name="next-steps"></a>További lépések
[Alkalmazások kezelése az Azure Active Directoryval](manage-apps/what-is-application-management.md)
