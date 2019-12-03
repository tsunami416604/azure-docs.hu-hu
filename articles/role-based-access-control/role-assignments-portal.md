---
title: Szerepkör-hozzárendelések hozzáadása vagy eltávolítása az Azure RBAC és a Azure Portal használatával
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
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 17a325e96e9709b60da2f23d1dc68e3300fde80c
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707869"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-the-azure-portal"></a>Szerepkör-hozzárendelések hozzáadása vagy eltávolítása az Azure RBAC és a Azure Portal használatával

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] ez a cikk azt ismerteti, hogyan rendelhet hozzá szerepköröket a Azure Portal használatával.

Ha Azure Active Directory rendszergazdai szerepköröket kell társítania, tekintse meg [a rendszergazdai szerepkörök megtekintése és társítása a Azure Active Directory-ben](../active-directory/users-groups-roles/directory-manage-roles-portal.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Szerepkör-hozzárendelések hozzáadásához vagy eltávolításához a következőket kell tennie:

- `Microsoft.Authorization/roleAssignments/write` és `Microsoft.Authorization/roleAssignments/delete` engedélyek, például a [felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator) vagy a [tulajdonos](built-in-roles.md#owner)

## <a name="overview-of-access-control-iam"></a>A hozzáférés-vezérlés (IAM) áttekintése

A **hozzáférés-vezérlés (iam)** az a panel, amelyet a szerepkörök hozzárendeléséhez használ. Az identitás-és hozzáférés-kezelés néven is ismert, és a Azure Portal több helyén is megjelenik. Az alábbi példa az előfizetés hozzáférés-vezérlés (IAM) paneljét mutatja be.

![Egy előfizetés hozzáférés-vezérlés (IAM) panelje](./media/role-assignments-portal/access-control-subscription.png)

Ahhoz, hogy a leghatékonyabb legyen a hozzáférés-vezérlés (IAM) panel, segít, ha a következő három kérdésre válaszol, amikor egy szerepkört szeretne hozzárendelni:

1. **Kinek van hozzáférése?**

    A felhasználóra, csoportra, egyszerű szolgáltatásnévre vagy felügyelt identitásra hivatkozik. Ezt *rendszerbiztonsági tag*is nevezik.

1. **Milyen szerepkörre van szükségük?**

    Az engedélyek szerepkörökbe vannak csoportosítva. Több [beépített szerepkörből](built-in-roles.md) is választhat, vagy saját egyéni szerepköröket is használhat.

1. **Hová kell férnek hozzá?**

    Hol hivatkozik arra az erőforrás-készletre, amelyre a hozzáférés vonatkozik. Ahol lehet felügyeleti csoport, előfizetés, erőforráscsoport vagy egyetlen erőforrás, például egy Storage-fiók. Ezt a *hatókört*nevezzük.

## <a name="add-a-role-assignment"></a>Szerepkör-hozzárendelés hozzáadása

Az alábbi lépéseket követve rendelhet hozzá egy szerepkört különböző hatókörökhöz.

1. A Azure Portal kattintson a **minden szolgáltatás** elemre, majd válassza ki a hatókört. Kiválaszthatja például a **felügyeleti csoportokat**, **előfizetéseket**, **erőforráscsoportokat**vagy egy erőforrást.

1. Kattintson az adott erőforrásra.

1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.

1. A **szerepkör-hozzárendelések** lapra kattintva megtekintheti az összes szerepkör-hozzárendelést ezen a hatókörön.

1. Kattintson a **hozzáadás** > **szerepkör-hozzárendelés hozzáadása** elemre a szerepkör-hozzárendelés hozzáadása ablaktábla megnyitásához.

   Ha nem rendelkezik jogosultsággal a szerepkörök hozzárendeléséhez, a szerepkör-hozzárendelés hozzáadása lehetőség le lesz tiltva.

   ![Menü hozzáadása](./media/role-assignments-portal/add-menu.png)

   ![Szerepkör-hozzárendelési ablaktábla hozzáadása](./media/role-assignments-portal/add-role-assignment.png)

1. A **Szerepkör** legördülő listájában válasszon ki egy szerepkört, például a **Virtuális gépek közreműködője** szerepkört.

1. A **Select (kiválasztás** ) listán válasszon ki egy felhasználót, csoportot, szolgáltatásnevet vagy felügyelt identitást. Ha a listában nem látja a rendszerbiztonsági tagot, írhat a **Kiválasztás** mezőbe megjelenítendő nevek, e-mail-címek és objektumazonosítók a címtárban történő kereséséhez.

1. Kattintson a **Mentés** gombra a szerepkör hozzárendeléséhez.

   Néhány pillanat elteltével a rendszerbiztonsági tag a kiválasztott hatókörhöz rendeli a szerepkört.

## <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Felhasználó hozzáadása egy előfizetés adminisztrátoraként

Ahhoz, hogy egy felhasználó egy Azure-előfizetés rendszergazdája legyen, rendelje hozzá őket a [tulajdonosi](built-in-roles.md#owner) szerepkörhöz az előfizetés hatókörében. A tulajdonosi szerepkör teljes hozzáférést biztosít a felhasználó számára az előfizetés összes erőforrásához, beleértve a mások számára való hozzáférés biztosításának engedélyét is. Ez ugyanúgy történik, mint bármely más szerepkör-hozzárendelés esetében.

1. A Azure Portal kattintson a **minden szolgáltatás** , majd az **előfizetések**elemre.

1. Kattintson arra az előfizetésre, amelyhez szerepkör-hozzárendelést szeretne hozzáadni.

1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.

1. Kattintson a **Szerepkör-hozzárendelések** lapra az előfizetés szerepkör-hozzárendeléseinek megtekintéséhez.

1. Kattintson a **hozzáadás** > **szerepkör-hozzárendelés hozzáadása** elemre a szerepkör-hozzárendelés hozzáadása ablaktábla megnyitásához.

   Ha nem rendelkezik jogosultsággal a szerepkörök hozzárendeléséhez, a szerepkör-hozzárendelés hozzáadása lehetőség le lesz tiltva.

   ![Menü hozzáadása](./media/role-assignments-portal/add-menu.png)

   ![Szerepkör-hozzárendelési ablaktábla hozzáadása](./media/role-assignments-portal/add-role-assignment.png)

1. A **Szerepkör** legördülő listában válassza a **Tulajdonos** szerepkört.

1. A **kiválasztási** listában válasszon ki egy felhasználót. Ha a listában nem látja a felhasználót, beírhatja a nevét a **kiválasztási** mezőbe, hogy rákeressen a megjelenítendő neveket és e-mail-címeket tartalmazó címtárban.

1. Kattintson a **Mentés** gombra a szerepkör hozzárendeléséhez.

   Néhány pillanat múlva a rendszer hozzárendeli a felhasználót a Tulajdonos szerepkörhöz az előfizetés hatókörében.

## <a name="remove-a-role-assignment"></a>Szerepkör-hozzárendelés eltávolítása

Az RBAC-ben hozzáférés eltávolításához egy szerepkör-hozzárendelést kell eltávolítania. A szerepkör-hozzárendelés eltávolításához kövesse az alábbi lépéseket.

1. Nyissa meg a **hozzáférés-vezérlést (iam)** egy hatókörön, például a felügyeleti csoport, az előfizetés, az erőforráscsoport vagy az erőforrás területen, ahol el szeretné távolítani a hozzáférést.

1. Kattintson a **Szerepkör-hozzárendelések** lapra az előfizetés szerepkör-hozzárendeléseinek megtekintéséhez.

1. A szerepkör-hozzárendelések listájában jelölje be az eltávolítani kívánt szerepkör-hozzárendeléssel rendelkező rendszerbiztonsági tag melletti jelölőnégyzetet.

   ![Szerepkör-hozzárendelés eltávolítási üzenete](./media/role-assignments-portal/remove-role-assignment-select.png)

1. Kattintson az **Eltávolítás**gombra.

   ![Szerepkör-hozzárendelés eltávolítási üzenete](./media/role-assignments-portal/remove-role-assignment.png)

1. A megjelenő szerepkör-hozzárendelés eltávolítása üzenetben kattintson az **Igen**gombra.

    Az örökölt szerepkör-hozzárendeléseket nem lehet eltávolítani. Ha örökölt szerepkör-hozzárendelést kell eltávolítania, azt abban a hatókörben kell megtennie, ahol a szerepkör-hozzárendelés létrejött. A **hatókör** oszlopban, a **(örökölt)** elem mellett található egy hivatkozás, amely arra a hatókörre mutat, ahol ez a szerepkör hozzá van rendelve. Lépjen az ott szereplő hatókörhöz a szerepkör-hozzárendelés eltávolításához.

   ![Szerepkör-hozzárendelés eltávolítási üzenete](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>Következő lépések

- [Szerepkör-hozzárendelések listázása az Azure RBAC és a Azure Portal használatával](role-assignments-list-portal.md)
- [Oktatóanyag: felhasználói hozzáférés biztosítása az Azure-erőforrásokhoz a RBAC és a Azure Portal használatával](quickstart-assign-role-user-portal.md)
- [Azure-erőforrások RBAC kapcsolatos hibák](troubleshooting.md)
- [Erőforrások rendszerezése az Azure-beli felügyeleti csoportokkal](../governance/management-groups/overview.md)
