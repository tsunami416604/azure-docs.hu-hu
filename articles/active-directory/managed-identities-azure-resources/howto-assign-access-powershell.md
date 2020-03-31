---
title: Felügyelt identitás-hozzáférés hozzárendelése egy erőforráshoz a PowerShell használatával – Azure AD
description: Lépésenkénti utasítások egy felügyelt identitás hozzárendeléséhez egy erőforráshoz, egy másik erőforráshoz való hozzáféréshez a PowerShell használatával.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74547246"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-powershell"></a>Felügyelt identitás-hozzáférés hozzárendelése egy erőforráshoz a PowerShell használatával

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Miután konfigurált egy Azure-erőforrást felügyelt identitással, a felügyelt identitás hozzáférést adhat egy másik erőforráshoz, ugyanúgy, mint bármely rendszerbiztonsági tag. Ebben a példában bemutatja, hogyan adhat hozzáférést egy Azure-beli virtuális gép felügyelt identitásának egy Azure-tárfiókhoz a PowerShell használatával.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitásait, tekintse meg az [áttekintő szakaszt.](overview.md) **Mindenképpen tekintse át a [rendszerhez rendelt és a felhasználó által hozzárendelt felügyelt identitás közötti különbséget.](overview.md#how-does-the-managed-identities-for-azure-resources-work)**
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- Telepítse [az Azure PowerShell legújabb verzióját,](/powershell/azure/install-az-ps) ha még nem tette meg.

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Felügyelt identitás-hozzáférés hozzárendelése az RBAC segítségével egy másik erőforráshoz

Miután engedélyezte a felügyelt identitást egy Azure-erőforrásban, [például egy Azure-beli virtuális gépen:](qs-configure-powershell-windows-vm.md)

1. Jelentkezzen be az `Connect-AzAccount` Azure-ba a parancsmag használatával. Használjon olyan fiókot, amely az Azure-előfizetéshez van társítva, amely nek megfelelően konfigurálta a felügyelt identitást:

   ```powershell
   Connect-AzAccount
   ```
2. Ebben a példában egy Azure virtuális gép hozzáférést biztosítunk egy tárfiókhoz. Először a [Get-AzVM](/powershell/module/az.compute/get-azvm) használatával kapjuk meg `myVM`a virtuális gép nevesített szolgáltatását, amely akkor jött létre, amikor engedélyeztük az identitás kezelését. Ezután a [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) használatával hozzáférést adhat a virtuális `myStorageAcct` **gépolvasónak** egy tárfiókhoz:

    ```powershell
    $spID = (Get-AzVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="next-steps"></a>További lépések

- [Felügyelt identitás az Azure-erőforrásokhoz – áttekintés](overview.md)
- Felügyelt identitás engedélyezése egy Azure virtuális gép, [az Azure-erőforrások felügyelt identitások konfigurálása egy Azure-beli virtuális gép en PowerShell használatával.](qs-configure-powershell-windows-vm.md)
