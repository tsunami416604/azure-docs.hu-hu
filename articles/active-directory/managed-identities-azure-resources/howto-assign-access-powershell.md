---
title: Egy felügyelt identitás hozzáférés hozzárendelése egy Azure-erőforrást PowerShell használatával
description: Részletes utasításokat egy erőforráson, egy felügyelt identitás hozzárendelése egy másik erőforrás, a PowerShell használatával való hozzáférést.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff36be7f87d0dd9e5cac5ee7f788eec0cda5a9fd
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58443855"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-powershell"></a>Egy felügyelt identitás hozzáférés hozzárendelése egy erőforrást PowerShell használatával

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Miután konfigurálta az Azure-erőforrás felügyelt identitással, a felügyelt identitás hozzáférést biztosíthat más erőforráshoz, csakúgy, mint bármely rendszerbiztonsági tag. Ez a példa bemutatja, az Azure virtuális gép felügyelt identitás hozzáférés biztosítása a PowerShell-lel az Azure storage-fiókba.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

- Ha még nem ismeri a felügyelt identitások Azure-erőforrások számára, tekintse meg a [áttekintés szakaszban](overview.md). **Ne feledje el áttekinteni a [különbség a rendszer által hozzárendelt, és a felhasználó által hozzárendelt felügyelt identitás](overview.md#how-does-it-work)**.
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- Telepítés [az Azure PowerShell legújabb verzióját](/powershell/azure/install-az-ps) Ha még nem tette.

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Az RBAC használatával hozzáférést egy felügyelt identitás hozzárendelése egy másik erőforrás

Miután engedélyezte az Azure-erőforrás, felügyelt identitás [például egy Azure virtuális gép](qs-configure-powershell-windows-vm.md):

1. Jelentkezzen be Azure-ban a `Connect-AzAccount` parancsmagot. Egy fiókot, amely alatt a felügyelt identitás konfigurálása Azure-előfizetéshez társított használja:

   ```powershell
   Connect-AzAccount
   ```
2. Ebben a példában azt egy Azure-beli Virtuálisgép-hozzáférés, hogy küldjön egy storage-fiókot. Először használjuk [Get-azvm parancsmag](/powershell/module/az.compute/get-azvm) beolvasni az egyszerű szolgáltatás nevű virtuális gép `myVM`, amely jött létre, amikor lehetővé tettük felügyelt identitás. Ezután használja [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) biztosíthat a virtuális gép **olvasó** nevű tárfiókot hozzáférést `myStorageAcct`:

    ```powershell
    $spID = (Get-AzVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="next-steps"></a>További lépések

- [Felügyelt identitások Azure-erőforrások – áttekintés](overview.md)
- Engedélyezheti a felügyelt identitás-beli virtuális gépen [konfigurálása felügyelt identitások az Azure-erőforrások PowerShell-lel, egy Azure virtuális Gépen található](qs-configure-powershell-windows-vm.md).
