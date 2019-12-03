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
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 25e11ae1311df9d0392340b32e0691298f78ee1c
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74710424"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-azure-portal"></a>Szerepkör-hozzárendelések listázása az Azure RBAC és a Azure Portal használatával

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] ez a cikk azt ismerteti, hogyan listázhatja a szerepkör-hozzárendeléseket a Azure Portal használatával.

## <a name="list-role-assignments-for-a-user-or-group"></a>Felhasználó vagy csoport szerepkör-hozzárendeléseinek listázása

Egy előfizetéshez tartozó felhasználóhoz vagy csoporthoz rendelt szerepköröket az **Azure-erőforrások** ablaktábla használatával tekintheti meg.

1. A Azure Portal kattintson a **minden szolgáltatás** elemre, majd válassza a **felhasználók** vagy **csoportok**lehetőséget.

1. Kattintson arra a felhasználóra vagy csoportra, amely számára a szerepkör-hozzárendeléseket ki szeretné listázni.

1. Kattintson az **Azure-erőforrások**elemre.

    A kiválasztott felhasználóhoz vagy csoporthoz rendelt szerepkörök listáját a különböző hatókörök, például a felügyeleti csoport, az előfizetés, az erőforráscsoport vagy az erőforrás részben tekintheti meg. Ez a lista tartalmazza az összes olyan szerepkör-hozzárendelést, amelyről olvasási engedéllyel rendelkezik.

    ![Felhasználó szerepkör-hozzárendelései](./media/role-assignments-list-portal/azure-resources-user.png)    

1. Az előfizetés módosításához kattintson az **előfizetések** listára.

## <a name="list-role-assignments-at-a-scope"></a>Hatókörhöz tartozó szerepkör-hozzárendelések listázása

1. A Azure Portal kattintson a **minden szolgáltatás** elemre, majd válassza ki a hatókört. Kiválaszthatja például a **felügyeleti csoportokat**, **előfizetéseket**, **erőforráscsoportokat**vagy egy erőforrást.

1. Kattintson az adott erőforrásra.

1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.

1. A **szerepkör-hozzárendelések** lapra kattintva megtekintheti az összes szerepkör-hozzárendelést ezen a hatókörön.

   ![Hozzáférés-vezérlés – szerepkör-hozzárendelések lap](./media/role-assignments-list-portal/access-control-role-assignments.png)

   A szerepkör-hozzárendelések lapon megtekintheti, hogy ki fér hozzá a hatókörhöz. Látható, hogy egyes szerepkörök hatóköre **erre az erőforrásra** érvényes, míg mások más hatókörökből **(öröklődnek)** . A hozzáférés vagy kifejezetten ehhez az erőforráshoz van hozzárendelve, vagy egy hozzárendelésből a szülő hatókörbe van származtatva.

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

## <a name="next-steps"></a>Következő lépések

- [Szerepkör-hozzárendelések hozzáadása vagy eltávolítása az Azure RBAC és a Azure Portal használatával](role-assignments-portal.md)
- [Azure-erőforrások RBAC kapcsolatos hibák](troubleshooting.md)
