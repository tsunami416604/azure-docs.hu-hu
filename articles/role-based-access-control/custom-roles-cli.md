---
title: Hozzon létre egyéni szerepkörök az Azure-erőforrások Azure parancssori felületével |} A Microsoft Docs
description: Megtudhatja, hogyan hozhat létre egyéni szerepköröket a szerepköralapú hozzáférés-vezérlés (RBAC) az Azure-erőforrások Azure CLI használatával. Ez magában foglalja a listában, létrehozása, frissítése és egyéni szerepkörök törlése.
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
ms.date: 02/20/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: ebced83346a7b130598e4a5f49a72d51ffd18e4f
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56584896"
---
# <a name="create-custom-roles-for-azure-resources-using-azure-cli"></a>Egyéni szerepkörök az Azure CLI használatával Azure-erőforrások létrehozása

Ha a [beépített szerepkörök az Azure-erőforrások](built-in-roles.md) nem felelnek meg a szervezet konkrét igényeinek, saját egyéni szerepköröket is létrehozhat. Ez a cikk bemutatja, hogyan hozhat létre és kezelhet az Azure CLI-vel egyéni szerepkörök.

Egyéni szerepkör létrehozásával egy részletes útmutató: [oktatóanyag: Hozzon létre egy egyéni szerepkört az Azure-erőforrások Azure CLI-vel](tutorial-custom-role-cli.md).

## <a name="prerequisites"></a>Előfeltételek

Egyéni szerepkörök létrehozása, az alábbiak szükségesek:

- Egyéni szerepkörök létrehozására vonatkozó engedélyre, amely lehet például [Tulajdonos](built-in-roles.md#owner) vagy [Felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator)
- [Az Azure Cloud Shell](../cloud-shell/overview.md) vagy [az Azure CLI](/cli/azure/install-azure-cli)

## <a name="list-custom-roles"></a>Egyéni szerepkörök listázása

Egyéni szerepkörök hozzárendelése elérhető listájában, használja a [az role definition listájában](/cli/azure/role/definition#az-role-definition-list). Az alábbi példákban a egyéni szerepkörök az aktuális előfizetésben.

```azurecli
az role definition list --custom-role-only true --output json | jq '.[] | {"roleName":.roleName, "roleType":.roleType}'
```

```azurecli
az role definition list --output json | jq '.[] | if .roleType == "CustomRole" then {"roleName":.roleName, "roleType":.roleType} else empty end'
```

```Output
{
  "roleName": "My Management Contributor",
  "type": "CustomRole"
}
{
  "roleName": "My Service Reader Role",
  "type": "CustomRole"
}
{
  "roleName": "Virtual Machine Operator",
  "type": "CustomRole"
}

...
```

## <a name="list-a-custom-role-definition"></a>Egy egyéni szerepkör-definíció listázása

Egy egyéni szerepkör-definíció listázásához használja [az role definition listájában](/cli/azure/role/definition#az-role-definition-list). Ez a beépített szerepkör használja ugyanazt a parancsot.

```azurecli
az role definition list --name <role_name>
```

A következő példa listákat a *virtuális gépet üzemeltető* szerepkör-definíció:

```azurecli
az role definition list --name "Virtual Machine Operator"
```

```Output
[
  {
    "assignableScopes": [
      "/subscriptions/11111111-1111-1111-1111-111111111111"
    ],
    "description": "Can monitor and restart virtual machines.",
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/00000000-0000-0000-0000-000000000000",
    "name": "00000000-0000-0000-0000-000000000000",
    "permissions": [
      {
        "actions": [
          "Microsoft.Storage/*/read",
          "Microsoft.Network/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action",
          "Microsoft.Authorization/*/read",
          "Microsoft.ResourceHealth/availabilityStatuses/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Insights/diagnosticSettings/*",
          "Microsoft.Support/*"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "Virtual Machine Operator",
    "roleType": "CustomRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

Az alábbi példa felsorolja a csak a műveletek a *virtuális gépet üzemeltető* szerepkör:

```azurecli
az role definition list --name "Virtual Machine Operator" --output json | jq '.[] | .permissions[0].actions'
```

```Output
[
  "Microsoft.Storage/*/read",
  "Microsoft.Network/*/read",
  "Microsoft.Compute/*/read",
  "Microsoft.Compute/virtualMachines/start/action",
  "Microsoft.Compute/virtualMachines/restart/action",
  "Microsoft.Authorization/*/read",
  "Microsoft.ResourceHealth/availabilityStatuses/read",
  "Microsoft.Resources/subscriptions/resourceGroups/read",
  "Microsoft.Insights/alertRules/*",
  "Microsoft.Insights/diagnosticSettings/*",
  "Microsoft.Support/*"
]
```

## <a name="create-a-custom-role"></a>Egyéni szerepkör létrehozása

Egy egyéni biztonsági szerepkört hozhat létre [az szerepkör-definíció létrehozására](/cli/azure/role/definition#az-role-definition-create). A szerepkör-definíció JSON-leírásuk és a egy JSON-leírásuk tartalmazó fájl elérési útját is lehetnek.

```azurecli
az role definition create --role-definition <role_definition>
```

A következő példában létrehozunk egy egyéni szerepkör nevű *virtuális gépet üzemeltető*. Az egyéni szerepkör-hozzáférési jogosultságot rendel az összes olvasási műveletek *Microsoft.Compute*, *Microsoft.Storage*, és *Microsoft.Network* erőforrás-szolgáltatók és engedményeseire hozzáférés Indítsa el, és indítsa újra a virtuális gépek figyelése céljából. Az egyéni szerepkör is használható két előfizetéssel. Ebben a példában egy JSON-fájlt használja bemenetként.

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333"
  ]
}
```

```azurecli
az role definition create --role-definition ~/roles/vmoperator.json
```

## <a name="update-a-custom-role"></a>Egyéni szerepkörök frissítése

Egyéni szerepkör frissítéséhez először használja [az role definition listájában](/cli/azure/role/definition#az-role-definition-list) a szerepkör-definíció lekéréséhez. A második végezze el a kívánt módosításokat a szerepkör-definíció. Végül a [az szerepkör-definíció frissítési](/cli/azure/role/definition#az-role-definition-update) frissített szerepkör-definíció mentéséhez.

```azurecli
az role definition update --role-definition <role_definition>
```

Az alábbi példa hozzáadja a *Microsoft.Insights/diagnosticSettings/* művelet a *műveletek* , a *virtuális gépet üzemeltető* egyéni szerepkör.

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333"
  ]
}
```

```azurecli
az role definition update --role-definition ~/roles/vmoperator.json
```

## <a name="delete-a-custom-role"></a>Egyéni szerepkörök törlése

Egyéni szerepkör törléséhez használja [az szerepkör-definíció törlése](/cli/azure/role/definition#az-role-definition-delete). A szerepkör törléséhez használja a szerepkör neve vagy a szerepkör-azonosítót. A szerepkör-azonosító meghatározásához [az role definition listájában](/cli/azure/role/definition#az-role-definition-list).

```azurecli
az role definition delete --name <role_name or role_id>
```

Az alábbi példával törölhet a *virtuális gépet üzemeltető* egyéni szerepkör.

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>További lépések

- [Oktatóanyag: Az Azure-erőforrások Azure CLI-vel egyéni szerepkör létrehozása](tutorial-custom-role-cli.md)
- [Egyéni szerepkörök az Azure-erőforrásokhoz](custom-roles.md)
- [Az Azure Resource Manager erőforrás-szolgáltatói műveletek](resource-provider-operations.md)