---
title: Adja hozzá, vagy távolítsa el a csoport tagjai – Azure Active Directory |} A Microsoft Docs
description: Tagok hozzáadása vagy eltávolítása egy csoportból, az Azure Active Directoryval kapcsolatos utasításokat.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: lizross
ms.custom: it-pro, seodec18
ms.reviewer: krbain
ms.openlocfilehash: 3db75187160be9a1e4ca985e207f84db71cf75d7
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55093201"
---
# <a name="add-or-remove-group-members-using-azure-active-directory"></a>Az Azure Active Directory csoport tagok hozzáadása vagy eltávolítása
Azure Active Directory használatával, akkor továbbra is hozzáadhat és eltávolíthat a csoport tagjai.

## <a name="to-add-group-members"></a>Csoporttagok hozzáadása

1. A címtár eléréséhez globális rendszergazdai fiókkal jelentkezzen be az [Azure portálra](https://portal.azure.com).

2. Válassza ki **Azure Active Directory**, majd válassza ki **csoportok**.

3. Az a **csoportok – összes csoport** lapon keresse meg és válassza ki a csoportot szeretne hozzáadni a tagokat. Ebben az esetben használja a korábban létrehozott csoportot **mobileszköz-kezelési szabályzat – Nyugat-India**.

    ![Csoportok – minden csoport oldalon kiemelve csoport neve](media/active-directory-groups-members-azure-portal/group-all-groups-screen.png)

4. Az **MDM policy – West áttekintési** lapján válassza a **Tagok** lehetőséget a **Kezelés** területen.

    ![Mobileszköz-kezelési szabályzat – Nyugat-India – áttekintés oldala, amelyen a tagok opció kiemelésével](media/active-directory-groups-members-azure-portal/group-overview-blade.png)

5. Válassza ki **tagok hozzáadása**, majd keresse meg és válassza ki az egyes tagok hozzáadása a csoporthoz, és válassza a kívánt **kiválasztása**.

    Kap egy üzenetet arról, hogy a tagok hozzáadása sikerült.

    ![Adja hozzá a tagok lapon, a tag látható keresni](media/active-directory-groups-members-azure-portal/update-members.png)

6. Frissítse a képernyőn látható az összes hozzáadni a csoporthoz, tag nevét.

## <a name="to-remove-group-members"></a>A csoport tagjainak eltávolítása

1. Az a **csoportok – összes csoport** lapon keresse meg és válassza ki a csoportot el szeretné távolítani a tagja. Újra fogjuk használni, **mobileszköz-kezelési szabályzat – Nyugat-India**.

2. Válassza ki **tagok** a a **kezelés** területen, keresse meg és válassza ki a nevét, a tag eltávolítása, és válassza ki a **eltávolítása**.

    ![Tag információs oldalán, az Eltávolítás lehetőség](media/active-directory-groups-members-azure-portal/remove-members-from-group.png)

## <a name="next-steps"></a>További lépések

- [Csoportok és tagok megtekintése](active-directory-groups-view-azure-portal.md)

- [A csoport beállításainak szerkesztése](active-directory-groups-settings-azure-portal.md)

- [Az erőforrásokhoz való hozzáférés kezelése csoportokkal](active-directory-manage-groups.md)

- [A csoportban lévő felhasználók dinamikus szabályainak kezelése](../users-groups-roles/groups-create-rule.md)

- [Azure-előfizetés társítása vagy hozzáadása az Azure Active Directoryhoz](active-directory-how-subscriptions-associated-directory.md)
