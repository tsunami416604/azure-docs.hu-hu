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
ms.date: 09/05/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 14ac23e6b69302ac412aac3ecab06345e5d722fd
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44296346"
---
# <a name="manage-access-using-rbac-and-the-azure-portal"></a>Hozzáférés kezelése az RBAC és az Azure Portal használatával

A [szerepköralapú hozzáférés-vezérlés (RBAC)](overview.md) az erőforrásokhoz való hozzáférés kezelésének a módja az Azure-ban. A cikk bemutatja, hogyan kezelheti a felhasználók, csoportok és alkalmazások hozzáférését az RBAC és az Azure Portal segítségével.

## <a name="list-roles"></a>Szerepkörök felsorolása

A szerepkör-definíció a szerepkör-hozzárendeléshez használható engedélyek gyűjteménye. Az Azure rendelkezik több mint 70 [beépített szerepkörök](built-in-roles.md). Kövesse az alábbi lépéseket a portálon a szerepkörök listáját.

1. Az Azure Portalon válassza a **Minden szolgáltatás**, majd az **Előfizetések** elemet.

1. Válassza ki az előfizetését.

1. Válassza a **Hozzáférés-vezérlés (IAM)** elemet.

   ![Szerepkörök lehetőség](./media/role-assignments-portal/list-subscription-access-control.png)

1. A beépített és egyéni szerepkörök listájának megtekintéséhez válassza a **Szerepkörök** elemet.

   ![Szerepkörök lehetőség](./media/role-assignments-portal/roles-option.png)

   Megtekintheti az egyes szerepkörökhöz rendelt felhasználók és csoportok számát.

   ![Szerepkörök listája](./media/role-assignments-portal/roles-list.png)

## <a name="list-access"></a>Hozzáférések felsorolása

A hozzáférések kezelésekor szeretné tudni, kinek van hozzáférése, milyen engedélyekkel rendelkezik és milyen szinten. A hozzáférések felsorolásához listázza a szerepkör-hozzárendeléseket. Kövesse az alábbi lépéseket a felhasználók hozzáférését listázása és a lista a különféle hatókörű hozzáférést.

### <a name="list-role-assignments-for-a-user"></a>Felhasználó szerepkör-hozzárendeléseinek felsorolása

1. A navigációs listában válassza az **Azure Active Directory** lehetőséget.

1. Válassza a **Felhasználók** lehetőséget a **Minden felhasználó** panel megnyitásához.

   ![Azure Active Directory – Minden felhasználó panel](./media/role-assignments-portal/aad-all-users.png)

1. A listából válasszon ki egy egyéni felhasználót.

1. A **Kezelés** szakaszban válassza az **Azure-erőforrások** lehetőséget.

   ![Azure Active Directory-felhasználó Azure-erőforrásai](./media/role-assignments-portal/aad-user-azure-resources.png)

   Az Azure-erőforrások panelen láthatja a szerepkör-hozzárendeléseit a kijelölt felhasználó és a kijelölt előfizetésben. Ez a lista csak szerepkör-hozzárendeléseit olvasási engedéllyel rendelkező erőforrásokat tartalmazza. Például ha a felhasználó is rendelkezik, amely nem olvasható szerepkör-hozzárendeléseket, azokat a szerepkör-hozzárendeléseket nem jelenik a listában.

1. Ha több előfizetéssel rendelkezik, akkor választhatja a **előfizetés** legördülő listából válassza ki egy másik előfizetésben található a szerepkör-hozzárendelések megtekintéséhez.

### <a name="list-role-assignments-for-a-resource-group"></a>Erőforráscsoport szerepkör-hozzárendeléseinek felsorolása

1. A navigációs listában válassza az **Erőforráscsoportok** lehetőséget.

1. Először válasszon ki egy erőforráscsoportot, majd a **Hozzáférés-vezérlés (IAM)** lehetőséget.

   A hozzáférés-vezérlés (IAM) paneljén, más néven identitás- és hozzáférés-kezelés megtekintheti, ki férhet hozzá az erőforráscsoporthoz. Látható, hogy egyes szerepkörök hatóköre **erre az erőforrásra** érvényes, míg mások más hatókörökből **(öröklődnek)**. A hozzáférés lehet kifejezetten az erőforráscsoporthoz rendelt, vagy a szülő előfizetés egyik hozzárendeléséből örökölt.

   ![Erőforráscsoportok](./media/role-assignments-portal/resource-group-access-control.png)

### <a name="list-role-assignments-for-a-subscription"></a>Előfizetés szerepkör-hozzárendeléseinek felsorolása

1. Az Azure Portalon válassza a **Minden szolgáltatás**, majd az **Előfizetések** elemet.

1. Válassza ki az előfizetését.

1. Válassza a **Hozzáférés-vezérlés (IAM)** elemet.

    A hozzáférés-vezérlés (IAM) panelen láthatja, ki férhet hozzá ehhez az előfizetéshez, és azok szerepét.

    ![Egy előfizetés hozzáférés-vezérlés (IAM) paneljén](./media/role-assignments-portal/subscription-access-control.png)

    Klasszikus előfizetés rendszergazdái és társadminisztrátorai minősülnek tulajdonosoknak az RBAC-modellben.

### <a name="list-role-assignments-for-a-management-group"></a>Szerepkör-hozzárendelések lista felügyeleti csoport

1. Az Azure Portalon válassza ki a **minden szolgáltatás** , majd **felügyeleti csoportok**.

1. Válassza ki a felügyeleti csoportban.

1. Válasszon **(részletek)** a kiválasztott felügyeleti csoport számára.

    ![Felügyeleti csoportok](./media/role-assignments-portal/management-groups-list.png)

1. Válassza a **Hozzáférés-vezérlés (IAM)** elemet.

    A hozzáférés-vezérlés (IAM) panelen láthatja, ki férhet hozzá a felügyeleti csoport és azok szerepét.

    ![Hozzáférés-vezérlés (IAM) paneljén, egy felügyeleti csoport](./media/role-assignments-portal/management-groups-access-control.png)

## <a name="grant-access"></a>Hozzáférés biztosítása

RBAC hozzáférést biztosítani, rendeljen hozzá egy szerepkörhöz. Kövesse az alábbi lépéseket a különféle hatókörű hozzáférést.

### <a name="assign-a-role-at-a-resource-group-scope"></a>Egy erőforrás-csoport hatókörben szerepkör hozzárendelése

1. A navigációs listában válassza az **Erőforráscsoportok** lehetőséget.

1. Válasszon ki egy erőforráscsoportot.

1. Válassza a **Hozzáférés-vezérlés (IAM)** lehetőséget az erőforráscsoporti hatókörben található szerepkör-hozzárendelések aktuális listájának megtekintéséhez.

   ![Hozzáférés-vezérlés (IAM) paneljén erőforráscsoport](./media/role-assignments-portal/grant-resource-group-access-control.png)

1. Kattintson a **Hozzáadás** elemre az **Engedélyek hozzáadása** panel megnyitásához.

   Ha nem rendelkezik a szerepkörök hozzárendeléséhez szükséges engedéllyel, a **Hozzáadás** lehetőség nem jelenik meg.

   ![Engedélyek hozzáadása panel](./media/role-assignments-portal/add-permissions.png)

1. A **Szerepkör** legördülő listájában válasszon ki egy szerepkört, például a **Virtuális gépek közreműködője** szerepkört.

1. A **Kiválasztás** listában válasszon ki egy felhasználót, csoportot vagy alkalmazást. Ha a listában nem látja a rendszerbiztonsági tagot, írhat a **Kiválasztás** mezőbe megjelenítendő nevek, e-mail-címek és objektumazonosítók a címtárban történő kereséséhez.

1. Válasszon **mentése** a szerepkör hozzárendelése.

   Néhány pillanat múlva a rendszerbiztonsági tagot a rendszer hozzárendeli a szerepkörhöz az erőforráscsoporti hatókörben.

### <a name="assign-a-role-at-a-subscription-scope"></a>Egy előfizetési hatókörben szerepkör hozzárendelése

1. Az Azure Portalon válassza a **Minden szolgáltatás**, majd az **Előfizetések** elemet.

1. Válassza ki az előfizetését.

1. Válassza a **Hozzáférés-vezérlés (IAM)** lehetőséget az előfizetési hatókörben található szerepkör-hozzárendelések aktuális listájának megtekintéséhez.

   ![Egy előfizetés hozzáférés-vezérlés (IAM) paneljén](./media/role-assignments-portal/grant-subscription-access-control.png)

1. Kattintson a **Hozzáadás** elemre az **Engedélyek hozzáadása** panel megnyitásához.

   Ha nem rendelkezik a szerepkörök hozzárendeléséhez szükséges engedéllyel, a **Hozzáadás** lehetőség nem jelenik meg.

   ![Engedélyek hozzáadása panel](./media/role-assignments-portal/add-permissions.png)

1. A **Szerepkör** legördülő listájában válasszon ki egy szerepkört, például a **Virtuális gépek közreműködője** szerepkört.

1. A **Kiválasztás** listában válasszon ki egy felhasználót, csoportot vagy alkalmazást. Ha a listában nem látja a rendszerbiztonsági tagot, írhat a **Kiválasztás** mezőbe megjelenítendő nevek, e-mail-címek és objektumazonosítók a címtárban történő kereséséhez.

1. Válasszon **mentése** a szerepkör hozzárendelése.

   Néhány pillanat múlva a rendszerbiztonsági tagot a rendszer hozzárendeli a szerepkörhöz az előfizetés hatókörében.

### <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Az előfizetés rendszergazda felhasználó hozzárendelése

Ahhoz, hogy a felhasználó az Azure-előfizetés rendszergazdájának, hozzárendelheti azokat a [tulajdonosa](built-in-roles.md#owner) szerepkör az előfizetések szintjén. A tulajdonosi szerepkör a felhasználó teljes hozzáférést biztosít, az összes erőforrást az előfizetésben, beleértve a jogot arra, hogy mások való hozzáférés delegálására. Ezek a lépések ugyanazok, mint bármilyen más szerepkör-hozzárendelés.

1. Az Azure Portalon válassza a **Minden szolgáltatás**, majd az **Előfizetések** elemet.

1. Válassza ki az előfizetését.

1. Válassza a **Hozzáférés-vezérlés (IAM)** lehetőséget az előfizetési hatókörben található szerepkör-hozzárendelések aktuális listájának megtekintéséhez.

   ![Egy előfizetés hozzáférés-vezérlés (IAM) paneljén](./media/role-assignments-portal/grant-subscription-access-control.png)

1. Kattintson a **Hozzáadás** elemre az **Engedélyek hozzáadása** panel megnyitásához.

   Ha nem rendelkezik a szerepkörök hozzárendeléséhez szükséges engedéllyel, a **Hozzáadás** lehetőség nem jelenik meg.

   ![Engedélyek hozzáadása panel](./media/role-assignments-portal/add-permissions.png)

1. Az a **szerepkör** legördülő listában válassza a **tulajdonosa** szerepkör.

1. Az a **kiválasztása** listában, válasszon ki egy felhasználót. Ha nem látja a felhasználónévre a listában, megadhatja a **kiválasztása** mezőben keressen a könyvtárban, a megjelenített nevek és e-mail-címeket.

1. Válasszon **mentése** a szerepkör hozzárendelése.

   Néhány pillanat múlva a felhasználó hozzárendelve a tulajdonosi szerepkör az előfizetések szintjén.

### <a name="assign-a-role-at-a-management-group-scope"></a>Egy felügyeleti csoport hatókörű szerepkör hozzárendelése

1. Az Azure Portalon válassza ki a **minden szolgáltatás** , majd **felügyeleti csoportok**.

1. Válassza ki a felügyeleti csoportban.

1. Válasszon **(részletek)** a kiválasztott felügyeleti csoport számára.

    ![Felügyeleti csoportok](./media/role-assignments-portal/management-groups-list.png)

1. Válassza a **Hozzáférés-vezérlés (IAM)** lehetőséget az előfizetési hatókörben található szerepkör-hozzárendelések aktuális listájának megtekintéséhez.

   ![Hozzáférés-vezérlés (IAM) paneljén, egy felügyeleti csoport](./media/role-assignments-portal/grant-management-groups-access-control.png)

1. Kattintson a **Hozzáadás** elemre az **Engedélyek hozzáadása** panel megnyitásához.

   Ha nem rendelkezik a szerepkörök hozzárendeléséhez szükséges engedéllyel, a **Hozzáadás** lehetőség nem jelenik meg.

   ![Engedélyek hozzáadása panel](./media/role-assignments-portal/add-permissions-management-groups.png)

1. Az a **szerepkör** legördülő listában válassza ki a megfelelő szerepkör **felügyeleti csoport közreműködői**.

    Információ a támogatott műveletekről a különböző szerepkörökhöz tartozó felügyeleti csoportok: [az erőforrások rendszerezéséhez az Azure felügyeleti csoportok](../azure-resource-manager/management-groups-overview.md#management-group-access).

1. A **Kiválasztás** listában válasszon ki egy felhasználót, csoportot vagy alkalmazást. Ha a listában nem látja a rendszerbiztonsági tagot, írhat a **Kiválasztás** mezőbe megjelenítendő nevek, e-mail-címek és objektumazonosítók a címtárban történő kereséséhez.

1. Válasszon **mentése** a szerepkör hozzárendelése.

   Néhány pillanat múlva a rendszerbiztonsági tag szerepköre a a felügyeleti csoport hatókörben.

## <a name="remove-access"></a>Hozzáférés eltávolítása

Az RBAC-ben hozzáférés eltávolításához egy szerepkör-hozzárendelést kell eltávolítania. Kövesse az alábbi lépéseket a hozzáférést.

### <a name="remove-a-role-assignment"></a>Szerepkör-hozzárendelés eltávolítása

1. Nyissa meg a **hozzáférés-vezérlés (IAM)** paneljén a felügyeleti csoportban, előfizetés, erőforráscsoport vagy erőforrás, amely rendelkezik a szerepkör-hozzárendelés el kívánja távolítani.

1. A szerepkör-hozzárendelések listájában jelölje be az eltávolítani kívánt szerepkör-hozzárendeléssel rendelkező rendszerbiztonsági tag melletti jelölőnégyzetet.

   ![Szerepkör-hozzárendelés eltávolítási üzenete](./media/role-assignments-portal/remove-role-assignment-select.png)

1. Válassza az **Eltávolítás** lehetőséget.

   ![Szerepkör-hozzárendelés eltávolítási üzenete](./media/role-assignments-portal/remove-role-assignment.png)

1. A megjelenő, a szerepkör-hozzárendelés eltávolításáról szóló üzenetben válassza az **Igen** lehetőséget.

    Az örökölt szerepkör-hozzárendeléseket nem lehet eltávolítani. Ha örökölt szerepkör-hozzárendelést kell eltávolítania, azt abban a hatókörben kell megtennie, ahol a szerepkör-hozzárendelés létrejött. Az a **hatókör** oszlopban, az **(örökölt)** egy hivatkozást, amellyel a hatókör, amelyekhez ezt a szerepkört hozzá volt rendelve. Lépjen az ott szereplő hatókörhöz a szerepkör-hozzárendelés eltávolításához.

   ![Szerepkör-hozzárendelés eltávolítási üzenete](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>További lépések

* [Rövid útmutató: Hozzáférés biztosítása egy felhasználó számára az RBAC és az Azure Portal használatával](quickstart-assign-role-user-portal.md)
* [Oktatóanyag: Hozzáférés biztosítása egy felhasználó számára az RBAC és az Azure PowerShell használatával](tutorial-role-assignments-user-powershell.md)
* [Beépített szerepkörök](built-in-roles.md)
* [Az erőforrások rendszerezéséhez az Azure felügyeleti csoportok](../azure-resource-manager/management-groups-overview.md)
