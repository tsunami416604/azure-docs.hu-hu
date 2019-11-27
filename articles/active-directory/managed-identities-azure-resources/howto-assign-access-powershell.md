---
title: Felügyelt identitás-hozzáférés kiosztása egy erőforráshoz a PowerShell használatával – Azure AD
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
ms.openlocfilehash: a2283ac076ef761fd098d75e7120e6557a959574
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74547246"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-powershell"></a>Egy felügyelt identitás hozzáférés hozzárendelése egy erőforrást PowerShell használatával

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Miután konfigurálta az Azure-erőforrás felügyelt identitással, a felügyelt identitás hozzáférést biztosíthat más erőforráshoz, csakúgy, mint bármely rendszerbiztonsági tag. Ez a példa bemutatja, az Azure virtuális gép felügyelt identitás hozzáférés biztosítása a PowerShell-lel az Azure storage-fiókba.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitásait, tekintse meg az [Áttekintés szakaszt](overview.md). **Mindenképpen tekintse át a [rendszer által hozzárendelt és a felhasználó által hozzárendelt felügyelt identitás közötti különbséget](overview.md#how-does-the-managed-identities-for-azure-resources-work)** .
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- Ha még nem tette meg [, telepítse a Azure PowerShell legújabb verzióját](/powershell/azure/install-az-ps) .

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Az RBAC használatával hozzáférést egy felügyelt identitás hozzárendelése egy másik erőforrás

Miután engedélyezte a felügyelt identitást egy Azure-erőforráson, [például egy Azure-beli virtuális gépen](qs-configure-powershell-windows-vm.md):

1. Jelentkezzen be az Azure-ba az `Connect-AzAccount`-parancsmag használatával. Egy fiókot, amely alatt a felügyelt identitás konfigurálása Azure-előfizetéshez társított használja:

   ```powershell
   Connect-AzAccount
   ```
2. Ebben a példában azt egy Azure-beli Virtuálisgép-hozzáférés, hogy küldjön egy storage-fiókot. Először a [Get-AzVM](/powershell/module/az.compute/get-azvm) használatával szerezheti be a `myVM`nevű virtuális gép egyszerű szolgáltatását, amely akkor jött létre, amikor engedélyezte a felügyelt identitást. Ezután a [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) használatával adja meg a virtuális gép **olvasójának** a `myStorageAcct`nevű Storage-fiókhoz való hozzáférést:

    ```powershell
    $spID = (Get-AzVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="next-steps"></a>Következő lépések

- [Felügyelt identitás az Azure-erőforrásokhoz – áttekintés](overview.md)
- A felügyelt identitás Azure-beli virtuális gépen való engedélyezésével kapcsolatban lásd: [felügyelt identitások konfigurálása](qs-configure-powershell-windows-vm.md)Azure-beli virtuális gépeken a PowerShell használatával.
