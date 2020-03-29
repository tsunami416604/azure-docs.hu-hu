---
title: RBAC szerepkör hozzárendelése az Azure CLI használatával az adateléréshez
titleSuffix: Azure Storage
description: Megtudhatja, hogy miként rendelhet engedélyeket az Azure Active Directory rendszerbiztonsági taghoz szerepköralapú hozzáférés-vezérléssel (RBAC) az Azure CLI használatával. Az Azure Storage támogatja a beépített és egyéni RBAC szerepkörök et az Azure AD-n keresztüli hitelesítéshez.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: abe35f3193e2d7ff9a949ca7cd330cb58da2b78c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74891968"
---
# <a name="use-azure-cli-to-assign-an-rbac-role-for-access-to-blob-and-queue-data"></a>Az Azure CLI használatával RBAC szerepkörhozzárendelése blob- és várólista-adatokhoz való hozzáféréshez

Az Azure Active Directory (Azure AD) [szerepköralapú hozzáférés-vezérlés (RBAC)](../../role-based-access-control/overview.md)révén engedélyezi a védett erőforrásokhoz való hozzáférési jogokat. Az Azure Storage a blob- vagy várólista-adatok eléréséhez használt közös engedélyeket tartalmazó beépített RBAC-szerepkörök készletét határozza meg.

Ha egy RBAC-szerepkör egy Azure AD rendszerbiztonsági taghoz van rendelve, az Azure hozzáférést biztosít ezekhez az erőforrásokhoz az adott rendszerbiztonsági tag számára. Az access az előfizetés, az erőforráscsoport, a tárfiók vagy egy adott tároló vagy várólista szintjére ható. Az Azure AD rendszerbiztonsági tag lehet egy felhasználó, egy csoport, egy egyszerű alkalmazás vagy egy [felügyelt identitás az Azure-erőforrások.](../../active-directory/managed-identities-azure-resources/overview.md)

Ez a cikk ismerteti, hogyan azure CLI a beépített RBAC-szerepkörök listázásához, és hozzárendeli őket a felhasználókhoz. Az Azure CLI használatáról az [Azure parancssori felülete (CLI)](https://docs.microsoft.com/cli/azure)című témakörben talál további információt.

## <a name="rbac-roles-for-blobs-and-queues"></a>RBAC-szerepkörök blobokhoz és várólistákhoz

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Erőforráshatókör meghatározása

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-rbac-roles"></a>Elérhető RBAC-szerepkörök listája

Az Azure CLI-vel elérhető beépített RBAC-szerepkörök listázásához használja az [az szerepkördefiníciós lista](/cli/azure/role/definition#az-role-definition-list) parancsot:

```azurecli-interactive
az role definition list --out table
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

RBAC szerepkör biztonsági taghoz rendeléséhez használja az [az szerepkör-hozzárendelés létrehozási](/cli/azure/role/assignment#az-role-assignment-create) parancsát. A parancs formátuma a hozzárendelés hatóköre alapján eltérő lehet. Az alábbi példák bemutatják, hogyan rendelhet egy szerepkört egy felhasználóhoz különböző hatókörökben, de ugyanazzal a paranccsal szerepkört rendelhet bármely rendszerbiztonsági taghoz.

### <a name="container-scope"></a>Tároló hatóköre

Ha egy tárolóhoz rendelt szerepkört szeretne hozzárendelni, adjon meg `--scope` egy karakterláncot, amely a tároló hatókörét tartalmazza a paraméterhez. A tároló hatóköre a következő formában jelenik meg:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container>
```

A következő példa hozzárendeli a **Storage Blob Data Contributor** szerepkört egy felhasználóhoz, hatóköre a tároló szintjén. Ügyeljen arra, hogy a szögletes zárójelben lévő mintaértékeket és helyőrző értékeket a saját értékeire cserélje le:

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container>"
```

### <a name="queue-scope"></a>Várólista hatóköre

Ha egy várólistához hatókörrel tartozó szerepkört szeretne hozzárendelni, adjon `--scope` meg egy karakterláncot, amely a paraméter várólistájának hatókörét tartalmazza. A várólista hatóköre a következő formában található:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue>
```

A következő példa hozzárendeli a **storage-várólista-adatközreműködői** szerepkört egy felhasználóhoz, amely a várólista szintjéhez van berendelve. Ügyeljen arra, hogy a szögletes zárójelben lévő mintaértékeket és helyőrző értékeket a saját értékeire cserélje le:

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue>"
```

### <a name="storage-account-scope"></a>Tárfiók hatóköre

Ha egy szerepkör hatókörét a tárfiókhoz szeretné rendelni, adja `--scope` meg a storage-fiók erőforrásának hatókörét a paraméterhez. A tárfiók hatóköre a következő formában jelenik meg:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

A következő példa bemutatja, hogyan rendelheti hozzá a **Storage Blob Data Reader** szerepkört egy felhasználóhoz a tárfiók szintjén. Győződjön meg arról, hogy a mintaértékeket a saját értékeire cseréli: \

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>Erőforráscsoport hatóköre

Ha hatókörrel ellátott szerepkört szeretne hozzárendelni az erőforráscsoporthoz, `--resource-group` adja meg a paraméter erőforráscsoportnevét vagy azonosítóját. A következő példa a **tárolási várólista-adatolvasó** szerepkört az erőforráscsoport szintjén rendeli hozzá egy felhasználóhoz. Ügyeljen arra, hogy a mintaértékeket és a helyőrző értékeket zárójelben cserélje le a saját értékeire:

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Reader" \
    --assignee <email> \
    --resource-group <resource-group>
```

### <a name="subscription-scope"></a>Előfizetés hatóköre

Ha az előfizetéshez hatókörrel tartozó szerepkört szeretne hozzárendelni, adja meg a `--scope` paraméter előfizetési hatókörét. Az előfizetés hatóköre a következő formában érhető el:

```
/subscriptions/<subscription>
```

A következő példa bemutatja, hogyan rendelheti hozzá a **Storage Blob Data Reader** szerepkört egy felhasználóhoz a tárfiók szintjén. Ügyeljen arra, hogy a mintaértékeket a saját értékeire cserélje le: 

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>További lépések

- [Azure-erőforrásokhoz való hozzáférés kezelése az RBAC és az Azure PowerShell használatával](../../role-based-access-control/role-assignments-powershell.md)
- [Hozzáférés az Azure blob- és várólista-adatokhoz az RBAC használatával az Azure PowerShell használatával](storage-auth-aad-rbac-powershell.md)
- [Hozzáférés biztosítása Azure-blobok és -üzenetsorok adataihoz RBAC használatával az Azure Portalon](storage-auth-aad-rbac-portal.md)
