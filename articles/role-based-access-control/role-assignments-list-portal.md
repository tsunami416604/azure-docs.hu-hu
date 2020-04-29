---
title: Szerepkör-hozzárendelések listázása az Azure RBAC és a Azure Portal használatával
description: Megtudhatja, hogyan határozhatja meg, hogy a felhasználók, csoportok, egyszerű szolgáltatások és felügyelt identitások milyen erőforrásokhoz férnek hozzá az Azure szerepköralapú hozzáférés-vezérlés (RBAC) és a Azure Portal használatához.
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
ms.date: 03/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 03a3d3c7d572d7ec5b8d3ac3d527d0d59e649bc2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80062248"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-azure-portal"></a>Szerepkör-hozzárendelések listázása az Azure RBAC és a Azure Portal használatával

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]Ez a cikk a szerepkör-hozzárendelések Azure Portal használatával történő listázását ismerteti.

> [!NOTE]
> Ha a szervezete egy Azure-beli [delegált erőforrás-kezelést](../lighthouse/concepts/azure-delegated-resource-management.md)használó szolgáltatónál kiszervezett felügyeleti funkciókat használ, az adott szolgáltató által meghatalmazott szerepkör-hozzárendelések nem jelennek meg.

## <a name="list-role-assignments-for-a-user-or-group"></a>Felhasználó vagy csoport szerepkör-hozzárendeléseinek listázása

Egy előfizetéshez tartozó felhasználóhoz vagy csoporthoz rendelt szerepköröket az **Azure-erőforrások** ablaktábla használatával tekintheti meg.

1. A Azure Portal kattintson a **minden szolgáltatás** elemre, majd válassza a **felhasználók** vagy **csoportok**lehetőséget.

1. Kattintson arra a felhasználóra vagy csoportra, amely számára a szerepkör-hozzárendeléseket ki szeretné listázni.

1. Kattintson az **Azure-erőforrások**elemre.

    A kiválasztott felhasználóhoz vagy csoporthoz rendelt szerepkörök listáját a különböző hatókörök, például a felügyeleti csoport, az előfizetés, az erőforráscsoport vagy az erőforrás részben tekintheti meg. Ez a lista tartalmazza az összes olyan szerepkör-hozzárendelést, amelyről olvasási engedéllyel rendelkezik.

    ![Felhasználó szerepkör-hozzárendelései](./media/role-assignments-list-portal/azure-resources-user.png)    

1. Az előfizetés módosításához kattintson az **előfizetések** listára.

## <a name="list-owners-of-a-subscription"></a>Előfizetés tulajdonosainak listázása

Az előfizetéshez tartozó [tulajdonosi](built-in-roles.md#owner) szerepkörrel rendelkező felhasználók az előfizetés összes adatát kezelhetik. Az alábbi lépéseket követve listázhatja az előfizetések tulajdonosait.

1. A Azure Portal kattintson a **minden szolgáltatás** , majd az **előfizetések**elemre.

1. Kattintson arra az előfizetésre, amelynek a tulajdonosait szeretné listázni.

1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.

1. Kattintson a **Szerepkör-hozzárendelések** lapra az előfizetés szerepkör-hozzárendeléseinek megtekintéséhez.

1. Görgessen a **tulajdonosok** szakaszhoz, és tekintse meg az előfizetéshez tartozó tulajdonosi szerepkörrel rendelkező összes felhasználót.

   ![Előfizetés hozzáférés-vezérlése – szerepkör-hozzárendelések lap](./media/role-assignments-list-portal/access-control-role-assignments-subscription.png)

## <a name="list-role-assignments-at-a-scope"></a>Hatókörhöz tartozó szerepkör-hozzárendelések listázása

1. A Azure Portal kattintson a **minden szolgáltatás** elemre, majd válassza ki a hatókört. Kiválaszthatja például a **felügyeleti csoportokat**, **előfizetéseket**, **erőforráscsoportokat**vagy egy erőforrást.

1. Kattintson az adott erőforrásra.

1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.

1. A **szerepkör-hozzárendelések** lapra kattintva megtekintheti az összes szerepkör-hozzárendelést ezen a hatókörön.

   ![Hozzáférés-vezérlés – szerepkör-hozzárendelések lap](./media/role-assignments-list-portal/access-control-role-assignments.png)

   A szerepkör-hozzárendelések lapon megtekintheti, hogy ki fér hozzá a hatókörhöz. Látható, hogy egyes szerepkörök hatóköre **erre az erőforrásra** érvényes, míg mások más hatókörökből **(öröklődnek)**. A hozzáférés vagy kifejezetten ehhez az erőforráshoz van hozzárendelve, vagy egy hozzárendelésből a szülő hatókörbe van származtatva.

## <a name="list-role-assignments-for-a-user-at-a-scope"></a>Hatókörhöz tartozó felhasználó szerepkör-hozzárendeléseinek listázása

Egy felhasználó, csoport, szolgáltatásnév vagy felügyelt identitás hozzáférésének listázásához listázhatja a szerepkör-hozzárendeléseit. Kövesse az alábbi lépéseket egy adott hatókörben egy felhasználó, csoport, szolgáltatásnév vagy felügyelt identitás szerepkör-hozzárendeléseinek listázásához.

1. A Azure Portal kattintson a **minden szolgáltatás** elemre, majd válassza ki a hatókört. Kiválaszthatja például a **felügyeleti csoportokat**, **előfizetéseket**, **erőforráscsoportokat**vagy egy erőforrást.

1. Kattintson az adott erőforrásra.

1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.

1. Kattintson a **hozzáférés-ellenőrzési** lapra.

    ![Hozzáférés-vezérlés – hozzáférés ellenőrzése lap](./media/role-assignments-list-portal/access-control-check-access.png)

1. A **Keresés** listában válassza ki, hogy milyen rendszerbiztonsági tag számára szeretné megtekinteni a hozzáférést.

1. A keresőmezőbe írja be a karakterláncot a megjelenítendő nevek, e-mail-címek vagy objektumazonosítók kereséséhez.

    ![Hozzáférés-kiválasztási lista kijelölése](./media/role-assignments-list-portal/check-access-select.png)

1. Kattintson a rendszerbiztonsági tag elemre a **hozzárendelések** panel megnyitásához.

    ![hozzárendelések ablaktábla](./media/role-assignments-list-portal/check-access-assignments.png)

    Ezen az ablaktáblán megtekintheti a kijelölt rendszerbiztonsági tag és a hatókörhöz rendelt szerepköröket. Ha ezen a hatókörön vannak megtagadási hozzárendelések, vagy a hatókör örökölt, a rendszer felsorolja őket.

## <a name="list-role-assignments-for-a-system-assigned-managed-identity"></a>A rendszerhez rendelt felügyelt identitás szerepkör-hozzárendeléseinek listázása

1. A Azure Portal nyissa meg a rendszer által hozzárendelt felügyelt identitást.

1. A bal oldali menüben kattintson az **Identity (identitás**) elemre.

    ![Rendszer által hozzárendelt felügyelt identitás](./media/role-assignments-list-portal/identity-system-assigned.png)

1. A **szerepkör-hozzárendelések**területen kattintson **az ehhez a felügyelt identitáshoz rendelt Azure RBAC-szerepkörök megjelenítése**elemre.

    A kiválasztott rendszerhez rendelt felügyelt identitáshoz rendelt szerepkörök listáját a különböző hatókörök, például a felügyeleti csoport, az előfizetés, az erőforráscsoport vagy az erőforrás tartalmazza. Ez a lista tartalmazza az összes olyan szerepkör-hozzárendelést, amelyről olvasási engedéllyel rendelkezik.

    ![Szerepkör-hozzárendelések egy rendszerhez rendelt felügyelt identitáshoz](./media/role-assignments-list-portal/azure-resources-system-assigned.png)

## <a name="list-role-assignments-for-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás szerepkör-hozzárendeléseinek listázása

1. A Azure Portal nyissa meg a felhasználó által hozzárendelt felügyelt identitást.

1. Kattintson az **Azure-erőforrások**elemre.

    A kiválasztott, felhasználó által hozzárendelt felügyelt identitáshoz rendelt szerepkörök listáját a különböző hatókörök, például a felügyeleti csoport, az előfizetés, az erőforráscsoport vagy az erőforrás részben tekintheti meg. Ez a lista tartalmazza az összes olyan szerepkör-hozzárendelést, amelyről olvasási engedéllyel rendelkezik.

    ![Szerepkör-hozzárendelések egy rendszerhez rendelt felügyelt identitáshoz](./media/role-assignments-list-portal/azure-resources-user-assigned.png)

1. Az előfizetés módosításához kattintson az **előfizetések** listára.

## <a name="list-number-of-role-assignments"></a>Szerepkör-hozzárendelések számának listázása

Az egyes előfizetésekben akár **2000** szerepkör-hozzárendelést is megadhat. A korlát nyomon követéséhez a **szerepkör-hozzárendelések** lapon szerepel egy diagram, amely felsorolja az aktuális előfizetéshez tartozó szerepkör-hozzárendelések számát.

![Hozzáférés-vezérlés – szerepkör-hozzárendelési diagram száma](./media/role-assignments-list-portal/access-control-role-assignments-chart.png)

Ha a maximális számhoz közeledik, és további szerepkör-hozzárendeléseket próbál hozzáadni, a **szerepkör-hozzárendelés hozzáadása** panelen figyelmeztetés jelenik meg. A szerepkör-hozzárendelések számának csökkentése érdekében lásd: az [Azure RBAC hibáinak megoldása](troubleshooting.md#azure-role-assignments-limit).

![Hozzáférés-vezérlés – szerepkör-hozzárendelési figyelmeztetés hozzáadása](./media/role-assignments-list-portal/add-role-assignment-warning.png)

## <a name="next-steps"></a>További lépések

- [Szerepkör-hozzárendelések hozzáadása vagy eltávolítása az Azure RBAC és a Azure Portal használatával](role-assignments-portal.md)
- [Azure-erőforrások RBAC kapcsolatos hibák](troubleshooting.md)
