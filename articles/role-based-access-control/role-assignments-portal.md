---
title: Azure-beli szerepkör-hozzárendelések hozzáadása vagy eltávolítása a Azure Portal-Azure RBAC használatával
description: Ismerje meg, hogyan biztosíthat hozzáférést az Azure-erőforrásokhoz felhasználók, csoportok, egyszerű szolgáltatások vagy felügyelt identitások számára a Azure Portal és az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 09/30/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 4e5c13c635091988f299d31c67795916e709d51a
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91597631"
---
# <a name="add-or-remove-azure-role-assignments-using-the-azure-portal"></a>Azure-beli szerepkör-hozzárendelés hozzáadása vagy eltávolítása az Azure Portal használatával

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] Ez a cikk azt ismerteti, hogyan rendelhet hozzá szerepköröket a Azure Portal használatával.

Ha Azure Active Directory rendszergazdai szerepköröket kell társítania, tekintse meg [a rendszergazdai szerepkörök megtekintése és társítása a Azure Active Directory-ben](../active-directory/users-groups-roles/directory-manage-roles-portal.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Szerepkör-hozzárendelések hozzáadásához vagy eltávolításához a következőket kell tennie:

- `Microsoft.Authorization/roleAssignments/write` és `Microsoft.Authorization/roleAssignments/delete` engedélyek, például a [felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator) vagy a [tulajdonos](built-in-roles.md#owner)

## <a name="access-control-iam"></a>Hozzáférés-vezérlés (IAM)

A **hozzáférés-vezérlés (iam)** az a lap, amelyet általában a szerepkörök hozzárendelésére használ az Azure-erőforrásokhoz való hozzáférés biztosításához. Az identitás-és hozzáférés-kezelés néven is ismert, és a Azure Portal több helyén is megjelenik. Az alábbi példa az előfizetés hozzáférés-vezérlés (IAM) oldalát mutatja be.

![Az előfizetés hozzáférés-vezérlés (IAM) lapja](./media/role-assignments-portal/access-control-subscription.png)

Ahhoz, hogy a leghatékonyabb legyen a hozzáférés-vezérlés (IAM) lapon, a szerepkörök hozzárendeléséhez kövesse az alábbi lépéseket.

1. Határozza meg, hogy kinek van szüksége hozzáférésre. Szerepkört hozzárendelhet egy felhasználóhoz, csoporthoz, egyszerű szolgáltatásnévhez vagy felügyelt identitáshoz.

1. Keresse meg a megfelelő szerepkört. Az engedélyek szerepkörökbe vannak csoportosítva. Több [Azure beépített szerepkörből](built-in-roles.md) is választhat, vagy használhatja saját egyéni szerepköreit is.

1. Azonosítsa a szükséges hatókört. Az Azure a hatókör négy szintjét biztosítja: [felügyeleti csoport](../governance/management-groups/overview.md), előfizetés, [erőforráscsoport](../azure-resource-manager/management/overview.md#resource-groups)és erőforrás. A hatókörrel kapcsolatos további információkért lásd a [hatókör ismertetése](scope-overview.md)című témakört.

1. Egy szerepkör hozzárendeléséhez hajtsa végre a következő részekben ismertetett lépéseket.

## <a name="add-a-role-assignment"></a>Szerepkör-hozzárendelés hozzáadása

Az Azure-RBAC az Azure-erőforrásokhoz való hozzáférés biztosításához hozzá kell adnia egy szerepkör-hozzárendelést. A szerepkörök hozzárendeléséhez kövesse az alábbi lépéseket.

1. A Azure Portal kattintson a **minden szolgáltatás** elemre, majd válassza ki azt a hatókört, amelyhez hozzáférést szeretne biztosítani. Kiválaszthatja például a **felügyeleti csoportokat**, **előfizetéseket**, **erőforráscsoportokat**vagy egy erőforrást.

1. Kattintson az adott hatókörhöz tartozó erőforrásra.

1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.

1. Kattintson a **szerepkör-hozzárendelések** lapra a szerepkör-hozzárendelések ezen a hatókörön való megtekintéséhez.

    ![Hozzáférés-vezérlés (IAM) és szerepkör-hozzárendelések lap](./media/role-assignments-portal/role-assignments.png)

1. Kattintson a **Hozzáadás**  >  **szerepkör-hozzárendelés hozzáadása**lehetőségre.

   Ha nem rendelkezik jogosultsággal a szerepkörök hozzárendeléséhez, a szerepkör-hozzárendelés hozzáadása lehetőség le lesz tiltva.

   ![Szerepkör-hozzárendelési menü hozzáadása](./media/shared/add-role-assignment-menu.png)

    Megnyílik a Szerepkör-hozzárendelés hozzáadása panel.

   ![Szerepkör-hozzárendelési ablaktábla hozzáadása](./media/role-assignments-portal/add-role-assignment.png)

1. A **Szerepkör** legördülő listájában válasszon ki egy szerepkört, például a **Virtuális gépek közreműködője** szerepkört.

1. A **Select (kiválasztás** ) listán válasszon ki egy felhasználót, csoportot, szolgáltatásnevet vagy felügyelt identitást. Ha a listában nem látja a rendszerbiztonsági tagot, írhat a **Kiválasztás** mezőbe megjelenítendő nevek, e-mail-címek és objektumazonosítók a címtárban történő kereséséhez.

1. Kattintson a **Mentés** gombra a szerepkör hozzárendeléséhez.

   Néhány pillanat elteltével a rendszerbiztonsági tag a kiválasztott hatókörhöz rendeli a szerepkört.

    ![Szerepkör-hozzárendelés mentett hozzáadása](./media/role-assignments-portal/add-role-assignment-save.png)

## <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Előfizetés rendszergazdai szerepkörének felhasználóhoz rendelése

Ahhoz, hogy egy felhasználó egy Azure-előfizetés rendszergazdája legyen, rendelje hozzá őket a [tulajdonosi](built-in-roles.md#owner) szerepkörhöz az előfizetés hatókörében. A tulajdonosi szerepkör teljes hozzáférést biztosít a felhasználó számára az előfizetés összes erőforrásához, beleértve a mások számára való hozzáférés biztosításának engedélyét is. Ez ugyanúgy történik, mint bármely más szerepkör-hozzárendelés esetében.

1. A Azure Portal kattintson a **minden szolgáltatás** , majd az **előfizetések**elemre.

1. Kattintson arra az előfizetésre, amelynél a hozzáférést biztosítani kívánja.

1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.

1. A **szerepkör-hozzárendelések** lapra kattintva megtekintheti az előfizetéshez tartozó szerepkör-hozzárendeléseket.

    ![Hozzáférés-vezérlés (IAM) és szerepkör-hozzárendelések lap](./media/role-assignments-portal/role-assignments.png)

1. Kattintson a **Hozzáadás**  >  **szerepkör-hozzárendelés hozzáadása**lehetőségre.

   Ha nem rendelkezik jogosultsággal a szerepkörök hozzárendeléséhez, a szerepkör-hozzárendelés hozzáadása lehetőség le lesz tiltva.

   ![Szerepkör-hozzárendelési menü hozzáadása az előfizetéshez](./media/shared/add-role-assignment-menu.png)

    Megnyílik a Szerepkör-hozzárendelés hozzáadása panel.

   ![Szerepkör-hozzárendelési ablaktábla hozzáadása az előfizetéshez](./media/role-assignments-portal/add-role-assignment.png)

1. A **Szerepkör** legördülő listában válassza a **Tulajdonos** szerepkört.

1. A **kiválasztási** listában válasszon ki egy felhasználót. Ha a listában nem látja a felhasználót, beírhatja a nevét a **kiválasztási** mezőbe, hogy rákeressen a megjelenítendő neveket és e-mail-címeket tartalmazó címtárban.

1. Kattintson a **Mentés** gombra a szerepkör hozzárendeléséhez.

   Néhány pillanat múlva a rendszer hozzárendeli a felhasználót a Tulajdonos szerepkörhöz az előfizetés hatókörében.

## <a name="add-a-role-assignment-for-a-managed-identity-preview"></a>Szerepkör-hozzárendelés hozzáadása felügyelt identitáshoz (előzetes verzió)

A felügyelt identitásokhoz szerepkör-hozzárendeléseket a jelen cikk korábbi részében ismertetett **hozzáférés-vezérlés (iam)** lap használatával adhat hozzá. Ha a hozzáférés-vezérlés (IAM) lapot használja, először a hatókörrel kell kezdődnie, majd ki kell választania a felügyelt identitást és szerepkört. Ez a szakasz egy másik módszert ismertet a szerepkör-hozzárendelések felügyelt identitásokhoz való hozzáadására. Ezekkel a lépésekkel először a felügyelt identitást kell használni, majd ki kell választania a hatókört és a szerepkört.

> [!IMPORTANT]
> A felügyelt identitás szerepkör-hozzárendelésének ezen alternatív lépések használatával történő hozzáadása jelenleg előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="system-assigned-managed-identity"></a>Rendszer által hozzárendelt felügyelt identitás

Az alábbi lépéseket követve rendeljen hozzá egy szerepkört egy rendszerhez rendelt felügyelt identitáshoz a felügyelt identitással.

1. A Azure Portal nyissa meg a rendszer által hozzárendelt felügyelt identitást.

1. A bal oldali menüben kattintson az **Identity (identitás**) elemre.

    ![Rendszer által hozzárendelt felügyelt identitás](./media/shared/identity-system-assigned.png)

1. Az **engedélyek**területen kattintson az **Azure szerepkör-hozzárendelések**elemre.

    Ha a szerepkörök már hozzá vannak rendelve a kiválasztott rendszerhez rendelt felügyelt identitáshoz, megjelenik a szerepkör-hozzárendelések listája. Ez a lista tartalmazza az összes olyan szerepkör-hozzárendelést, amelyről olvasási engedéllyel rendelkezik.

    ![Szerepkör-hozzárendelések egy rendszerhez rendelt felügyelt identitáshoz](./media/shared/role-assignments-system-assigned.png)

1. Az előfizetés módosításához kattintson az **előfizetés** listára.

1. Kattintson a **szerepkör-hozzárendelés hozzáadása (előzetes verzió)** elemre.

1. A legördülő lista segítségével kiválaszthatja, hogy a szerepkör-hozzárendelés mely erőforrásokra vonatkozzon, például az **előfizetés**, az **erőforráscsoport**vagy az erőforrás.

    Ha nem rendelkezik a szerepkör-hozzárendelés írási engedélyeivel a kiválasztott hatókörhöz, a rendszer egy beágyazott üzenetet jelenít meg. 

1. A **Szerepkör** legördülő listájában válasszon ki egy szerepkört, például a **Virtuális gépek közreműködője** szerepkört.

   ![Szerepkör-hozzárendelési ablaktábla hozzáadása a rendszerhez rendelt felügyelt identitáshoz](./media/role-assignments-portal/add-role-assignment-with-scope.png)

1. Kattintson a **Mentés** gombra a szerepkör hozzárendeléséhez.

   Néhány pillanat elteltével a felügyelt identitás hozzárendeli a szerepkört a kiválasztott hatókörhöz.

### <a name="user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás

Az alábbi lépéseket követve rendeljen hozzá egy szerepkört a felhasználóhoz rendelt felügyelt identitáshoz a felügyelt identitással.

1. A Azure Portal nyissa meg a felhasználó által hozzárendelt felügyelt identitást.

1. A bal oldali menüben kattintson az **Azure szerepkör-hozzárendelések**elemre.

    Ha a szerepkörök már hozzá vannak rendelve a kiválasztott felhasználóhoz rendelt felügyelt identitáshoz, megjelenik a szerepkör-hozzárendelések listája. Ez a lista tartalmazza az összes olyan szerepkör-hozzárendelést, amelyről olvasási engedéllyel rendelkezik.

    ![Felhasználó által hozzárendelt felügyelt identitás szerepkör-hozzárendelései](./media/shared/role-assignments-user-assigned.png)

1. Az előfizetés módosításához kattintson az **előfizetés** listára.

1. Kattintson a **szerepkör-hozzárendelés hozzáadása (előzetes verzió)** elemre.

1. A legördülő lista segítségével kiválaszthatja, hogy a szerepkör-hozzárendelés mely erőforrásokra vonatkozzon, például az **előfizetés**, az **erőforráscsoport**vagy az erőforrás.

    Ha nem rendelkezik a szerepkör-hozzárendelés írási engedélyeivel a kiválasztott hatókörhöz, a rendszer egy beágyazott üzenetet jelenít meg. 

1. A **Szerepkör** legördülő listájában válasszon ki egy szerepkört, például a **Virtuális gépek közreműködője** szerepkört.

   ![Szerepkör-hozzárendelési ablaktábla hozzáadása felhasználó által hozzárendelt felügyelt identitáshoz](./media/role-assignments-portal/add-role-assignment-with-scope.png)

1. Kattintson a **Mentés** gombra a szerepkör hozzárendeléséhez.

   Néhány pillanat elteltével a felügyelt identitás hozzárendeli a szerepkört a kiválasztott hatókörhöz.

## <a name="remove-a-role-assignment"></a>Szerepkör-hozzárendelés eltávolítása

Az Azure-RBAC az Azure-erőforrásokhoz való hozzáférés eltávolításához távolítsa el a szerepkör-hozzárendelést. A szerepkör-hozzárendelés eltávolításához kövesse az alábbi lépéseket.

1. Nyissa meg a **hozzáférés-vezérlést (iam)** egy hatókörön, például a felügyeleti csoport, az előfizetés, az erőforráscsoport vagy az erőforrás területen, ahol el szeretné távolítani a hozzáférést.

1. Kattintson a **Szerepkör-hozzárendelések** lapra az előfizetés szerepkör-hozzárendeléseinek megtekintéséhez.

1. A szerepkör-hozzárendelések listájában jelölje be az eltávolítani kívánt szerepkör-hozzárendeléssel rendelkező rendszerbiztonsági tag melletti jelölőnégyzetet.

   ![A szerepkör-hozzárendelés eltávolításra van kijelölve](./media/role-assignments-portal/remove-role-assignment-select.png)

1. Kattintson az **Eltávolítás** elemre.

   ![Szerepkör-hozzárendelés eltávolítási üzenete](./media/role-assignments-portal/remove-role-assignment.png)

1. A megjelenő szerepkör-hozzárendelés eltávolítása üzenetben kattintson az **Igen**gombra.

    Ha olyan üzenet jelenik meg, amely szerint az örökölt szerepkör-hozzárendelések nem távolíthatók el, a rendszer megpróbálja eltávolítani a szerepkör-hozzárendelést egy alárendelt hatókörben. Nyissa meg a hozzáférés-vezérlés (IAM) részt azon a hatókörön, ahol a szerepkör hozzá lett rendelve, és próbálkozzon újra. A hozzáférés-vezérlés (IAM) megfelelő hatókörben való megnyitásának gyorsan megtekinthető a **hatókör** oszlop, és a mellette lévő hivatkozásra kattintva **(örökölt)**.

   ![Szerepkör-hozzárendelési üzenet eltávolítása az örökölt szerepkör-hozzárendelésekhez](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>További lépések

- [Azure-beli szerepkör-hozzárendelések listázása a Azure Portal használatával](role-assignments-list-portal.md)
- [Oktatóanyag: felhasználói hozzáférés biztosítása az Azure-erőforrásokhoz a Azure Portal használatával](quickstart-assign-role-user-portal.md)
- [Az Azure RBAC hibáinak megoldása](troubleshooting.md)
- [Erőforrások rendszerezése az Azure-beli felügyeleti csoportokkal](../governance/management-groups/overview.md)
