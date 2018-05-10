---
title: Létrehoz egy csoportot a felhasználók számára az Azure Active Directory |} Microsoft Docs
description: Hozzon létre egy csoportot az Azure Active Directoryban, és vegye fel tagokat a csoporthoz
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 08/04/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8f1722ab753287068e65e3d386e1d1a1bf40d742
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="create-a-group-and-add-members-in-azure-active-directory"></a>Hozzon létre egy csoportot, és tagokat vehet az Azure Active Directoryban
> [!div class="op_single_selector"]
> * [Azure Portal](active-directory-groups-create-azure-portal.md)
> * [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)
>
>

Ez a cikk azt ismerteti, hogyan hozhat létre és tölthet egy új csoportot az Azure Active Directoryban. Egy csoport segítségével végrehajthat felügyeleti feladatokat, mint a hozzárendelése a licencek vagy hozzáférési engedélyekkel a felhasználók vagy eszközök számát egyszerre.

## <a name="how-do-i-create-a-group"></a>Hogyan hozható létre csoport?
1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
2. Válassza ki **minden szolgáltatás**, adja meg **felhasználók és csoportok** a szövegmezőbe, majd válassza ki azt a **Enter**.

   ![Nyitó felhasználók kezelése](./media/active-directory-groups-create-azure-portal/search-user-management.png)
3. Az a **felhasználók és csoportok** panelen válassza **összes csoport**.

   ![A csoportok panel megnyitása](./media/active-directory-groups-create-azure-portal/view-groups-blade.png)
4. Az a **felhasználók és csoportok – minden csoport** panelen válassza a **Hozzáadás** parancsot.

   ![Az Add parancs kiválasztása](./media/active-directory-groups-create-azure-portal/add-group-command.png)
5. Az a **csoport** panelen, egy nevet és leírást a csoporthoz hozzáadni.
6. Válasszon ki a csoportba felvenni kívánt tagot, jelölje be **hozzárendelt** a a **tagságtípusának** mezőbe, majd válassza ki **tagok**. Való egy csoport tagságának dinamikusan felügyeletével kapcsolatos további információkért lásd: [attribútumok használata a csoporttagság speciális szabályok létrehozása](active-directory-groups-dynamic-membership-azure-portal.md).

   ![Tagok hozzáadása kiválasztása](./media/active-directory-groups-create-azure-portal/select-members.png)
7. Az a **tagok** panelen, jelölje be egy vagy több felhasználók vagy eszközök felvétele a csoportba, és válassza ki a **válasszon** gomb felvétele a csoportba a panel alján. A **felhasználói** mezőben szűrése a képernyőt a megfelelő felhasználó vagy eszköz nevek a bejegyzést. Nem helyettesítő karakterek használata engedélyezett, hogy a mezőben.
8. Amikor befejezte a tagok hozzáadását a csoporthoz, válassza ki a **létrehozása** a a **csoport** panelen.    

   ![Hozzon létre a csoport megerősítése](./media/active-directory-groups-create-azure-portal/create-group-confirmation.png)


## <a name="next-steps"></a>További lépések
E cikkekben további információk találhatók az Azure Active Directoryval kapcsolatban.

* [Tekintse meg a meglévő csoportok](active-directory-groups-view-azure-portal.md)
* [Egy csoport beállításainak kezelése](active-directory-groups-settings-azure-portal.md)
* [A csoport tagjai kezelése](active-directory-groups-members-azure-portal.md)
* [A csoport tagságát kezelése](active-directory-groups-membership-azure-portal.md)
* [A csoport dinamikus szabályok kezelése](active-directory-groups-dynamic-membership-azure-portal.md)
