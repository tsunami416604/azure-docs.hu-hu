---
title: Csoporttagok hozzáadása vagy eltávolítása – Azure Active Directory | Microsoft dokumentumok
description: Útmutató a tagok hozzáadásához vagy eltávolításához egy csoportból az Azure Active Directory használatával.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1c83c57be63ae9e2a4d4113accaefe8a2c2b525
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68561958"
---
# <a name="add-or-remove-group-members-using-azure-active-directory"></a>Csoporttagok hozzáadása vagy eltávolítása az Azure Active Directory használatával
Az Azure Active Directory használatával továbbra is hozzáadhat és eltávolíthat csoporttagokat.

## <a name="to-add-group-members"></a>Csoporttagok hozzáadása

1. A címtár eléréséhez globális rendszergazdai fiókkal jelentkezzen be az [Azure portálra](https://portal.azure.com).

2. Válassza az **Azure Active Directory**lehetőséget, majd a **Csoportok**lehetőséget.

3. A **Csoportok – Minden csoport** lapon keresse meg és jelölje ki azt a csoportot, amelyhez a tagot hozzá kívánja adni. Ebben az esetben használja a korábban létrehozott csoport, **MDM-házirend - Nyugat**.

    ![Csoportok - Minden csoport lap, a csoport neve kiemelve](media/active-directory-groups-members-azure-portal/group-all-groups-screen.png)

4. Az **MDM policy – West áttekintési** lapján válassza a **Tagok** lehetőséget a **Kezelés** területen.

    ![MDM-szabályzat – Nyugat-áttekintés oldal, kiemelt tagopcióval](media/active-directory-groups-members-azure-portal/group-overview-blade.png)

5. Válassza **a Tagok hozzáadása**lehetőséget, majd keresse meg és jelölje ki a csoporthoz hozzáadni kívánt tagokat, majd válassza a Kijelölés **lehetőséget.**

    Egy üzenet jelenik meg, amely szerint a tagok hozzáadása sikeresen megtörtént.

    ![Tagok hozzáadása lap, a keresett tag jelenik meg](media/active-directory-groups-members-azure-portal/update-members.png)

6. Frissítse a képernyőt a csoporthoz hozzáadott összes tagnév megtekintéséhez.

## <a name="to-remove-group-members"></a>Csoporttagok eltávolítása

1. A **Csoportok – Minden csoport** lapon keresse meg és jelölje ki azt a csoportot, amelyből el szeretné távolítani a tagot. Ismét fogjuk használni, **MDM politika - Nyugat**.

2. Válassza a **Tagok** lehetőséget a **Kezelés** területen, keresse meg és jelölje ki az eltávolítani kívánt tag nevét, majd válassza az **Eltávolítás lehetőséget.**

    ![Tag adatai lap, eltávolítás i.](media/active-directory-groups-members-azure-portal/remove-members-from-group.png)

## <a name="next-steps"></a>További lépések

- [Csoportok és tagok megtekintése](active-directory-groups-view-azure-portal.md)

- [A csoport beállításainak szerkesztése](active-directory-groups-settings-azure-portal.md)

- [Az erőforrásokhoz való hozzáférés kezelése csoportokkal](active-directory-manage-groups.md)

- [A csoportban lévő felhasználók dinamikus szabályainak kezelése](../users-groups-roles/groups-create-rule.md)

- [Azure-előfizetés társítása vagy hozzáadása az Azure Active Directoryhoz](active-directory-how-subscriptions-associated-directory.md)
