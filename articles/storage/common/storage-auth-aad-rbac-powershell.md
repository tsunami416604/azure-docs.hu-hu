---
title: RBAC-szerepkör hozzárendelése a PowerShell használatával az adateléréshez
titleSuffix: Azure Storage
description: Megtudhatja, hogy miként rendelhet engedélyeket az Azure Active Directory rendszerbiztonsági taghoz szerepköralapú hozzáférés-vezérléssel (RBAC) a PowerShell használatával. Az Azure Storage támogatja a beépített és egyéni RBAC szerepkörök et az Azure AD-n keresztüli hitelesítéshez.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 1413035c879198cf333aeeb5d8fe993162939172
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75460584"
---
# <a name="use-powershell-to-assign-an-rbac-role-for-access-to-blob-and-queue-data"></a>A PowerShell használatával RBAC-szerepkört rendelhet a blob- és várólista-adatokhoz való hozzáféréshez

Az Azure Active Directory (Azure AD) [szerepköralapú hozzáférés-vezérlés (RBAC)](../../role-based-access-control/overview.md)révén engedélyezi a védett erőforrásokhoz való hozzáférési jogokat. Az Azure Storage a tárolók vagy várólisták eléréséhez használt közös engedélyeket tartalmazó beépített RBAC-szerepkörök készletét határozza meg.

Ha egy RBAC-szerepkör egy Azure AD rendszerbiztonsági taghoz van rendelve, az Azure hozzáférést biztosít ezekhez az erőforrásokhoz az adott rendszerbiztonsági tag számára. Az access az előfizetés, az erőforráscsoport, a tárfiók vagy egy adott tároló vagy várólista szintjére ható. Az Azure AD rendszerbiztonsági tag lehet egy felhasználó, egy csoport, egy egyszerű alkalmazás vagy egy [felügyelt identitás az Azure-erőforrások.](../../active-directory/managed-identities-azure-resources/overview.md)

Ez a cikk ismerteti, hogyan azure PowerShell használatával a beépített RBAC-szerepkörök listázásához, és hozzárendeli őket a felhasználókhoz. Az Azure PowerShell használatáról az [Azure PowerShell áttekintése című témakörben olvashat bővebben.](https://docs.microsoft.com/powershell/azure/overview)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="rbac-roles-for-blobs-and-queues"></a>RBAC-szerepkörök blobokhoz és várólistákhoz

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Erőforráshatókör meghatározása

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-rbac-roles"></a>Elérhető RBAC-szerepkörök listája

Az Azure PowerShell beépített RBAC-szerepkörök listájának listázásához használja a [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) parancsot:

```powershell
Get-AzRoleDefinition | FT Name, Description
```

A beépített Azure Storage-adatszerepkörök et az Azure más beépített szerepköreivel együtt láthatja:

```Example
Storage Blob Data Contributor             Allows for read, write and delete access to Azure Storage blob containers and data
Storage Blob Data Owner                   Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.
Storage Blob Data Reader                  Allows for read access to Azure Storage blob containers and data
Storage Queue Data Contributor            Allows for read, write, and delete access to Azure Storage queues and queue messages
Storage Queue Data Message Processor      Allows for peek, receive, and delete access to Azure Storage queue messages
Storage Queue Data Message Sender         Allows for sending of Azure Storage queue messages
Storage Queue Data Reader                 Allows for read access to Azure Storage queues and queue messages
```

## <a name="assign-an-rbac-role-to-a-security-principal"></a>RBAC szerepkör hozzárendelése rendszerbiztonsági taghoz

Ha RBAC szerepkört szeretne hozzárendelni egy rendszerbiztonsági taghoz, használja a [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) parancsot. A parancs formátuma a hozzárendelés hatóköre alapján eltérő lehet. A parancs futtatásához tulajdonosi vagy közreműködői szerepkört kell hozzárendelnie a megfelelő hatókörhöz. Az alábbi példák bemutatják, hogyan rendelhet egy szerepkört egy felhasználóhoz különböző hatókörökben, de ugyanazzal a paranccsal szerepkört rendelhet bármely rendszerbiztonsági taghoz.

### <a name="container-scope"></a>Tároló hatóköre

Ha egy tárolóhoz rendelt szerepkört szeretne hozzárendelni, adjon meg `--scope` egy karakterláncot, amely a tároló hatókörét tartalmazza a paraméterhez. A tároló hatóköre a következő formában jelenik meg:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
```

A következő példa hozzárendeli a **Storage Blob Data Contributor** szerepkört egy felhasználóhoz, hatóköre egy mintatároló nevű *tárolóhoz.* Ügyeljen arra, hogy a szögletes zárójelben lévő mintaértékeket és helyőrző értékeket a saját értékeire cserélje le: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/sample-container"
```

### <a name="queue-scope"></a>Várólista hatóköre

Ha egy várólistához hatókörrel tartozó szerepkört szeretne hozzárendelni, adjon `--scope` meg egy karakterláncot, amely a paraméter várólistájának hatókörét tartalmazza. A várólista hatóköre a következő formában található:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue-name>
```

A következő példa hozzárendeli a **storage-várólista-adatközreműködói** szerepkört egy felhasználóhoz, amely egy *mintavárólista*nevű várólistához van kódolva. Ügyeljen arra, hogy a szögletes zárójelben lévő mintaértékeket és helyőrző értékeket a saját értékeire cserélje le: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/sample-queue"
```

### <a name="storage-account-scope"></a>Tárfiók hatóköre

Ha egy szerepkör hatókörét a tárfiókhoz szeretné rendelni, adja `--scope` meg a storage-fiók erőforrásának hatókörét a paraméterhez. A tárfiók hatóköre a következő formában jelenik meg:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

A következő példa bemutatja, hogyan kell a **Storage Blob Data Reader** szerepkör hatókörét egy felhasználó a tárfiók szintjén. Ügyeljen arra, hogy a mintaértékeket a saját értékeire cserélje le: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>Erőforráscsoport hatóköre

Ha hatókörrel ellátott szerepkört szeretne hozzárendelni az erőforráscsoporthoz, `--resource-group` adja meg a paraméter erőforráscsoportnevét vagy azonosítóját. A következő példa a **tárolási várólista-adatolvasó** szerepkört az erőforráscsoport szintjén rendeli hozzá egy felhasználóhoz. Ügyeljen arra, hogy a mintaértékeket és a helyőrző értékeket zárójelben cserélje le a saját értékeire: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Reader" `
    -ResourceGroupName "sample-resource-group"
```

### <a name="subscription-scope"></a>Előfizetés hatóköre

Ha az előfizetéshez hatókörrel tartozó szerepkört szeretne hozzárendelni, adja meg a `--scope` paraméter előfizetési hatókörét. Az előfizetés hatóköre a következő formában érhető el:

```
/subscriptions/<subscription>
```

A következő példa bemutatja, hogyan rendelheti hozzá a **Storage Blob Data Reader** szerepkört egy felhasználóhoz a tárfiók szintjén. Ügyeljen arra, hogy a mintaértékeket a saját értékeire cserélje le: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>További lépések

- [Azure-erőforrásokhoz való hozzáférés kezelése az RBAC és az Azure PowerShell használatával](../../role-based-access-control/role-assignments-powershell.md)
- [Hozzáférés biztosítása Azure-blobok és -üzenetsorok adataihoz RBAC használatával az Azure CLI-vel](storage-auth-aad-rbac-cli.md)
- [Hozzáférés biztosítása Azure-blobok és -üzenetsorok adataihoz RBAC használatával az Azure Portalon](storage-auth-aad-rbac-portal.md)
