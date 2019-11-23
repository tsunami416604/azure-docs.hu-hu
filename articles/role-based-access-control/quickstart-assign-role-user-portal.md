---
title: Tutorial - Grant a user access to Azure resources using RBAC and the Azure portal
description: In this tutorial, learn how to grant a user access to Azure resources using role-based access control (RBAC) in the Azure portal.
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
ms.openlocfilehash: f4dd3995df2a068824c4aa6bccca5606d250a165
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74419663"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-rbac-and-the-azure-portal"></a>Tutorial: Grant a user access to Azure resources using RBAC and the Azure portal

[Role-based access control (RBAC)](overview.md) is the way that you manage access to Azure resources. In this tutorial, you grant a user access to create and manage virtual machines in a resource group.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Grant access for a user at a resource group scope
> * Hozzáférés eltávolítása

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

1. In the navigation list, click **Resource groups**.

1. Click **Add** to open the **Resource group** blade.

   ![Új erőforráscsoport hozzáadása](./media/quickstart-assign-role-user-portal/resource-group.png)

1. For **Resource group name**, enter **rbac-resource-group**.

1. Válasszon egy előfizetést és a egy helyet.

1. Click **Create** to create the resource group.

1. Click **Refresh** to refresh the list of resource groups.

   Az új erőforráscsoport az erőforráscsoportok listájában jelenik meg.

   ![Erőforráscsoport-lista](./media/quickstart-assign-role-user-portal/resource-group-list.png)

## <a name="grant-access"></a>Hozzáférés biztosítása

Az RBAC-ben a hozzáférés biztosítása egy szerepkör-hozzárendelés létrehozásával történik.

1. In the list of **Resource groups**, click the new **rbac-resource-group** resource group.

1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.

1. Click the **Role assignments** tab to see the current list of role assignments.

   ![Hozzáférés-vezérlés (IAM) panel erőforráscsoporthoz](./media/quickstart-assign-role-user-portal/access-control.png)

1. Click **Add** > **Add role assignment** to open the Add role assignment pane.

   If you don't have permissions to assign roles, the Add role assignment option will be disabled.

   ![Add menu](./media/role-assignments-portal/add-menu.png)

   ![Add role assignment pane](./media/quickstart-assign-role-user-portal/add-role-assignment.png)

1. A **Szerepkör** legördülő listájában válassza a **Virtuális gépek közreműködője** szerepkört.

1. A **Kiválasztás** listában válassza ki saját magát vagy egy másik felhasználót.

1. Click **Save** to create the role assignment.

   After a few moments, the user is assigned the Virtual Machine Contributor role at the rbac-resource-group resource group scope.

   ![Virtuális gépek közreműködője szerepkör hozzárendelése](./media/quickstart-assign-role-user-portal/vm-contributor-assignment.png)

## <a name="remove-access"></a>Hozzáférés eltávolítása

Az RBAC-ben hozzáférés eltávolításához egy szerepkör-hozzárendelést kell eltávolítania.

1. In the list of role assignments, add a checkmark next to the user with the Virtual Machine Contributor role.

1. Click **Remove**.

   ![Szerepkör-hozzárendelés eltávolítási üzenete](./media/quickstart-assign-role-user-portal/remove-role-assignment.png)

1. In the remove role assignment message that appears, click **Yes**.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

1. In the navigation list, click **Resource groups**.

1. Click **rbac-resource-group** to open the resource group.

1. Click **Delete resource group** to delete the resource group.

   ![Erőforráscsoport törlése](./media/quickstart-assign-role-user-portal/delete-resource-group.png)

1. On the **Are you sure you want to delete** blade, type the resource group name: **rbac-resource-group**.

1. Click **Delete** to delete the resource group.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Tutorial: Grant a user access to Azure resources using RBAC and Azure PowerShell](tutorial-role-assignments-user-powershell.md)
