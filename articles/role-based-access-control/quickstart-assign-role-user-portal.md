---
title: Oktatóanyag – a felhasználó rbac-RÓL és az Azure portal segítségével Azure-erőforrásokhoz való hozzáférés biztosítása |} A Microsoft Docs
description: Megtudhatja, hogyan felhasználó szerepköralapú hozzáférés-vezérlés (RBAC) használatával az Azure Portalon az Azure-erőforrásokhoz való hozzáférést.
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
ms.openlocfilehash: 5786f7b48477fa705b43e3a953ac15b2c768bd71
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57854253"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-rbac-and-the-azure-portal"></a>Oktatóanyag: Egy felhasználó hozzáférést rbac-RÓL és az Azure portal segítségével Azure-erőforrások

[Szerepköralapú hozzáférés-vezérlés (RBAC)](overview.md) van az Azure-erőforrásokhoz való hozzáférés kezelése ugyanúgy. Ebben az oktatóanyagban egy erőforráscsoportba tartozó virtuális gépek létrehozása és kezelése egy felhasználó hozzáférést ad.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzáférés engedélyezése egy felhasználó egy erőforrás-csoport hatóköre:
> * Hozzáférés eltávolítása

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

1. Kattintson a navigációs lista **erőforráscsoportok**.

1. Kattintson a **Hozzáadás** megnyitásához a **erőforráscsoport** panelen.

   ![Új erőforráscsoport hozzáadása](./media/quickstart-assign-role-user-portal/resource-group.png)

1. A **erőforráscsoport-név**, adja meg **rbac-resource-group**.

1. Válasszon egy előfizetést és a egy helyet.

1. Kattintson a **létrehozás** az erőforráscsoport létrehozásához.

1. Kattintson a **frissítése** az erőforráscsoportok listájának frissítéséhez.

   Az új erőforráscsoport az erőforráscsoportok listájában jelenik meg.

   ![Erőforráscsoport-lista](./media/quickstart-assign-role-user-portal/resource-group-list.png)

## <a name="grant-access"></a>Hozzáférés biztosítása

Az RBAC-ben a hozzáférés biztosítása egy szerepkör-hozzárendelés létrehozásával történik.

1. Listájában **erőforráscsoportok**, kattintson az új **rbac-resource-group** erőforráscsoportot.

1. Kattintson a **hozzáférés-vezérlés (IAM)**.

1. Kattintson a **szerepkör-hozzárendelések** fülre, és tekintse meg a szerepkör-hozzárendelések listáját.

   ![Hozzáférés-vezérlés (IAM) panel erőforráscsoporthoz](./media/quickstart-assign-role-user-portal/access-control.png)

1. Kattintson a **Hozzáadás** > **szerepkör-hozzárendelés hozzáadása** a hozzáadása szerepkör-hozzárendelés panel megnyitásához.

   Ha nem rendelkezik engedélyekkel szerepkörök hozzárendeléséhez, a Hozzáadás szerepkör-hozzárendelés beállítás letiltásra kerül.

   ![Hozzáadásra szolgáló menü](./media/role-assignments-portal/add-menu.png)

   ![Szerepkör-hozzárendelési ablaktáblán hozzáadása](./media/quickstart-assign-role-user-portal/add-role-assignment.png)

1. A **Szerepkör** legördülő listájában válassza a **Virtuális gépek közreműködője** szerepkört.

1. A **Kiválasztás** listában válassza ki saját magát vagy egy másik felhasználót.

1. Kattintson a **mentése** a szerepkör-hozzárendelés létrehozásához.

   Néhány pillanat múlva a felhasználó társítva van a virtuális gépek Közreműködője szerepkör az rbac-resource-group erőforrás csoport hatókörben.

   ![Virtuális gépek közreműködője szerepkör hozzárendelése](./media/quickstart-assign-role-user-portal/vm-contributor-assignment.png)

## <a name="remove-access"></a>Hozzáférés eltávolítása

Az RBAC-ben hozzáférés eltávolításához egy szerepkör-hozzárendelést kell eltávolítania.

1. Szerepkör-hozzárendelések listájában adja hozzá a felhasználót a virtuális gépek Közreműködője szerepkör melletti jelölőnégyzet bejelölésekor.

1. Kattintson a **eltávolítása**.

   ![Szerepkör-hozzárendelés eltávolítási üzenete](./media/quickstart-assign-role-user-portal/remove-role-assignment.png)

1. A remove szerepkör hozzárendelése megjelenő üzenetben kattintson **Igen**.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

1. Kattintson a navigációs lista **erőforráscsoportok**.

1. Kattintson a **rbac-resource-group** , nyissa meg az erőforráscsoportot.

1. Kattintson a **erőforráscsoport törlése** , törölje az erőforráscsoportot.

   ![Erőforráscsoport törlése](./media/quickstart-assign-role-user-portal/delete-resource-group.png)

1. Az a **Opravdu chcete odstranit** panelen írja be az erőforráscsoport neve: **rbac-resource-group**.

1. Kattintson a **törlése** , törölje az erőforráscsoportot.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Egy felhasználó hozzáférést biztosítani Azure-erőforrások RBAC és az Azure PowerShell használatával](tutorial-role-assignments-user-powershell.md)

