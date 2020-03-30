---
title: Szerepkör-hozzárendelések hozzáadása vagy eltávolítása az RBAC-kal és az Azure Portalon
description: Megtudhatja, hogyan adhat hozzáférést az Azure-erőforrásokhoz felhasználók, csoportok, egyszerű szolgáltatástagok vagy felügyelt identitások számára az Azure szerepköralapú hozzáférés-vezérlés (RBAC) és az Azure Portal használatával.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246356"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-the-azure-portal"></a>Szerepkör-hozzárendelések hozzáadása vagy eltávolítása az Azure RBAC és az Azure Portal használatával

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]Ez a cikk ismerteti, hogyan rendelhet szerepköröket az Azure Portalhasználatával.

Ha rendszergazdai szerepköröket kell hozzárendelnie az Azure Active Directoryban, olvassa el [a Rendszergazdai szerepkörök megtekintése és hozzárendelése az Azure Active Directoryban című témakört.](../active-directory/users-groups-roles/directory-manage-roles-portal.md)

## <a name="prerequisites"></a>Előfeltételek

Szerepkör-hozzárendelések hozzáadásához vagy eltávolításához a következőkre van szükség:

- `Microsoft.Authorization/roleAssignments/write`és `Microsoft.Authorization/roleAssignments/delete` engedélyek, például [a Felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator) vagy [a tulajdonos](built-in-roles.md#owner)

## <a name="access-control-iam"></a>Hozzáférés-vezérlés (IAM)

**A hozzáférés-vezérlés (IAM)** az a panel, amellyel szerepköröket rendelhet az Azure-erőforrásokhoz való hozzáférés engedélyezéséhez. Identitás- és hozzáférés-kezelésként is ismert, és az Azure Portalon több helyen is megjelenik. Az alábbi példa a hozzáférés-vezérlés (IAM) panel egy előfizetés.

![Hozzáférés-vezérlési (IAM) panel előfizetéshez](./media/role-assignments-portal/access-control-subscription.png)

Ahhoz, hogy a leghatékonyabb legyen a hozzáférés-vezérlés (IAM) panellel, akkor segít, ha a következő három kérdésre tud válaszolni, amikor szerepkört próbál hozzárendelni:

1. **Kinek van szüksége hozzáférésre?**

    Ki hivatkozik egy felhasználóra, csoportra, egyszerű szolgáltatásra vagy felügyelt identitásra. Ezt *rendszerbiztonsági tagnak is nevezik.*

1. **Milyen szerepre van szükségük?**

    Az engedélyek szerepkörökbe vannak csoportosítva. Több [beépített szerepkör](built-in-roles.md) listájából választhat, vagy használhatja a saját egyéni szerepköreit.

1. **Hol van szükségük hozzáférésre?**

    Ahol az erőforrások azon készletére vonatkozik, amelyekre a hozzáférés vonatkozik. Ahol lehet egy felügyeleti csoport, előfizetés, erőforráscsoport vagy egyetlen erőforrás, például egy tárfiók. Ezt *nevezzük hatókörnek.*

## <a name="add-a-role-assignment"></a>Szerepkör-hozzárendelés hozzáadása

Az Azure RBAC-ban egy Azure-erőforráshoz való hozzáférés engedélyezéséhez hozzáad egy szerepkör-hozzárendelést. A szerepkör hozzárendeléséhez kövesse az alábbi lépéseket.

1. Az Azure Portalon kattintson a **Minden szolgáltatás** elemre, majd válassza ki a hatókört, amely hozzáférést szeretne adni. Kiválaszthatja például a **Felügyeleti csoportok**, **Az Előfizetések**, **az Erőforráscsoportok**vagy egy erőforrás lehetőséget.

1. Kattintson az adott hatókör höz tartozó erőforrásra.

1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.

1. A **Szerepkör-hozzárendelések** fülre kattintva megtekintheti a hatókör szerepkör-hozzárendeléseit.

    ![Hozzáférés-vezérlés (IAM) és szerepkör-hozzárendelések lap](./media/role-assignments-portal/role-assignments.png)

1. Kattintson a > **Szerepkör-hozzárendelés hozzáadása gombra.** **Add**

   Ha nincs engedélye a szerepkörök hozzárendeléséhez, a Szerepkör-hozzárendelés hozzáadása beállítás le lesz tiltva.

   ![Hozzáadás menü](./media/role-assignments-portal/add-menu.png)

    Megnyílik a Szerepkör-hozzárendelés hozzáadása panel.

   ![Szerepkör-hozzárendelés hozzáadása ablaktábla](./media/role-assignments-portal/add-role-assignment.png)

1. A **Szerepkör** legördülő listájában válasszon ki egy szerepkört, például a **Virtuális gépek közreműködője** szerepkört.

1. A **Kijelölés** listában jelöljön ki egy felhasználót, csoportot, egyszerű szolgáltatást vagy felügyelt identitást. Ha a listában nem látja a rendszerbiztonsági tagot, írhat a **Kiválasztás** mezőbe megjelenítendő nevek, e-mail-címek és objektumazonosítók a címtárban történő kereséséhez.

1. Kattintson a **Mentés** gombra a szerepkör hozzárendeléséhez.

   Néhány pillanat múlva a rendszerbiztonsági tag a kijelölt hatókörhöz rendeli a szerepkört.

    ![Mentett szerepkör-hozzárendelés hozzáadása](./media/role-assignments-portal/add-role-assignment-save.png)

## <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Felhasználó hozzáadása egy előfizetés adminisztrátoraként

Ha egy felhasználót egy Azure-előfizetés rendszergazdájának szeretne tenni, rendelje hozzá a [tulajdonosi](built-in-roles.md#owner) szerepkört az előfizetéshatókörben. A Tulajdonos szerepkör teljes hozzáférést biztosít a felhasználónak az előfizetés összes erőforrásához, beleértve a másokszámára való hozzáférés engedélyezésére vonatkozó engedélyt is. Ez ugyanúgy történik, mint bármely más szerepkör-hozzárendelés esetében.

1. Az Azure Portalon kattintson az **Összes szolgáltatás** elemre, majd **az Előfizetések**elemre.

1. Kattintson arra az előfizetésre, amelynél a hozzáférést biztosítani kívánja.

1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.

1. Kattintson a **Szerepkör-hozzárendelések** fülre az előfizetés szerepkör-hozzárendelései megtekintéséhez.

    ![Hozzáférés-vezérlés (IAM) és szerepkör-hozzárendelések lap](./media/role-assignments-portal/role-assignments.png)

1. Kattintson a > **Szerepkör-hozzárendelés hozzáadása gombra.** **Add**

   Ha nincs engedélye a szerepkörök hozzárendeléséhez, a Szerepkör-hozzárendelés hozzáadása beállítás le lesz tiltva.

   ![Hozzáadás menü](./media/role-assignments-portal/add-menu.png)

    Megnyílik a Szerepkör-hozzárendelés hozzáadása panel.

   ![Szerepkör-hozzárendelés hozzáadása ablaktábla](./media/role-assignments-portal/add-role-assignment.png)

1. A **Szerepkör** legördülő listában válassza a **Tulajdonos** szerepkört.

1. A **kiválasztási** listában válasszon ki egy felhasználót. Ha a listában nem látja a felhasználót, beírhatja a nevét a **kiválasztási** mezőbe, hogy rákeressen a megjelenítendő neveket és e-mail-címeket tartalmazó címtárban.

1. Kattintson a **Mentés** gombra a szerepkör hozzárendeléséhez.

   Néhány pillanat múlva a rendszer hozzárendeli a felhasználót a Tulajdonos szerepkörhöz az előfizetés hatókörében.

## <a name="remove-a-role-assignment"></a>Szerepkör-hozzárendelés eltávolítása

Az Azure RBAC-ban egy Azure-erőforrás ból való hozzáférés eltávolításához eltávolít egy szerepkör-hozzárendelést. A szerepkör-hozzárendelés eltávolításához kövesse az alábbi lépéseket.

1. Nyissa meg **a hozzáférés-vezérlést (IAM)** egy hatókörön, például felügyeleti csoporton, előfizetésen, erőforráscsoporton vagy erőforráson, ahol el szeretné távolítani a hozzáférést.

1. Kattintson a **Szerepkör-hozzárendelések** lapra az előfizetés szerepkör-hozzárendeléseinek megtekintéséhez.

1. A szerepkör-hozzárendelések listájában jelölje be az eltávolítani kívánt szerepkör-hozzárendeléssel rendelkező rendszerbiztonsági tag melletti jelölőnégyzetet.

   ![Szerepkör-hozzárendelés eltávolítási üzenete](./media/role-assignments-portal/remove-role-assignment-select.png)

1. Kattintson az **Eltávolítás** lehetőségre.

   ![Szerepkör-hozzárendelés eltávolítási üzenete](./media/role-assignments-portal/remove-role-assignment.png)

1. A megjelenő szerepkör-hozzárendelési üzenet eltávolítása korválassza az **Igen**gombot.

    Ha olyan üzenetet lát, amely szerint az örökölt szerepkör-hozzárendelések nem távolíthatók el, akkor egy gyermekhatókörben próbál eltávolítani egy szerepkör-hozzárendelést. Nyissa meg a hozzáférés-vezérlést (IAM) azon a hatókörön, ahová a szerepkört hozzárendelte, majd próbálkozzon újra. A hozzáférés-vezérlés (IAM) megnyitásának gyors módja a megfelelő hatókörben, ha megnézi a **Hatókör oszlopot,** és az **(Örökölt)** gomb melletti hivatkozásra kattint.

   ![Szerepkör-hozzárendelés eltávolítási üzenete](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>További lépések

- [Szerepkör-hozzárendelések listázása az Azure RBAC és az Azure Portal használatával](role-assignments-list-portal.md)
- [Oktatóanyag: Felhasználói hozzáférés biztosítása az Azure-erőforrásokhoz az RBAC és az Azure Portal használatával](quickstart-assign-role-user-portal.md)
- [Az RBAC hibáiaz Azure-erőforrásokhoz](troubleshooting.md)
- [Erőforrások rendszerezése az Azure-beli felügyeleti csoportokkal](../governance/management-groups/overview.md)
