---
title: Felügyelt identitás-hozzáférés kiosztása egy erőforráshoz a PowerShell használatával – Azure AD
description: Részletes útmutató felügyelt identitások egy erőforráshoz való hozzárendeléséhez, egy másik erőforráshoz való hozzáféréshez a PowerShell használatával.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2018
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ae2da130f61d31db4904ed2dd5ac18444929950
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/31/2020
ms.locfileid: "89177499"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-powershell"></a>Felügyelt identitás-hozzáférés kiosztása egy erőforráshoz a PowerShell használatával

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Miután konfigurált egy Azure-erőforrást egy felügyelt identitással, megadhatja a felügyelt identitás hozzáférését egy másik erőforráshoz, ugyanúgy, mint a rendszerbiztonsági tag. Ebből a példából megtudhatja, hogyan biztosíthatja az Azure-beli virtuális gépek felügyelt identitását egy Azure Storage-fiókhoz a PowerShell használatával.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitásait, tekintse meg az [Áttekintés szakaszt](overview.md). **Mindenképpen tekintse át a [rendszer által hozzárendelt és a felhasználó által hozzárendelt felügyelt identitás közötti különbséget](overview.md#managed-identity-types)**.
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- Ha még nem tette meg [, telepítse a Azure PowerShell legújabb verzióját](/powershell/azure/install-az-ps) .

## <a name="use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Felügyelt identitás-hozzáférés kiosztása az Azure RBAC használatával egy másik erőforráshoz

Miután engedélyezte a felügyelt identitást egy Azure-erőforráson, [például egy Azure-beli virtuális gépen](qs-configure-powershell-windows-vm.md):

1. Jelentkezzen be az Azure-ba a `Connect-AzAccount` parancsmag használatával. Olyan fiókot használjon, amely ahhoz az Azure-előfizetéshez van társítva, amelyhez a felügyelt identitást konfigurálta:

   ```powershell
   Connect-AzAccount
   ```
2. Ebben a példában egy Azure-beli virtuális gép hozzáférést biztosítunk egy Storage-fiókhoz. Először a [Get-AzVM](/powershell/module/az.compute/get-azvm) használatával szerezheti be a nevű virtuális gép egyszerű szolgáltatását `myVM` , amely akkor jött létre, amikor engedélyezte a felügyelt identitást. Ezután a [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) használatával adja meg a virtuális gép **olvasójának** a következő nevű Storage-fiókhoz való hozzáférést `myStorageAcct` :

    ```powershell
    $spID = (Get-AzVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="next-steps"></a>További lépések

- [Felügyelt identitás az Azure-erőforrásokhoz – áttekintés](overview.md)
- A felügyelt identitás Azure-beli virtuális gépen való engedélyezésével kapcsolatban lásd: [felügyelt identitások konfigurálása](qs-configure-powershell-windows-vm.md)Azure-beli virtuális gépeken a PowerShell használatával.
