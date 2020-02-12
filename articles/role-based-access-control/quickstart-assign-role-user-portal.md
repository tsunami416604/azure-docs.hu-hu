---
title: 'Oktatóanyag: felhasználói hozzáférés biztosítása az Azure-erőforrásokhoz a RBAC és a Azure Portal '
description: Ebből az oktatóanyagból megtudhatja, hogyan biztosíthat felhasználói hozzáférést az Azure-erőforrásokhoz a Azure Portal szerepköralapú hozzáférés-vezérlés (RBAC) használatával.
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
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77138090"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-rbac-and-the-azure-portal"></a>Oktatóanyag: felhasználói hozzáférés biztosítása az Azure-erőforrásokhoz a RBAC és a Azure Portal használatával

A [szerepköralapú hozzáférés-vezérlés (RBAC)](overview.md) az Azure-erőforrásokhoz való hozzáférés kezelésének módja. Ebben az oktatóanyagban felhasználói hozzáférést biztosít a virtuális gépek egy erőforráscsoporthoz való létrehozásához és kezeléséhez.

Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Hozzáférés biztosítása egy erőforráscsoport-hatókörben lévő felhasználó számára
> * Hozzáférés eltávolítása

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

1. A navigációs listában kattintson az **erőforráscsoportok**elemre.

1. Kattintson a **Hozzáadás** elemre az **erőforráscsoport** panel megnyitásához.

   ![Új erőforráscsoport hozzáadása](./media/quickstart-assign-role-user-portal/resource-group.png)

1. Az **erőforráscsoport neve**mezőbe írja be a következőt: **RBAC-Resource-Group**.

1. Válasszon egy előfizetést és a egy helyet.

1. Az erőforráscsoport létrehozásához kattintson a **Létrehozás** gombra.

1. Az erőforráscsoportok listájának frissítéséhez kattintson a **frissítés** gombra.

   Az új erőforráscsoport az erőforráscsoportok listájában jelenik meg.

   ![Erőforráscsoport-lista](./media/quickstart-assign-role-user-portal/resource-group-list.png)

## <a name="grant-access"></a>Hozzáférés biztosítása

Az RBAC-ben a hozzáférés biztosítása egy szerepkör-hozzárendelés létrehozásával történik.

1. Az **erőforráscsoportok**listájában kattintson az új **RBAC-Resource-Group** erőforráscsoport elemre.

1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.

1. Kattintson a **szerepkör-hozzárendelések** lapra a szerepkör-hozzárendelések aktuális listájának megtekintéséhez.

   ![Hozzáférés-vezérlés (IAM) panel erőforráscsoporthoz](./media/quickstart-assign-role-user-portal/access-control.png)

1. Kattintson a **hozzáadás** > **szerepkör-hozzárendelés hozzáadása** elemre a szerepkör-hozzárendelés hozzáadása ablaktábla megnyitásához.

   Ha nem rendelkezik jogosultsággal a szerepkörök hozzárendeléséhez, a szerepkör-hozzárendelés hozzáadása lehetőség le lesz tiltva.

   ![Menü hozzáadása](./media/role-assignments-portal/add-menu.png)

   ![Szerepkör-hozzárendelési ablaktábla hozzáadása](./media/quickstart-assign-role-user-portal/add-role-assignment.png)

1. A **Szerepkör** legördülő listájában válassza a **Virtuális gépek közreműködője** szerepkört.

1. A **Kiválasztás** listában válassza ki saját magát vagy egy másik felhasználót.

1. A szerepkör-hozzárendelés létrehozásához kattintson a **Mentés** gombra.

   Néhány pillanat elteltével a felhasználónak hozzá kell rendelnie a virtuálisgép-közreműködő szerepkört a RBAC-Resource-Group erőforráscsoport-hatókörhöz.

   ![Virtuális gépek közreműködője szerepkör hozzárendelése](./media/quickstart-assign-role-user-portal/vm-contributor-assignment.png)

## <a name="remove-access"></a>Hozzáférés eltávolítása

Az RBAC-ben hozzáférés eltávolításához egy szerepkör-hozzárendelést kell eltávolítania.

1. A szerepkör-hozzárendelések listájában vegyen fel egy pipát a felhasználó mellett a virtuális gép közreműködői szerepkörével.

1. Kattintson az **Eltávolítás**gombra.

   ![Szerepkör-hozzárendelés eltávolítási üzenete](./media/quickstart-assign-role-user-portal/remove-role-assignment.png)

1. A megjelenő szerepkör-hozzárendelés eltávolítása üzenetben kattintson az **Igen**gombra.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

1. A navigációs listában kattintson az **erőforráscsoportok**elemre.

1. Kattintson a **RBAC-Resource-Group (csoport** ) elemre az erőforráscsoport megnyitásához.

1. Az erőforráscsoport törléséhez kattintson az **erőforráscsoport törlése** elemre.

   ![Erőforráscsoport törlése](./media/quickstart-assign-role-user-portal/delete-resource-group.png)

1. A biztosan **törölni** kívánja a panelt, írja be az erőforráscsoport nevét: **RBAC-Resource-Group**.

1. Az erőforráscsoport törléséhez kattintson a **Törlés** gombra.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: felhasználói hozzáférés biztosítása az Azure-erőforrásokhoz a RBAC és a Azure PowerShell használatával](tutorial-role-assignments-user-powershell.md)
