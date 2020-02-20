---
title: Szerepkör-hozzárendelések listázása az Azure RBAC és az Azure CLI használatával
description: Megtudhatja, hogyan határozhatja meg, hogy a felhasználók, csoportok, egyszerű szolgáltatások és felügyelt identitások milyen erőforrásokhoz férnek hozzá az Azure szerepköralapú hozzáférés-vezérlés (RBAC) és az Azure CLI használatával.
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
ms.openlocfilehash: b02ec00544ef11ca1048fd6d3bd9bdf3fccd8c8c
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471414"
---
# <a name="list-role-assignments-using-azure-rbac-and-azure-cli"></a>Szerepkör-hozzárendelések listázása az Azure RBAC és az Azure CLI használatával

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] ez a cikk azt ismerteti, hogyan listázhatja ki a szerepkör-hozzárendeléseket az Azure CLI használatával.

> [!NOTE]
> Ha a szervezete egy Azure-beli [delegált erőforrás-kezelést](../lighthouse/concepts/azure-delegated-resource-management.md)használó szolgáltatónál kiszervezett felügyeleti funkciókat használ, az adott szolgáltató által meghatalmazott szerepkör-hozzárendelések nem jelennek meg.

## <a name="prerequisites"></a>Előfeltételek

- [Bash Azure Cloud Shell](/azure/cloud-shell/overview) vagy [Azure CLI](/cli/azure) -ben

## <a name="list-role-assignments-for-a-user"></a>Felhasználó szerepkör-hozzárendeléseinek felsorolása

Egy adott felhasználó szerepkör-hozzárendeléseinek listázásához használja az [az role hozzárendelés List](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --assignee <assignee>
```

Alapértelmezés szerint csak a jelenlegi előfizetéshez tartozó szerepkör-hozzárendelések jelennek meg. Ha meg szeretné tekinteni a jelenlegi előfizetéshez tartozó szerepkör-hozzárendeléseket, adja hozzá a `--all` paramétert. Az örökölt szerepkör-hozzárendelések megtekintéséhez adja hozzá a `--include-inherited` paramétert.

Az alábbi példa felsorolja azokat a szerepkör-hozzárendeléseket, amelyek közvetlenül a *patlong\@contoso.com* -felhasználóhoz vannak rendelve:

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
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

```azurecli
az role assignment list --resource-group <resource_group>
```

A következő példa a *Pharma-Sales* erőforráscsoport szerepkör-hozzárendeléseit sorolja fel:

```azurecli
az role assignment list --resource-group pharma-sales --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
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

```azurecli
az role assignment list --subscription <subscription_name_or_id>
```

```Example
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="list-role-assignments-for-a-management-group"></a>Felügyeleti csoport szerepkör-hozzárendeléseinek listázása

A felügyeleti csoport hatókörében lévő összes szerepkör-hozzárendelés felsorolásához használja [az az role-hozzárendelési lista](/cli/azure/role/assignment#az-role-assignment-list)lehetőséget. A felügyeleti csoport AZONOSÍTÓjának lekéréséhez a Azure Portal **felügyeleti csoportok** paneljén található, vagy használhatja az [az Account Management-Group listát](/cli/azure/account/management-group#az-account-management-group-list).

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
az role assignment list --scope /providers/Microsoft.Management/managementGroups/marketing-group --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="list-role-assignments-for-a-managed-identity"></a>Felügyelt identitás szerepkör-hozzárendeléseinek listázása

1. Szerezze be a rendszer által hozzárendelt vagy felhasználó által hozzárendelt felügyelt identitás objektum-AZONOSÍTÓját. 

    A felhasználó által hozzárendelt felügyelt identitás objektum-AZONOSÍTÓjának lekéréséhez az [az ad SP List](/cli/azure/ad/sp#az-ad-sp-list) vagy [az Identity List](/cli/azure/identity#az-identity-list)lehetőséget használhatja.

    ```azurecli
    az ad sp list --display-name "<name>" --query [].objectId --output tsv
    ```

    A rendszer által hozzárendelt felügyelt identitás azonosítójának lekéréséhez használhatja [az az ad SP listát](/cli/azure/ad/sp#az-ad-sp-list).

    ```azurecli
    az ad sp list --display-name "<vmname>" --query [].objectId --output tsv
    ```

1. A szerepkör-hozzárendelések listázásához használja az [az role-hozzárendelési lista](/cli/azure/role/assignment#az-role-assignment-list)lehetőséget.

    Alapértelmezés szerint csak a jelenlegi előfizetéshez tartozó szerepkör-hozzárendelések jelennek meg. Ha meg szeretné tekinteni a jelenlegi előfizetéshez tartozó szerepkör-hozzárendeléseket, adja hozzá a `--all` paramétert. Az örökölt szerepkör-hozzárendelések megtekintéséhez adja hozzá a `--include-inherited` paramétert.

    ```azurecli
    az role assignment list --assignee <objectid>
    ```

## <a name="next-steps"></a>Következő lépések

- [Szerepkör-hozzárendelések hozzáadása vagy eltávolítása az Azure RBAC és az Azure CLI használatával](role-assignments-cli.md)
