---
title: "A csoport az Azure Active Directoryban a tagokat kezelése |} Microsoft Docs"
description: "Hogyan lehet hozzáadni vagy a felhasználók és eszközök eltávolítása egy csoportból az Azure Active Directoryban"
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: d399a97d-fd2a-4b2d-b73d-0975db83f41b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017;it-pro
ms.reviewer: piotrci
ms.openlocfilehash: 49c975aa34f28c6c00591435726e538bb79a78cb
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="manage-group-membership-for-users-in-your-azure-active-directory-tenant"></a>Az Azure Active Directory-bérlő felhasználók csoport tagságának kezelésére
Ez a cikk azt ismerteti, hogyan kezelheti az Azure Active Directory (Azure AD) csoport tagjai.

## <a name="how-do-i-find-the-members-and-manage-them"></a>Hogyan keresse meg a tagok és kezelheti azokat?
1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
2. Válassza ki **további szolgáltatások**, adja meg **felhasználók és csoportok** a szövegmezőbe, majd válassza ki azt a **Enter**.

   ![Nyitó felhasználók kezelése](./media/active-directory-groups-members-azure-portal/search-user-management.png)
3. Az a **felhasználók és csoportok** panelen válassza **összes csoport**.

   ![A csoportok panel megnyitása](./media/active-directory-groups-members-azure-portal/view-groups-blade.png)
4. Az a **felhasználók és csoportok – minden csoport** panelen válasszon ki egy csoportot.
5. Az a **csoport - *groupname***  panelen válassza **tagok**.

   ![A tagok panel megnyitása](./media/active-directory-groups-members-azure-portal/view-group-members.png)
6. Tagok hozzáadása a csoporthoz, az a **csoport - tagok** panelen válassza **tagok hozzáadása**.

   ![Tagok parancs hozzáadása](./media/active-directory-groups-members-azure-portal/add-group-members-command.png)
7. Az a **tagok** panelen, jelölje be egy vagy több felhasználók vagy eszközök felvétele a csoportba, és válassza ki a **válasszon** gomb felvétele a csoportba a panel alján. A **felhasználói** mezőben szűrése a képernyőt a megfelelő felhasználó vagy eszköz nevek a bejegyzést. Nem helyettesítő karakterek használata engedélyezett, hogy a mezőben.
8. Tagok eltávolítása a csoportból, a a **csoport - tagok** panelen válassza ki a megfelelő tag.
9. Az a ***membername*** panelen válassza a **eltávolítása** parancsot, és erősítse meg választását a parancssorba.

   ![Távolítsa el a tagok parancs](./media/active-directory-groups-members-azure-portal/remove-group-members-command.png)
10. Ha befejezte a csoport tagjainak módosítása, válassza ki a **mentése**.

## <a name="additional-information"></a>További információ
E cikkekben további információk találhatók az Azure Active Directoryval kapcsolatban.

* [Tekintse meg a meglévő csoportok](active-directory-groups-view-azure-portal.md)
* [Hozzon létre egy új csoportot és tagok hozzáadása](active-directory-groups-create-azure-portal.md)
* [Egy csoport beállításainak kezelése](active-directory-groups-settings-azure-portal.md)
* [A csoport tagságát kezelése](active-directory-groups-membership-azure-portal.md)
* [A csoport dinamikus szabályok kezelése](active-directory-groups-dynamic-membership-azure-portal.md)
