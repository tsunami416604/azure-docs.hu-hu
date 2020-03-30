---
title: Szerepkör-hozzárendelések listázása az Azure RBAC és az Azure CLI használatával
description: Megtudhatja, hogyan állapíthatja meg, hogy a felhasználók, csoportok, egyszerű szolgáltatás- és felügyelt identitások milyen erőforrásokhoz férhetnek hozzá az Azure szerepköralapú hozzáférés-vezérlés (RBAC) és az Azure CLI használatával.
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
ms.openlocfilehash: 5716e7bb89d017866bd1575256e2d119bb7acbe5
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385061"
---
# <a name="list-role-assignments-using-azure-rbac-and-azure-cli"></a>Szerepkör-hozzárendelések listázása az Azure RBAC és az Azure CLI használatával

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]Ez a cikk ismerteti, hogyan listázhatok szerepkör-hozzárendelések az Azure CLI használatával.

> [!NOTE]
> Ha a szervezet kiszervezte a felügyeleti funkciókat egy olyan szolgáltatóhoz, amely [az Azure delegált erőforrás-kezelését](../lighthouse/concepts/azure-delegated-resource-management.md)használja, az adott szolgáltató által engedélyezett szerepkör-hozzárendelések itt nem jelennek meg.

## <a name="prerequisites"></a>Előfeltételek

- [Bash az Azure Cloud Shell](/azure/cloud-shell/overview) ben vagy [az Azure CLI-ben](/cli/azure)

## <a name="list-role-assignments-for-a-user"></a>Felhasználó szerepkör-hozzárendeléseinek felsorolása

Egy adott felhasználó szerepkör-hozzárendeléseinek listázásához használja [az szerepkör-hozzárendelési listát:](/cli/azure/role/assignment#az-role-assignment-list)

```azurecli-interactive
az role assignment list --assignee <assignee>
```

Alapértelmezés szerint csak az aktuális előfizetés szerepkör-hozzárendelései jelennek meg. Az aktuális előfizetés szerepkör-hozzárendeléseinek megtekintéséhez `--all` adja hozzá a paramétert. Az örökölt szerepkör-hozzárendelések megtekintéséhez adja hozzá a `--include-inherited` paramétert.

A következő példa a közvetlenül a *patlong\@contoso.com* felhasználóhoz rendelt szerepkör-hozzárendeléseket sorolja fel:

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

Az erőforráscsoport hatókörén élésű szerepkör-hozzárendelések listázásához használja [az az szerepkör-hozzárendelési listát:](/cli/azure/role/assignment#az-role-assignment-list)

```azurecli-interactive
az role assignment list --resource-group <resource_group>
```

A következő példa a *pharma-sales* erőforráscsoport szerepkör-hozzárendeléseit sorolja fel:

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

Ha az összes szerepkör-hozzárendelést egy előfizetési hatókörben szeretné felsorolni, használja [az az szerepkör-hozzárendelési listát.](/cli/azure/role/assignment#az-role-assignment-list) Az előfizetés-azonosító lekért, megtalálja azt az **Azure Portalon** az Előfizetések panelen, vagy [használhatja az az-fióklistát.](/cli/azure/account#az-account-list)

```azurecli-interactive
az role assignment list --subscription <subscription_name_or_id>
```

Példa:

```azurecli-interactive
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="list-role-assignments-for-a-management-group"></a>Felügyeleti csoport szerepkör-hozzárendelésének listázása

Ha egy felügyeleticsoport hatókörén szeretné felsorolni az összes szerepkör-hozzárendelést, használja [az az szerepkör-hozzárendelési listát.](/cli/azure/role/assignment#az-role-assignment-list) A felügyeleti csoport azonosítójának lekért, **megtalálhatja** azt az Azure Portalfelügyeleti csoportok panelen, vagy használhatja [az az-fiókfelügyeleti csoport listáját.](/cli/azure/account/management-group#az-account-management-group-list)

```azurecli-interactive
az role assignment list --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

Példa:

```azurecli-interactive
az role assignment list --scope /providers/Microsoft.Management/managementGroups/marketing-group --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="list-role-assignments-for-a-managed-identity"></a>Felügyelt identitás szerepkör-hozzárendelésének listázása

1. A rendszerhez vagy a felhasználó által hozzárendelt felügyelt identitás objektumazonosítójának beszereznie.

    A felhasználó által hozzárendelt felügyelt identitás objektumazonosítójának lekért ásához használhatja [az ad sp listát](/cli/azure/ad/sp#az-ad-sp-list) vagy az [identitáslistát.](/cli/azure/identity#az-identity-list)

    ```azurecli-interactive
    az ad sp list --display-name "<name>" --query [].objectId --output tsv
    ```

    A rendszer által hozzárendelt felügyelt identitás objektumazonosítójának lekért ásához használhatja [az ad sp list (Az ad sp list ) listát.](/cli/azure/ad/sp#az-ad-sp-list)

    ```azurecli-interactive
    az ad sp list --display-name "<vmname>" --query [].objectId --output tsv
    ```

1. A szerepkör-hozzárendelések listázásához használja az [az szerepkör-hozzárendelési listát.](/cli/azure/role/assignment#az-role-assignment-list)

    Alapértelmezés szerint csak az aktuális előfizetés szerepkör-hozzárendelései jelennek meg. Az aktuális előfizetés szerepkör-hozzárendeléseinek megtekintéséhez `--all` adja hozzá a paramétert. Az örökölt szerepkör-hozzárendelések megtekintéséhez adja hozzá a `--include-inherited` paramétert.

    ```azurecli-interactive
    az role assignment list --assignee <objectid>
    ```

## <a name="next-steps"></a>További lépések

- [Szerepkör-hozzárendelések hozzáadása vagy eltávolítása az Azure RBAC és az Azure CLI használatával](role-assignments-cli.md)
