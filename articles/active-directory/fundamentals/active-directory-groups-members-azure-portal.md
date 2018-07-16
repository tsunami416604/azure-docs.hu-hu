---
title: Egy csoport tagjainak kezelése az Azure AD-ben | Microsoft Docs
description: Felhasználók és eszközök hozzáadása és eltávolítása egy csoportból az Azure Active Directoryban
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: quickstart
ms.date: 08/28/2017
ms.author: lizross
ms.custom: it-pro
ms.reviewer: krbain
ms.openlocfilehash: 947b0c11aba211530e3ae25d6617079bcaf2995f
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2018
ms.locfileid: "37860394"
---
# <a name="manage-group-membership-for-users-in-your-azure-active-directory-tenant"></a>Felhasználók csoporttagságainak kezelése az Azure Active Directory-bérlőben
Ez a cikk azt ismerteti, hogy hogyan kezelheti egy csoport tagjait az Azure Active Directoryban (Azure AD).

## <a name="how-do-i-find-the-members-and-manage-them"></a>Hogyan találhatom meg és kezelhetem a csoporttagokat?
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
2. Válassza a **Minden szolgáltatás** elemet, írja be a **Felhasználók és csoportok** kifejezést a szövegmezőbe, majd nyomja le az **Enter** billentyűt.

   ![Felhasználókezelés megnyitása](./media/active-directory-groups-members-azure-portal/search-user-management.png)
3. A **Felhasználók és csoportok** panelen válassza a **Minden csoport** elemet.

   ![A csoportok paneljének megnyitása](./media/active-directory-groups-members-azure-portal/view-groups-blade.png)
4. A **Felhasználók és csoportok – Minden csoport** panelen válasszon ki egy csoportot.
5. A **Csoport – *csoportnév*** panelen válassza a **Tagok** elemet.

   ![A Tagok panel megnyitása](./media/active-directory-groups-members-azure-portal/view-group-members.png)
6. A csoporttagok hozzáadásához válassza a **Csoport – Tagok** panel **Tagok hozzáadása** parancsát.

   ![Tagok hozzáadása parancs](./media/active-directory-groups-members-azure-portal/add-group-members-command.png)
7. A **Tagok** panelen jelöljön ki egy vagy több, a csoporthoz hozzárendelni kívánt felhasználót vagy eszközt, majd a panel alján található **Kiválasztás** gombbal adja őket hozzá a csoporthoz. A **Felhasználó** mezővel aszerint szűrheti a megjelenített elemeket, hogy a megadott szöveg megtalálható-e egy felhasználó- vagy eszköznévben. Helyettesítő karakterek nem használhatók a mezőben.
8. Ha el szeretne távolítani tagokat a csoportból, válasszon ki egy csoporttagot a **Csoport – Tagok** panelen.
9. A ***tag neve*** panelen válassza ki az **Eltávolítás** parancsot, majd a parancssorban erősítse meg a döntést.

   ![Tagok eltávolítása parancs](./media/active-directory-groups-members-azure-portal/remove-group-members-command.png)
10. Ha befejezte a csoporttagok módosítását, válassza a **Mentés** lehetőséget.

## <a name="additional-information"></a>További információ
E cikkekben további információk találhatók az Azure Active Directoryval kapcsolatban.

* [Meglévő csoportok megtekintése](active-directory-groups-view-azure-portal.md)
* [Új csoport létrehozása és tagok hozzáadása](active-directory-groups-create-azure-portal.md)
* [Csoportbeállítások kezelése](active-directory-groups-settings-azure-portal.md)
* [Csoporttagságok kezelése](active-directory-groups-membership-azure-portal.md)
* [A csoportban lévő felhasználók dinamikus szabályainak kezelése](../users-groups-roles/groups-dynamic-membership.md)
