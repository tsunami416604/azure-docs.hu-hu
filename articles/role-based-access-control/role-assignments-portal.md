---
title: Hozzáférés kezelése az RBAC és az Azure Portal használatával | Microsoft Docs
description: Megismerheti, hogyan kezelheti a felhasználók, csoportok és alkalmazások hozzáférését a szerepköralapú hozzáférés-vezérlés (RBAC) és az Azure Portal segítségével. Ez tartalmazza a hozzáférés felsorolásának, a hozzáférés adásának és a hozzáférés eltávolításának módját.
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
ms.date: 07/11/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: aaa36d850516ff4d8e40b62c588347468da5c6d2
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008161"
---
# <a name="manage-access-using-rbac-and-the-azure-portal"></a>Hozzáférés kezelése az RBAC és az Azure Portal használatával

A [szerepköralapú hozzáférés-vezérlés (RBAC)](overview.md) az erőforrásokhoz való hozzáférés kezelésének a módja az Azure-ban. A cikk bemutatja, hogyan kezelheti a felhasználók, csoportok és alkalmazások hozzáférését az RBAC és az Azure Portal segítségével.

## <a name="list-roles"></a>Szerepkörök felsorolása

A szerepkör-definíció a szerepkör-hozzárendeléshez használható engedélyek gyűjteménye. Az Azure több mint 60 [beépített szerepkörrel](built-in-roles.md) rendelkezik.

1. Az Azure Portalon válassza a **Minden szolgáltatás**, majd az **Előfizetések** elemet.

1. Válassza ki az előfizetését.

1. Válassza a **Hozzáférés-vezérlés (IAM)** elemet.

   ![Szerepkörök lehetőség](./media/role-assignments-portal/list-subscription-access-control.png)

1. A beépített és egyéni szerepkörök listájának megtekintéséhez válassza a **Szerepkörök** elemet.

   ![Szerepkörök lehetőség](./media/role-assignments-portal/roles-option.png)

   Megtekintheti az egyes szerepkörökhöz rendelt felhasználók és csoportok számát.

   ![Szerepkörök listája](./media/role-assignments-portal/roles-list.png)

## <a name="list-access"></a>Hozzáférések felsorolása

A hozzáférések kezelésekor szeretné tudni, kinek van hozzáférése, milyen engedélyekkel rendelkezik és milyen szinten. A hozzáférések felsorolásához listázza a szerepkör-hozzárendeléseket.

### <a name="list-role-assignments-for-a-subscription"></a>Előfizetés szerepkör-hozzárendeléseinek felsorolása

1. Az Azure Portalon válassza a **Minden szolgáltatás**, majd az **Előfizetések** elemet.

1. Válassza ki az előfizetését.

1. Válassza a **Hozzáférés-vezérlés (IAM)** elemet.

    A Hozzáférés-vezérlés (IAM) (más néven identitás- és hozzáférés-kezelés) panelen megtekintheti, ki fér hozzá az előfizetéshez, illetve az ő szerepköreiket.

    ![Hozzáférés-vezérlés (IAM) panel](./media/role-assignments-portal/subscription-access-control.png)

    Az RBAC-modellben a hagyományos előfizetések adminisztrátorai és társadminisztrátorai minősülnek az előfizetés tulajdonosának.


### <a name="list-role-assignments-for-a-resource-group"></a>Erőforráscsoport szerepkör-hozzárendeléseinek felsorolása

1. A navigációs listában válassza az **Erőforráscsoportok** lehetőséget.

1. Először válasszon ki egy erőforráscsoportot, majd a **Hozzáférés-vezérlés (IAM)** lehetőséget.

   A Hozzáférés-vezérlés (IAM) panelen megtekintheti, ki fér hozzá az erőforráscsoporthoz. Látható, hogy egyes szerepkörök hatóköre **erre az erőforrásra** érvényes, míg mások más hatókörökből **(öröklődnek)**. A hozzáférés lehet kifejezetten az erőforráscsoporthoz rendelt, vagy a szülő előfizetés egyik hozzárendeléséből örökölt.

   ![Erőforráscsoportok](./media/role-assignments-portal/resource-group-access-control.png)

### <a name="list-role-assignments-for-a-user"></a>Felhasználó szerepkör-hozzárendeléseinek felsorolása

1. A navigációs listában válassza az **Azure Active Directory** lehetőséget.

1. Válassza a **Felhasználók** lehetőséget a **Minden felhasználó** panel megnyitásához.

   ![Azure Active Directory – Minden felhasználó panel](./media/role-assignments-portal/aad-all-users.png)

1. A listából válasszon ki egy egyéni felhasználót.

1. A **Kezelés** szakaszban válassza az **Azure-erőforrások** lehetőséget.

   ![Azure Active Directory-felhasználó Azure-erőforrásai](./media/role-assignments-portal/aad-user-azure-resources.png)

   Az Azure-erőforrások panelen láthatja a szerepkör-hozzárendeléseit a kijelölt felhasználó és a kijelölt előfizetésben. Ez a lista csak szerepkör-hozzárendeléseit olvasási engedéllyel rendelkező erőforrásokat tartalmazza. Például ha a felhasználó is rendelkezik, amely nem olvasható szerepkör-hozzárendeléseket, azokat a szerepkör-hozzárendeléseket nem jelenik a listában.

1. Ha több előfizetéssel rendelkezik, akkor választhatja a **előfizetés** legördülő listából válassza ki egy másik előfizetésben található a szerepkör-hozzárendelések megtekintéséhez.

## <a name="grant-access"></a>Hozzáférés biztosítása

Az RBAC-ben a hozzáférés biztosítása egy szerepkör-hozzárendelés létrehozásával történik.

### <a name="create-a-role-assignment-at-a-subscription-scope"></a>Szerepkör-hozzárendelés létrehozása előfizetési hatókörben

1. Az Azure Portalon válassza a **Minden szolgáltatás**, majd az **Előfizetések** elemet.

1. Válassza ki az előfizetését.

1. Válassza a **Hozzáférés-vezérlés (IAM)** lehetőséget az előfizetési hatókörben található szerepkör-hozzárendelések aktuális listájának megtekintéséhez.

   ![Hozzáférés-vezérlés (IAM) panel erőforráscsoporthoz](./media/role-assignments-portal/grant-subscription-access-control.png)

1. Kattintson a **Hozzáadás** elemre az **Engedélyek hozzáadása** panel megnyitásához.

   Ha nem rendelkezik a szerepkörök hozzárendeléséhez szükséges engedéllyel, a **Hozzáadás** lehetőség nem jelenik meg.

   ![Engedélyek hozzáadása panel](./media/role-assignments-portal/add-permissions.png)

1. A **Szerepkör** legördülő listájában válasszon ki egy szerepkört, például a **Virtuális gépek közreműködője** szerepkört.

1. A **Kiválasztás** listában válasszon ki egy felhasználót, csoportot vagy alkalmazást. Ha a listában nem látja a rendszerbiztonsági tagot, írhat a **Kiválasztás** mezőbe megjelenítendő nevek, e-mail-címek és objektumazonosítók a címtárban történő kereséséhez.

1. Kattintson a **Mentés** gombra a szerepkör-hozzárendelés létrehozásához.

   Néhány pillanat múlva a rendszerbiztonsági tagot a rendszer hozzárendeli a szerepkörhöz az előfizetés hatókörében.

### <a name="create-a-role-assignment-at-a-resource-group-scope"></a>Szerepkör-hozzárendelés létrehozása erőforráscsoporti hatókörben

1. A navigációs listában válassza az **Erőforráscsoportok** lehetőséget.

1. Válasszon ki egy erőforráscsoportot.

1. Válassza a **Hozzáférés-vezérlés (IAM)** lehetőséget az erőforráscsoporti hatókörben található szerepkör-hozzárendelések aktuális listájának megtekintéséhez.

   ![Hozzáférés-vezérlés (IAM) panel erőforráscsoporthoz](./media/role-assignments-portal/grant-resource-group-access-control.png)

1. Kattintson a **Hozzáadás** elemre az **Engedélyek hozzáadása** panel megnyitásához.

   Ha nem rendelkezik a szerepkörök hozzárendeléséhez szükséges engedéllyel, a **Hozzáadás** lehetőség nem jelenik meg.

   ![Engedélyek hozzáadása panel](./media/role-assignments-portal/add-permissions.png)

1. A **Szerepkör** legördülő listájában válasszon ki egy szerepkört, például a **Virtuális gépek közreműködője** szerepkört.

1. A **Kiválasztás** listában válasszon ki egy felhasználót, csoportot vagy alkalmazást. Ha a listában nem látja a rendszerbiztonsági tagot, írhat a **Kiválasztás** mezőbe megjelenítendő nevek, e-mail-címek és objektumazonosítók a címtárban történő kereséséhez.

1. Kattintson a **Mentés** gombra a szerepkör-hozzárendelés létrehozásához.

   Néhány pillanat múlva a rendszerbiztonsági tagot a rendszer hozzárendeli a szerepkörhöz az erőforráscsoporti hatókörben.

## <a name="remove-access"></a>Hozzáférés eltávolítása

Az RBAC-ben hozzáférés eltávolításához egy szerepkör-hozzárendelést kell eltávolítania.

### <a name="remove-a-role-assignment"></a>Szerepkör-hozzárendelés eltávolítása

1. Nyissa meg azon előfizetés, erőforráscsoport, vagy erőforrás **Hozzáférés-vezérlés (IAM)** paneljét, amely az eltávolítani kívánt szerepkör-hozzárendelést tartalmazza.

1. A szerepkör-hozzárendelések listájában jelölje be az eltávolítani kívánt szerepkör-hozzárendeléssel rendelkező rendszerbiztonsági tag melletti jelölőnégyzetet.

   ![Szerepkör-hozzárendelés eltávolítási üzenete](./media/role-assignments-portal/remove-role-assignment-select.png)

1. Válassza az **Eltávolítás** lehetőséget.

   ![Szerepkör-hozzárendelés eltávolítási üzenete](./media/role-assignments-portal/remove-role-assignment.png)

1. A megjelenő, a szerepkör-hozzárendelés eltávolításáról szóló üzenetben válassza az **Igen** lehetőséget.

Az örökölt szerepkör-hozzárendeléseket nem lehet eltávolítani. Ha örökölt szerepkör-hozzárendelést kell eltávolítania, azt abban a hatókörben kell megtennie, ahol a szerepkör-hozzárendelés létrejött. A **Hatókör** oszlopban az **Örökölt** elem melletti hivatkozás azokra az erőforrásokra mutat, ahol az adott szerepkör hozzárendelése megtörtént. Lépjen az ott szereplő hatókörhöz a szerepkör-hozzárendelés eltávolításához.

## <a name="next-steps"></a>További lépések

* [Rövid útmutató: Hozzáférés biztosítása egy felhasználó számára az RBAC és az Azure Portal használatával](quickstart-assign-role-user-portal.md)
* [Oktatóanyag: Hozzáférés biztosítása egy felhasználó számára az RBAC és az Azure PowerShell használatával](tutorial-role-assignments-user-powershell.md)
* [Beépített szerepkörök](built-in-roles.md)
