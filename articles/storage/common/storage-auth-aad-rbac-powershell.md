---
title: Az Azure AD hozzáférési jogosultsága ahhoz, hogy az RBAC - Azure Storage blob és üzenetsor adatok kezelése az Azure PowerShell használatával
description: Azure PowerShell használatával hozzáférés hozzárendelése a tárolókhoz és a szerepköralapú hozzáférés-vezérlés (RBAC) az üzenetsorokat. Az Azure Storage támogatja a beépített és egyéni RBAC-szerepkörök az Azure AD-n keresztül hitelesítést.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: f14c6625a36356a6882e1596db13c1749a9a292a
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58449964"
---
# <a name="grant-access-to-azure-blob-and-queue-data-with-rbac-using-powershell"></a>Az RBAC a PowerShell-lel az Azure blob és üzenetsor adatokhoz való hozzáférés engedélyezése

Az Azure Active Directory (Azure AD) keresztül biztonságos erőforrásokhoz való hozzáférési jogosultságok engedélyezi [szerepköralapú hozzáférés-vezérlés (RBAC)](../../role-based-access-control/overview.md). Az Azure Storage határozza meg, amely magában foglalja a tárolók és -várólisták eléréséhez használt gyakori jogosultságkészletek beépített RBAC-szerepkör. 

Az RBAC szerepkör van rendelve egy Azure AD rendszerbiztonsági tag, amikor az Azure biztosít hozzáférést az ezeket az erőforrásokat, hogy a rendszerbiztonsági tag esetében. Hozzáférés az előfizetéshez, az erőforráscsoport, a storage-fiókban vagy egy tároló vagy üzenetsor szintjét hatóköre. Az Azure AD rendszerbiztonsági tag lehet, hogy egy felhasználó, csoport, egy alkalmazás egyszerű szolgáltatást, vagy egy [-identitás az Azure-erőforrások](../../active-directory/managed-identities-azure-resources/overview.md).

Ez a cikk ismerteti az Azure PowerShell használatával a beépített RBAC-szerepkörök listája, és hozzárendelheti azokat a felhasználók számára. Azure PowerShell használatával kapcsolatos további információkért lásd: [áttekintése az Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="rbac-roles-for-blobs-and-queues"></a>A blobok és üzenetsorok RBAC-szerepkörök

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Erőforrás-hatókör meghatározása 

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-rbac-roles"></a>Lista elérhető RBAC-szerepkörök

Elérhető beépített RBAC-szerepkörök az Azure PowerShell-lel listájában, használja a [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) parancsot:

```powershell
Get-AzRoleDefinition | FT Name, Description
```

Ekkor megjelenik a beépített Azure Storage együtt jelenik meg, más beépített szerepkörök az Azure data szerepkörök:

```Example
Storage Blob Data Contributor             Allows for read, write and delete access to Azure Storage blob containers and data
Storage Blob Data Owner                   Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.
Storage Blob Data Reader                  Allows for read access to Azure Storage blob containers and data
Storage Queue Data Contributor            Allows for read, write, and delete access to Azure Storage queues and queue messages
Storage Queue Data Message Processor      Allows for peek, receive, and delete access to Azure Storage queue messages
Storage Queue Data Message Sender         Allows for sending of Azure Storage queue messages
Storage Queue Data Reader                 Allows for read access to Azure Storage queues and queue messages
```

## <a name="assign-an-rbac-role-to-a-user"></a>Az RBAC szerepkör hozzárendelése felhasználóhoz

Az RBAC szerepkör hozzárendelése felhasználóhoz, használja a [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) parancsot. A parancs formátuma a hozzárendelés hatókörét is különböznek egymástól a rendszer. Az alábbi példák bemutatják, hogyan szerepkör hozzárendelése a felhasználók a különböző hatókörök.

### <a name="container-scope"></a>Tároló hatókör

Egy tároló hatóköre szerepkör hozzárendelése tárolója hatókörének tartalmazó karakterláncot kell megadni a `--scope` paraméter. A hatókör egy tárolóhoz van a képernyőn:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
```

Az alábbi példa a **Storage-Blobadatok Közreműködője** szerepkört egy felhasználóhoz kötődő nevű tárolóba *minta-tároló*. Ügyeljen arra, hogy mintaértékeket és zárójelben a helyőrző értékeket cserélje le a saját értékeit: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/sample-container"
```

### <a name="queue-scope"></a>Csoportjuk hatókörébe tartozó

Üzenetsor hatókörű szerepkör hozzárendelése a hatókör várólistájának tartalmazó karakterláncot kell megadni a `--scope` paraméter. A hatókör üzenetsorhoz van a képernyőn:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue-name>
```

Az alábbi példa a **tárolási Üzenetsorbeli adatok Közreműködője** szerepkört egy felhasználóhoz kötődő nevű üzenetsor *várólista-minta*. Ügyeljen arra, hogy mintaértékeket és zárójelben a helyőrző értékeket cserélje le a saját értékeit: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/sample-queue"
```

### <a name="storage-account-scope"></a>Storage-fiók hatókörében

Hatóköre a tárfiók szerepkör hozzárendelése, adja meg a tárfiók típusú erőforrást a hatóköre a `--scope` paraméter. A storage-fiókok hatóköre a képernyőn:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

A következő példa bemutatja hogyan hatókör a **Storage-Blobadatok olvasója** a tárfiók szintjén egy felhasználói szerepkört. Ügyeljen arra, hogy a minta értékeket cserélje le a saját értékeit: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>Erőforrás-csoport hatóköre

Az erőforráscsoport hatóköre szerepkörök hozzárendeléséhez, adja meg az erőforráscsoport neve vagy azonosítója a `--resource-group` paraméter. Az alábbi példa a **tárolási Üzenetsorbeli adatok olvasója** szerepkört egy felhasználóhoz, az erőforráscsoport szintjén. Ügyeljen arra, hogy a mintaértékeket és zárójelben helyőrzőértékeket cserélje le a saját értékeit: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Reader" `
    -ResourceGroupName "sample-resource-group"
```

### <a name="subscription-scope"></a>Egy előfizetésre

Az előfizetés hatóköre szerepkörök hozzárendeléséhez, adja meg azt az a előfizetés esetében a `--scope` paraméter. A hatókör-előfizetéssel van a képernyőn:

```
/subscriptions/<subscription>
```

Az alábbi példa bemutatja, hogyan rendelje hozzá a **Storage-Blobadatok olvasója** a tárfiók szintjén egy felhasználói szerepkört. Ügyeljen arra, hogy a minta értékeket cserélje le a saját értékeit: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>További lépések

- [Rbac-RÓL és az Azure PowerShell használatával Azure-erőforrásokhoz való hozzáférés kezelése](../../role-based-access-control/role-assignments-powershell.md)
- [Az RBAC Azure CLI-vel az Azure blob és üzenetsor adatokhoz való hozzáférés engedélyezése](storage-auth-aad-rbac-cli.md)
- [Hozzáférés engedélyezése az Azure blob és üzenetsor adatok RBAC használata az Azure Portalon](storage-auth-aad-rbac-portal.md)
