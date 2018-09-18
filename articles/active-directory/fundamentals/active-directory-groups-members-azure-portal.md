---
title: Hogyan lehet hozzáadni, vagy az Azure Active Directory csoport tagjainak eltávolítása |} A Microsoft Docs
description: Megtudhatja, hogyan hozzáadása vagy eltávolítása a felhasználók és eszközök egy csoportból az Azure Active Directory használatával.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: lizross
ms.custom: it-pro
ms.reviewer: krbain
ms.openlocfilehash: 7c1a83d83dcbf247550c66602a6f53d4ef0d7930
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2018
ms.locfileid: "45733364"
---
# <a name="how-to-add-or-remove-group-members-using-azure-active-directory"></a>Útmutató: az Azure Active Directory csoport tagok hozzáadása vagy eltávolítása
Azure Active Directory használatával, akkor továbbra is hozzáadhat és eltávolíthat a csoport tagjai.

## <a name="to-add-group-members"></a>Csoporttagok hozzáadása

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) a címtár egy globális rendszergazdai fiók használatával.

2. Válassza ki **Azure Active Directory**, majd válassza ki **csoportok**.

3. Az a **csoportok – összes csoport** lapon keresse meg és válassza ki a csoportot szeretne hozzáadni a tagokat. Ebben az esetben használja a korábban létrehozott csoportot **mobileszköz-kezelési szabályzat – Nyugat-India**.

    ![Csoportok – minden csoport oldalon kiemelve csoport neve](media/active-directory-groups-members-azure-portal/group-all-groups-screen.png)

4. Az a **mobileszköz-kezelési szabályzat – Nyugat-India – áttekintés** lapon jelölje be **tagok** a a **kezelés** területen.

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

- [A csoportok és tagok megtekintése](active-directory-groups-view-azure-portal.md)

- [A csoport beállításainak szerkesztése](active-directory-groups-settings-azure-portal.md)

- [Csoportok használata erőforrásaihoz való hozzáférés kezelése](active-directory-manage-groups.md)

- [A csoportban lévő felhasználók dinamikus szabályainak kezelése](../users-groups-roles/groups-create-rule.md)

- [Hozzárendelése vagy Azure-előfizetés hozzáadása az Azure Active Directoryhoz](active-directory-how-subscriptions-associated-directory.md)
