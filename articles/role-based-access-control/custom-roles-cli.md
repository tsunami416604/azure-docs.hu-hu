---
title: Egyéni szerepkörök létrehozása vagy frissítése az Azure-erőforrásokhoz az Azure CLI használatával | Microsoft dokumentumok
description: Megtudhatja, hogyan listázhatja, hozhat létre, frissítheti vagy törölheti az egyéni szerepköröket az Azure CLI használatával szerepköralapú hozzáférés-vezérléssel (RBAC).
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
ms.date: 03/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 44676f7b92c2bcd30612295840054ab2f0c0cf12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062232"
---
# <a name="create-or-update-custom-roles-for-azure-resources-using-azure-cli"></a>Egyéni szerepkörök létrehozása vagy frissítése az Azure-erőforrásokhoz az Azure CLI használatával

> [!IMPORTANT]
> Felügyeleti csoport hozzáadása `AssignableScopes` jelenleg előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

Ha az [Azure-erőforrások beépített szerepkörei](built-in-roles.md) nem felelnek meg a szervezet egyedi igényeinek, létrehozhat saját egyéni szerepköröket. Ez a cikk ismerteti, hogyan listázheti, hozhat létre, frissítheti vagy törölheti az egyéni szerepköröket az Azure CLI használatával.

Az egyéni szerepkör létrehozásáról részletes oktatóanyagaz [Oktatóanyag: Egyéni szerepkör létrehozása az Azure-erőforrásokhoz az Azure CLI használatával című](tutorial-custom-role-cli.md)témakörben látható.

## <a name="prerequisites"></a>Előfeltételek

Egyéni szerepkörök létrehozásához a következőkre van szükség:

- Egyéni szerepkörök létrehozására vonatkozó engedélyre, amely lehet például [Tulajdonos](built-in-roles.md#owner) vagy [Felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator)
- [Azure Cloud Shell](../cloud-shell/overview.md) vagy [Azure CLI](/cli/azure/install-azure-cli)

## <a name="list-custom-roles"></a>Egyéni szerepkörök listázása

A hozzárendeléshez rendelkezésre álló egyéni szerepkörök listázásához használja [az az szerepkör-definíciós listát.](/cli/azure/role/definition#az-role-definition-list) Az alábbi példák az aktuális előfizetés összes egyéni szerepkörét felsorolják.

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

## <a name="list-a-custom-role-definition"></a>Egyéni szerepkör-definíció felsorolása

Egyéni szerepkör-definíció listázásához használja [az az szerepkör-definíciós listát.](/cli/azure/role/definition#az-role-definition-list) Ez ugyanaz a parancs, amelyet a beépített szerepkörhöz használna.

```azurecli
az role definition list --name <role_name>
```

A következő példa a *virtuálisgép-üzemeltető* szerepkör-definícióját sorolja fel:

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

A következő példa csak a *Virtuálisgép-üzemeltető* szerepkör feladatait sorolja fel:

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

Egyéni szerepkör létrehozásához használja [az az szerepkör-definíció létrehozása](/cli/azure/role/definition#az-role-definition-create). A szerepkör-definíció lehet JSON-leírás vagy JSON-leírást tartalmazó fájl elérési útja.

```azurecli
az role definition create --role-definition <role_definition>
```

A következő példa létrehoz egy egyéni szerepkört *nevű Virtuálisgép-üzemeltető.* Ez az egyéni szerepkör hozzáférést rendel a *Microsoft.Compute*, *a Microsoft.Storage*és a *Microsoft.Network* erőforrás-szolgáltatók összes olvasási műveletéhez, és hozzáférést rendel a virtuális gépek indításához, újraindításához és figyeléséhez. Ez az egyéni szerepkör két előfizetésben használható. Ez a példa egy JSON-fájlt használ bemenetként.

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

Egyéni szerepkör frissítéséhez először használja [az szerepkör-definíciós listát](/cli/azure/role/definition#az-role-definition-list) a szerepkör-definíció lekéréséhez. Másodszor, a kívánt módosításokat a szerepkör-definíció. Végül használja [az szerepkör-definíció frissítés](/cli/azure/role/definition#az-role-definition-update) a frissített szerepkör-definíció mentéséhez.

```azurecli
az role definition update --role-definition <role_definition>
```

A következő példa hozzáadja a *Microsoft.Insights/diagnosticSettings/* műveletet, `Actions` és hozzáad egy felügyeleti csoportot a `AssignableScopes` *Virtuálisgép-üzemeltető* egyéni szerepkörhöz. Felügyeleti csoport hozzáadása `AssignableScopes` jelenleg előzetes verzióban érhető el.

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
    "/subscriptions/33333333-3333-3333-3333-333333333333",
    "/providers/Microsoft.Management/managementGroups/marketing-group"
  ]
}
```

```azurecli
az role definition update --role-definition ~/roles/vmoperator.json
```

## <a name="delete-a-custom-role"></a>Egyéni szerepkörök törlése

Egyéni szerepkör törléséhez használja [az az szerepkördefiníció törlését.](/cli/azure/role/definition#az-role-definition-delete) A törölni kívánt szerepkör megadásához használja a szerepkör nevét vagy a szerepkör-azonosítót. A szerepkör-azonosító meghatározásához használja az [az szerepkör-definíciós listát.](/cli/azure/role/definition#az-role-definition-list)

```azurecli
az role definition delete --name <role_name or role_id>
```

A következő példa törli a *Virtuálisgép-üzemeltető* egyéni szerepkört.

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>További lépések

- [Oktatóanyag: Hozzon létre egy egyéni szerepkört az Azure-erőforrásokhoz az Azure CLI használatával](tutorial-custom-role-cli.md)
- [Egyéni szerepkörök Azure-erőforrásokhoz](custom-roles.md)
- [Az Azure Resource Manager erőforrás-szolgáltatóműveletei](resource-provider-operations.md)