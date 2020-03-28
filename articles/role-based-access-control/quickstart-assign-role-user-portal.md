---
title: 'Oktatóanyag: Felhasználói hozzáférés biztosítása az Azure-erőforrásokhoz az RBAC-mal és az Azure Portalon '
description: Ebben az oktatóanyagban megtudhatja, hogyan adhat hozzáférést egy felhasználónak az Azure-erőforrásokhoz szerepköralapú hozzáférés-vezérlés (RBAC) használatával az Azure Portalon.
services: role-based-access-control
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/22/2019
ms.author: rolyon
ms.openlocfilehash: 6281c9a1818a3b46ac4f7a62f8ae76668db56887
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77138090"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-rbac-and-the-azure-portal"></a>Oktatóanyag: Felhasználói hozzáférés biztosítása az Azure-erőforrásokhoz az RBAC és az Azure Portal használatával

[Szerepköralapú hozzáférés-vezérlés (RBAC)](overview.md) az Azure-erőforrásokhoz való hozzáférés kezelése. Ebben az oktatóanyagban hozzáférést biztosít a felhasználóknak virtuális gépek létrehozásához és kezeléséhez egy erőforráscsoportban.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzáférés megadása egy felhasználószámára egy erőforráscsoport hatókörén
> * Hozzáférés eltávolítása

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

1. A navigációs listában kattintson az **Erőforráscsoportok** elemre.

1. Kattintson a **Hozzáadás** gombra az **Erőforráscsoport** panel megnyitásához.

   ![Új erőforráscsoport hozzáadása](./media/quickstart-assign-role-user-portal/resource-group.png)

1. Az **Erőforráscsoport nevéhez**adja meg **az rbac-resource-group értéket.**

1. Válasszon egy előfizetést és a egy helyet.

1. Az erőforráscsoport létrehozásához kattintson a **Létrehozás** lehetőségre.

1. Kattintson a **Frissítés** gombra az erőforráscsoportok listájának frissítéséhez.

   Az új erőforráscsoport az erőforráscsoportok listájában jelenik meg.

   ![Erőforráscsoport-lista](./media/quickstart-assign-role-user-portal/resource-group-list.png)

## <a name="grant-access"></a>Hozzáférés biztosítása

Az RBAC-ben a hozzáférés biztosítása egy szerepkör-hozzárendelés létrehozásával történik.

1. Az **Erőforráscsoportok**listájában kattintson az új **rbac-erőforráscsoport** erőforráscsoportra.

1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.

1. A szerepkör-hozzárendelések aktuális listájának megtekintéséhez kattintson a **Szerepkör-hozzárendelés** fülre.

   ![Hozzáférés-vezérlés (IAM) panel erőforráscsoporthoz](./media/quickstart-assign-role-user-portal/access-control.png)

1. A Szerepkör-hozzárendelés **hozzáadása** > ablaktábla megnyitásához kattintson a**Szerepkör-hozzárendelés hozzáadása** gombra.

   Ha nincs engedélye a szerepkörök hozzárendeléséhez, a Szerepkör-hozzárendelés hozzáadása beállítás le lesz tiltva.

   ![Hozzáadás menü](./media/role-assignments-portal/add-menu.png)

   ![Szerepkör-hozzárendelés hozzáadása ablaktábla](./media/quickstart-assign-role-user-portal/add-role-assignment.png)

1. A **Szerepkör** legördülő listájában válassza a **Virtuális gépek közreműködője** szerepkört.

1. A **Kiválasztás** listában válassza ki saját magát vagy egy másik felhasználót.

1. Kattintson a **Mentés** gombra a szerepkör-hozzárendelés létrehozásához.

   Néhány pillanat múlva a felhasználó a virtuális gép közreműködői szerepkört rendeli hozzá az rbac-erőforrás-csoport erőforráscsoport hatókörén.

   ![Virtuális gépek közreműködője szerepkör hozzárendelése](./media/quickstart-assign-role-user-portal/vm-contributor-assignment.png)

## <a name="remove-access"></a>Hozzáférés eltávolítása

Az RBAC-ben hozzáférés eltávolításához egy szerepkör-hozzárendelést kell eltávolítania.

1. A szerepkör-hozzárendelések listájában jelölje be a virtuálisgép közreműködői szerepkörrel rendelkező felhasználót.

1. Kattintson az **Eltávolítás** lehetőségre.

   ![Szerepkör-hozzárendelés eltávolítási üzenete](./media/quickstart-assign-role-user-portal/remove-role-assignment.png)

1. A megjelenő szerepkör-hozzárendelési üzenet eltávolítása korválassza az **Igen**gombot.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

1. A navigációs listában kattintson az **Erőforráscsoportok** elemre.

1. Kattintson **az rbac-resource-group** elemre az erőforráscsoport megnyitásához.

1. Kattintson az **Erőforráscsoport törlése** gombra az erőforráscsoport törléséhez.

   ![Erőforráscsoport törlése](./media/quickstart-assign-role-user-portal/delete-resource-group.png)

1. A **Győződjön meg róla, hogy törölni szeretné** a panelt, írja be az erőforráscsoport nevét: **rbac-resource-group**.

1. Az erőforráscsoport törléséhez kattintson a **Törlés** gombra.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Felhasználói hozzáférés biztosítása az Azure-erőforrásokhoz az RBAC és az Azure PowerShell használatával](tutorial-role-assignments-user-powershell.md)
