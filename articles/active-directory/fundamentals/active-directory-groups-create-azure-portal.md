---
title: Csoport létrehozása a felhasználók számára az Azure AD-ben | Microsoft Docs
description: Csoport létrehozása és tagok hozzáadása az Azure Active Directoryban
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: quickstart
ms.date: 08/04/2017
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 3c71c9c49413045e3a730c10e90ea3c12648b4cb
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2018
ms.locfileid: "37857722"
---
# <a name="create-a-group-and-add-members-in-azure-active-directory"></a>Csoport létrehozása és tagok hozzáadása az Azure Active Directoryban
> [!div class="op_single_selector"]
> * [Azure Portal](active-directory-groups-create-azure-portal.md)
> * [PowerShell](../users-groups-roles/groups-settings-v2-cmdlets.md)

Ez a cikk ismerteti egy új csoport létrehozásának és feltöltésének módját az Azure Active Directoryban. A csoportokat kezelési feladatok, például licencek vagy engedélyek egyszerre több felhasználóhoz vagy eszközhöz való hozzárendelésére használhatja.

## <a name="how-do-i-create-a-group"></a>Hogyan hozható létre csoport?
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
2. Válassza a **Minden szolgáltatás** elemet, írja be a **Felhasználók és csoportok** kifejezést a szövegmezőbe, majd válassza a **Bevitel** parancsot.

   ![Felhasználókezelés megnyitása](./media/active-directory-groups-create-azure-portal/search-user-management.png)
3. A **Felhasználók és csoportok** panelen válassza a **Minden csoport** elemet.

   ![A csoportok paneljének megnyitása](./media/active-directory-groups-create-azure-portal/view-groups-blade.png)
4. A **Felhasználók és csoportok – Minden csoport** panelen válassza a **Hozzáadás** parancsot.

   ![A Hozzáadás parancs kiválasztása](./media/active-directory-groups-create-azure-portal/add-group-command.png)
5. A **Csoport** panelen adja meg a csoport nevét és leírását.
6. Csoporttagok felvételéhez válassza ki a **Hozzárendelt** típust a **Tagságtípus** mezőben, majd válassza a **Tagok** elemet. További információ a csoporttagságok dinamikus kezelésével kapcsolatban: [Attribútumok használata speciális csoporttagsági szabályok létrehozásához](../users-groups-roles/groups-dynamic-membership.md).

   ![Hozzáadni kívánt tagok kiválasztása](./media/active-directory-groups-create-azure-portal/select-members.png)
7. A **Tagok** panelen jelöljön ki egy vagy több, a csoporthoz hozzárendelni kívánt felhasználót vagy eszközt, majd a panel alján található **Kiválasztás** gombbal adja őket hozzá a csoporthoz. A **Felhasználó** mezővel aszerint szűrheti a megjelenített elemeket, hogy a megadott szöveg megtalálható-e egy felhasználó- vagy eszköznévben. Helyettesítő karakterek nem használhatók a mezőben.
8. Ha befejezte a tagok hozzáadását a csoporthoz, válassza a **Létrehozás** gombot a **Csoport** panelen.    

   ![Csoport létrehozásának megerősítése](./media/active-directory-groups-create-azure-portal/create-group-confirmation.png)


## <a name="next-steps"></a>További lépések
E cikkekben további információk találhatók az Azure Active Directoryval kapcsolatban.

* [Meglévő csoportok megtekintése](active-directory-groups-view-azure-portal.md)
* [Csoportbeállítások kezelése](active-directory-groups-settings-azure-portal.md)
* [Csoporttagok kezelése](active-directory-groups-members-azure-portal.md)
* [Csoporttagságok kezelése](active-directory-groups-membership-azure-portal.md)
* [A csoportban lévő felhasználók dinamikus szabályainak kezelése](../users-groups-roles/groups-dynamic-membership.md)
