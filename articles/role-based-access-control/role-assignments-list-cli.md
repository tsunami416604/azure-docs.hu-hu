---
title: Azure-beli szerepkör-hozzárendelések listázása az Azure CLI-vel – Azure RBAC
description: Megtudhatja, hogyan határozhatja meg, hogy a felhasználók, csoportok, egyszerű szolgáltatások és felügyelt identitások milyen erőforrásokhoz férnek hozzá az Azure CLI és az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/10/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: f4b635d6867c36b8b0f385320e3720bea41b54d1
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2020
ms.locfileid: "82735742"
---
# <a name="list-azure-role-assignments-using-azure-cli"></a>Azure-beli szerepkör-hozzárendelések listázása az Azure CLI használatával

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]Ez a cikk bemutatja, hogyan listázhatja ki a szerepkör-hozzárendeléseket az Azure CLI használatával.

> [!NOTE]
> Ha a szervezete egy Azure-beli [delegált erőforrás-kezelést](../lighthouse/concepts/azure-delegated-resource-management.md)használó szolgáltatónál kiszervezett felügyeleti funkciókat használ, az adott szolgáltató által meghatalmazott szerepkör-hozzárendelések nem jelennek meg.

## <a name="prerequisites"></a>Előfeltételek

- [Bash Azure Cloud Shell](/azure/cloud-shell/overview) vagy [Azure CLI](/cli/azure) -ben

## <a name="list-role-assignments-for-a-user"></a>Felhasználó szerepkör-hozzárendeléseinek felsorolása

Egy adott felhasználó szerepkör-hozzárendeléseinek listázásához használja az [az role hozzárendelés List](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli-interactive
az role assignment list --assignee <assignee>
```

Alapértelmezés szerint csak a jelenlegi előfizetéshez tartozó szerepkör-hozzárendelések jelennek meg. A jelenlegi előfizetéshez és az alatta lévő szerepkör-hozzárendelések megtekintéséhez adja hozzá a `--all` (z) paramétert. Az örökölt szerepkör-hozzárendelések megtekintéséhez adja `--include-inherited` hozzá a paramétert.

Az alábbi példa felsorolja azokat a szerepkör-hozzárendeléseket, amelyek közvetlenül *a\@patlong contoso.com* -felhasználóhoz vannak rendelve:

```azurecli-interactive
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
```

## <a name="list-role-assignments-for-a-resource-group"></a>Erőforráscsoport szerepkör-hozzárendeléseinek felsorolása

Az erőforráscsoport-hatókörben létező szerepkör-hozzárendelések listázásához használja az [az role-hozzárendelési lista](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli-interactive
az role assignment list --resource-group <resource_group>
```

A következő példa a *Pharma-Sales* erőforráscsoport szerepkör-hozzárendeléseit sorolja fel:

```azurecli-interactive
az role assignment list --resource-group pharma-sales --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}

...
```

## <a name="list-role-assignments-for-a-subscription"></a>Előfizetés szerepkör-hozzárendeléseinek felsorolása

Az előfizetési hatókör összes szerepkör-hozzárendelésének listázásához használja [az az role-hozzárendelési lista](/cli/azure/role/assignment#az-role-assignment-list)lehetőséget. Az előfizetés-azonosító lekéréséhez a Azure Portal az **előfizetések** panelén található, vagy használhatja az [az Account List](/cli/azure/account#az-account-list)lehetőséget.

```azurecli-interactive
az role assignment list --subscription <subscription_name_or_id>
```

Példa:

```azurecli-interactive
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="list-role-assignments-for-a-management-group"></a>Felügyeleti csoport szerepkör-hozzárendeléseinek listázása

A felügyeleti csoport hatókörében lévő összes szerepkör-hozzárendelés felsorolásához használja [az az role-hozzárendelési lista](/cli/azure/role/assignment#az-role-assignment-list)lehetőséget. A felügyeleti csoport AZONOSÍTÓjának lekéréséhez a Azure Portal **felügyeleti csoportok** paneljén található, vagy használhatja az [az Account Management-Group listát](/cli/azure/account/management-group#az-account-management-group-list).

```azurecli-interactive
az role assignment list --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

Példa:

```azurecli-interactive
az role assignment list --scope /providers/Microsoft.Management/managementGroups/marketing-group --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="list-role-assignments-for-a-managed-identity"></a>Felügyelt identitás szerepkör-hozzárendeléseinek listázása

1. Szerezze be a rendszer által hozzárendelt vagy felhasználó által hozzárendelt felügyelt identitás objektumazonosító-AZONOSÍTÓját.

    A felhasználó által hozzárendelt felügyelt identitás objektum-AZONOSÍTÓjának lekéréséhez az [az ad SP List](/cli/azure/ad/sp#az-ad-sp-list) vagy [az Identity List](/cli/azure/identity#az-identity-list)lehetőséget használhatja.

    ```azurecli-interactive
    az ad sp list --display-name "<name>" --query [].objectId --output tsv
    ```

    A rendszer által hozzárendelt felügyelt identitás azonosítójának lekéréséhez használhatja [az az ad SP listát](/cli/azure/ad/sp#az-ad-sp-list).

    ```azurecli-interactive
    az ad sp list --display-name "<vmname>" --query [].objectId --output tsv
    ```

1. A szerepkör-hozzárendelések listázásához használja az [az role-hozzárendelési lista](/cli/azure/role/assignment#az-role-assignment-list)lehetőséget.

    Alapértelmezés szerint csak a jelenlegi előfizetéshez tartozó szerepkör-hozzárendelések jelennek meg. A jelenlegi előfizetéshez és az alatta lévő szerepkör-hozzárendelések megtekintéséhez adja hozzá a `--all` (z) paramétert. Az örökölt szerepkör-hozzárendelések megtekintéséhez adja `--include-inherited` hozzá a paramétert.

    ```azurecli-interactive
    az role assignment list --assignee <objectid>
    ```

## <a name="next-steps"></a>További lépések

- [Azure-beli szerepkör-hozzárendelések hozzáadása vagy eltávolítása az Azure CLI-vel](role-assignments-cli.md)
