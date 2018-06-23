---
title: Hozzon létre egyéni szerepkörök Azure parancssori felületével |} Microsoft Docs
description: Megtudhatja, hogyan hozzon létre egyéni szerepkörök az Azure parancssori felület használatával szerepköralapú hozzáférés-vezérlést (RBAC). Ez magában foglalja a listában, létrehozása, frissítése és egyéni szerepkörök törlése.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/20/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: b1df50c73497e3f5ad64a7ba7210079871b155e0
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321156"
---
# <a name="create-custom-roles-using-azure-cli"></a>Hozzon létre egyéni szerepkörök az Azure parancssori felület használatával

Ha a [beépített szerepkörök](built-in-roles.md) nem felelnek meg a szervezet igényeinek, saját egyéni szerepköröket is létrehozhat. Ez a cikk ismerteti az Azure parancssori felület használatával egyéni szerepkörök létrehozására és kezelésére.

## <a name="prerequisites"></a>Előfeltételek

Egyedi szerepkörök létrehozását, az alábbiak szükségesek:

- Egyéni szerepkörök létrehozásához szükséges engedélyek [tulajdonos](built-in-roles.md#owner) vagy [felhasználói hozzáférés adminisztrátora](built-in-roles.md#user-access-administrator)
- [Az Azure CLI](/cli/azure/install-azure-cli) helyben telepítve

## <a name="list-custom-roles"></a>Egyéni szerepkörök listája

Kilistázhatja az kiosztására használható egyéni szerepkörök [az szerepkör-definíció lista](/cli/azure/role/definition#az-role-definition-list). Az alábbi példákban a egyéni szerepkörök az aktuális előfizetésben.

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

## <a name="create-a-custom-role"></a>Egyéni szerepkör létrehozása

Egy egyéni biztonsági szerepkört hozhat létre [az szerepkör-definíció létrehozása](/cli/azure/role/definition#az-role-definition-create). A szerepkör-definíció lehet JSON-leírásuk vagy egy JSON-leírásuk tartalmazó fájl elérési útját.

```azurecli
az role definition create --role-definition <role_definition>
```

Az alábbi példa létrehoz egy egyéni biztonsági szerepkört nevű *virtuális gépet üzemeltető*. Az egyéni szerepkör hozzáférést rendel hozzá az összes olvasási műveletek *Microsoft.Compute*, *Microsoft.Storage*, és *Microsoft.Network* erőforrás-szolgáltatók és rendel hozzáférés Indítsa el, indítsa újra, és a virtuális gépek figyelése. Az egyéni szerepkör két előfizetések használható. A példa egy JSON-fájl bemenetként.

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

## <a name="update-a-custom-role"></a>Frissítés egy egyéni biztonsági szerepkört

Ha egy egyéni biztonsági szerepkört frissítéséhez először az [szerepkör-definíció listán az](/cli/azure/role/definition#az-role-definition-list) szerepkör-definíció beolvasása. A szerepkör-definíció, végezze el a szükséges módosításokat. Végül [az szerepkör-definíció frissítése](/cli/azure/role/definition#az-role-definition-update) frissített szerepkör-definíció mentése.

```azurecli
az role definition update --role-definition <role_definition>
```

A következő példakóddal felveheti a *Microsoft.Insights/diagnosticSettings/* művelet a *műveletek* , a *virtuális gépet üzemeltető* egyéni biztonsági szerepkört.

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

## <a name="delete-a-custom-role"></a>Egyéni szerepkör törléséhez

Egyéni szerepkör törléséhez használja [az szerepkör-definíció törlése](/cli/azure/role/definition#az-role-definition-delete). A szerepkör törléséhez megadásához használja a szerepkör nevét vagy a szerepkör-azonosítót. A szerepkör-Azonosítót meghatározásához [az szerepkör-definíció lista](/cli/azure/role/definition#az-role-definition-list).

```azurecli
az role definition delete --name <role_name or role_id>
```

A következő példa törli a *virtuális gépet üzemeltető* egyéni biztonsági szerepkört.

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>További lépések

- [Oktatóanyag: Hozzon létre egy egyéni biztonsági szerepkört az Azure parancssori felület használatával](tutorial-custom-role-cli.md)
- [Egyéni szerepkörök az Azure-ban](custom-roles.md)
- [Az Azure Resource Manager erőforrás-szolgáltatói műveletekhez](resource-provider-operations.md)