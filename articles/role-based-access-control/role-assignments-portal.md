---
title: Szerepkör-hozzárendelések hozzáadása vagy eltávolítása a RBAC és a Azure Portal
description: Ismerje meg, hogyan biztosíthat hozzáférést az Azure-erőforrásokhoz felhasználók, csoportok, egyszerű szolgáltatások vagy felügyelt identitások számára az Azure szerepköralapú hozzáférés-vezérlés (RBAC) és a Azure Portal használatával.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/25/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: e2458b07602bca3b6f12b2f486b509c11d705461
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79246356"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-the-azure-portal"></a>Szerepkör-hozzárendelések hozzáadása vagy eltávolítása az Azure RBAC és a Azure Portal használatával

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] ez a cikk azt ismerteti, hogyan rendelhet hozzá szerepköröket a Azure Portal használatával.

Ha Azure Active Directory rendszergazdai szerepköröket kell társítania, tekintse meg [a rendszergazdai szerepkörök megtekintése és társítása a Azure Active Directory-ben](../active-directory/users-groups-roles/directory-manage-roles-portal.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Szerepkör-hozzárendelések hozzáadásához vagy eltávolításához a következőket kell tennie:

- `Microsoft.Authorization/roleAssignments/write` és `Microsoft.Authorization/roleAssignments/delete` engedélyek, például a [felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator) vagy a [tulajdonos](built-in-roles.md#owner)

## <a name="access-control-iam"></a>Hozzáférés-vezérlés (IAM)

A **hozzáférés-vezérlés (iam)** az a panel, amelyet a szerepkörök hozzárendeléséhez használhat az Azure-erőforrásokhoz való hozzáférés biztosításához. Az identitás-és hozzáférés-kezelés néven is ismert, és a Azure Portal több helyén is megjelenik. Az alábbi példa az előfizetés hozzáférés-vezérlés (IAM) paneljét mutatja be.

![Egy előfizetés hozzáférés-vezérlés (IAM) panelje](./media/role-assignments-portal/access-control-subscription.png)

Ahhoz, hogy a leghatékonyabb legyen a hozzáférés-vezérlés (IAM) panel, segít, ha a következő három kérdésre válaszol, amikor egy szerepkört szeretne hozzárendelni:

1. **Kinek van hozzáférése?**

    A felhasználóra, csoportra, egyszerű szolgáltatásnévre vagy felügyelt identitásra hivatkozik. Ezt *rendszerbiztonsági tag*is nevezik.

1. **Milyen szerepkörre van szükségük?**

    Az engedélyek szerepkörökbe vannak csoportosítva. Több [beépített szerepkörből](built-in-roles.md) is választhat, vagy használhatja saját egyéni szerepköreit is.

1. **Hová kell férnek hozzá?**

    Hol hivatkozik arra az erőforrás-készletre, amelyre a hozzáférés vonatkozik. Ahol lehet felügyeleti csoport, előfizetés, erőforráscsoport vagy egyetlen erőforrás, például egy Storage-fiók. Ezt a *hatókört*nevezzük.

## <a name="add-a-role-assignment"></a>Szerepkör-hozzárendelés hozzáadása

Az Azure-RBAC az Azure-erőforrásokhoz való hozzáférés biztosításához hozzá kell adnia egy szerepkör-hozzárendelést. A szerepkörök hozzárendeléséhez kövesse az alábbi lépéseket.

1. A Azure Portal kattintson a **minden szolgáltatás** elemre, majd válassza ki azt a hatókört, amelyhez hozzáférést szeretne biztosítani. Kiválaszthatja például a **felügyeleti csoportokat**, **előfizetéseket**, **erőforráscsoportokat**vagy egy erőforrást.

1. Kattintson az adott hatókörhöz tartozó erőforrásra.

1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.

1. Kattintson a **szerepkör-hozzárendelések** lapra a szerepkör-hozzárendelések ezen a hatókörön való megtekintéséhez.

    ![Hozzáférés-vezérlés (IAM) és szerepkör-hozzárendelések lap](./media/role-assignments-portal/role-assignments.png)

1. Kattintson a **hozzáadás** > **szerepkör-hozzárendelés hozzáadása**lehetőségre.

   Ha nem rendelkezik jogosultsággal a szerepkörök hozzárendeléséhez, a szerepkör-hozzárendelés hozzáadása lehetőség le lesz tiltva.

   ![Menü hozzáadása](./media/role-assignments-portal/add-menu.png)

    Megnyílik a szerepkör-hozzárendelés hozzáadása panel.

   ![Szerepkör-hozzárendelési ablaktábla hozzáadása](./media/role-assignments-portal/add-role-assignment.png)

1. A **Szerepkör** legördülő listájában válasszon ki egy szerepkört, például a **Virtuális gépek közreműködője** szerepkört.

1. A **Select (kiválasztás** ) listán válasszon ki egy felhasználót, csoportot, szolgáltatásnevet vagy felügyelt identitást. Ha a listában nem látja a rendszerbiztonsági tagot, írhat a **Kiválasztás** mezőbe megjelenítendő nevek, e-mail-címek és objektumazonosítók a címtárban történő kereséséhez.

1. Kattintson a **Mentés** gombra a szerepkör hozzárendeléséhez.

   Néhány pillanat elteltével a rendszerbiztonsági tag a kiválasztott hatókörhöz rendeli a szerepkört.

    ![Szerepkör-hozzárendelés mentett hozzáadása](./media/role-assignments-portal/add-role-assignment-save.png)

## <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Felhasználó hozzáadása egy előfizetés adminisztrátoraként

Ahhoz, hogy egy felhasználó egy Azure-előfizetés rendszergazdája legyen, rendelje hozzá őket a [tulajdonosi](built-in-roles.md#owner) szerepkörhöz az előfizetés hatókörében. A tulajdonosi szerepkör teljes hozzáférést biztosít a felhasználó számára az előfizetés összes erőforrásához, beleértve a mások számára való hozzáférés biztosításának engedélyét is. Ez ugyanúgy történik, mint bármely más szerepkör-hozzárendelés esetében.

1. A Azure Portal kattintson a **minden szolgáltatás** , majd az **előfizetések**elemre.

1. Kattintson arra az előfizetésre, amelynél a hozzáférést biztosítani kívánja.

1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.

1. A **szerepkör-hozzárendelések** lapra kattintva megtekintheti az előfizetéshez tartozó szerepkör-hozzárendeléseket.

    ![Hozzáférés-vezérlés (IAM) és szerepkör-hozzárendelések lap](./media/role-assignments-portal/role-assignments.png)

1. Kattintson a **hozzáadás** > **szerepkör-hozzárendelés hozzáadása**lehetőségre.

   Ha nem rendelkezik jogosultsággal a szerepkörök hozzárendeléséhez, a szerepkör-hozzárendelés hozzáadása lehetőség le lesz tiltva.

   ![Menü hozzáadása](./media/role-assignments-portal/add-menu.png)

    Megnyílik a szerepkör-hozzárendelés hozzáadása panel.

   ![Szerepkör-hozzárendelési ablaktábla hozzáadása](./media/role-assignments-portal/add-role-assignment.png)

1. A **Szerepkör** legördülő listában válassza a **Tulajdonos** szerepkört.

1. A **kiválasztási** listában válasszon ki egy felhasználót. Ha a listában nem látja a felhasználót, beírhatja a nevét a **kiválasztási** mezőbe, hogy rákeressen a megjelenítendő neveket és e-mail-címeket tartalmazó címtárban.

1. Kattintson a **Mentés** gombra a szerepkör hozzárendeléséhez.

   Néhány pillanat múlva a rendszer hozzárendeli a felhasználót a Tulajdonos szerepkörhöz az előfizetés hatókörében.

## <a name="remove-a-role-assignment"></a>Szerepkör-hozzárendelés eltávolítása

Az Azure-RBAC az Azure-erőforrásokhoz való hozzáférés eltávolításához távolítsa el a szerepkör-hozzárendelést. A szerepkör-hozzárendelés eltávolításához kövesse az alábbi lépéseket.

1. Nyissa meg a **hozzáférés-vezérlést (iam)** egy hatókörön, például a felügyeleti csoport, az előfizetés, az erőforráscsoport vagy az erőforrás területen, ahol el szeretné távolítani a hozzáférést.

1. Kattintson a **Szerepkör-hozzárendelések** lapra az előfizetés szerepkör-hozzárendeléseinek megtekintéséhez.

1. A szerepkör-hozzárendelések listájában jelölje be az eltávolítani kívánt szerepkör-hozzárendeléssel rendelkező rendszerbiztonsági tag melletti jelölőnégyzetet.

   ![Szerepkör-hozzárendelés eltávolítási üzenete](./media/role-assignments-portal/remove-role-assignment-select.png)

1. Kattintson az **Eltávolítás**gombra.

   ![Szerepkör-hozzárendelés eltávolítási üzenete](./media/role-assignments-portal/remove-role-assignment.png)

1. A megjelenő szerepkör-hozzárendelés eltávolítása üzenetben kattintson az **Igen**gombra.

    Ha olyan üzenet jelenik meg, amely szerint az örökölt szerepkör-hozzárendelések nem távolíthatók el, a rendszer megpróbálja eltávolítani a szerepkör-hozzárendelést egy alárendelt hatókörben. Nyissa meg a hozzáférés-vezérlés (IAM) részt azon a hatókörön, ahol a szerepkör hozzá lett rendelve, és próbálkozzon újra. A hozzáférés-vezérlés (IAM) megfelelő hatókörben való megnyitásának gyorsan megtekinthető a **hatókör** oszlop, és a mellette lévő hivatkozásra kattintva **(örökölt)** .

   ![Szerepkör-hozzárendelés eltávolítási üzenete](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>További lépések

- [Szerepkör-hozzárendelések listázása az Azure RBAC és a Azure Portal használatával](role-assignments-list-portal.md)
- [Oktatóanyag: felhasználói hozzáférés biztosítása az Azure-erőforrásokhoz a RBAC és a Azure Portal használatával](quickstart-assign-role-user-portal.md)
- [Azure-erőforrások RBAC kapcsolatos hibák](troubleshooting.md)
- [Erőforrások rendszerezése az Azure-beli felügyeleti csoportokkal](../governance/management-groups/overview.md)
