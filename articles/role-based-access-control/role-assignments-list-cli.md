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
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 1a58d2170ec1107222f0e37e432063af23743e42
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74710437"
---
# <a name="list-role-assignments-using-azure-rbac-and-azure-cli"></a>Szerepkör-hozzárendelések listázása az Azure RBAC és az Azure CLI használatával

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] ez a cikk azt ismerteti, hogyan listázhatja ki a szerepkör-hozzárendeléseket az Azure CLI használatával.

## <a name="prerequisites"></a>Előfeltételek

- [Bash Azure Cloud Shell](/azure/cloud-shell/overview) vagy [Azure CLI](/cli/azure) -ben

## <a name="list-role-assignments-for-a-user"></a>Felhasználó szerepkör-hozzárendeléseinek felsorolása

Egy adott felhasználó szerepkör-hozzárendeléseinek listázásához használja az [az role hozzárendelés List](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --assignee <assignee>
```

Alapértelmezés szerint csak az előfizetésre hatókörű közvetlen hozzárendelések jelennek meg. Ha erőforrás vagy csoport szerint szeretné megtekinteni a hozzárendeléseket, használja a `--all` és az örökölt hozzárendelések megtekintéséhez használja a `--include-inherited`.

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

A felügyeleti csoport hatókörében lévő összes szerepkör-hozzárendelés felsorolásához használja [az az role-hozzárendelési lista](/cli/azure/role/assignment#az-role-assignment-list)lehetőséget. A felügyeleti csoport AZONOSÍTÓjának lekéréséhez a Azure Portal **felügyeleti csoportok** paneljén található, vagy használhatja az [az Account Management-Group listát](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list).

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
az role assignment list --scope /providers/Microsoft.Management/managementGroups/marketing-group --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="next-steps"></a>Következő lépések

- [Szerepkör-hozzárendelések hozzáadása vagy eltávolítása az Azure RBAC és az Azure CLI használatával](role-assignments-cli.md)
