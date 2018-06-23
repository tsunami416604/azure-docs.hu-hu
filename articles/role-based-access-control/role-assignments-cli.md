---
title: Az RBAC és az Azure CLI-hozzáférés kezelése |} Microsoft Docs
description: Megtudhatja, hogyan kezelheti a hozzáférést a felhasználók, csoportok és alkalmazások, szerepkörön alapuló hozzáférés-vezérlést (RBAC) és az Azure parancssori felület használatával. Ez magában foglalja a listázási hozzáférés, engedélyezheti a hozzáférést, és megszünteti a hozzáférést.
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
ms.openlocfilehash: 16577339f1aa33fbd1a8b90f4beaef1ee4ce806c
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2018
ms.locfileid: "36316396"
---
# <a name="manage-access-using-rbac-and-azure-cli"></a>Az RBAC és az Azure CLI-hozzáférés kezelése

[Szerepköralapú hozzáférés-vezérlést (RBAC)](overview.md) kezelése az Azure-ban az erőforrásokhoz való hozzáférés módja. Ez a cikk ismerteti, hogyan kezelheti a hozzáférést a felhasználók, csoportok és alkalmazások RBAC és az Azure parancssori felület használatával.

## <a name="prerequisites"></a>Előfeltételek

Hozzáférés kezelése a következők szükségesek:

* [Azure-felhőbe rendszerhéj bash](/azure/cloud-shell/overview)
* [Azure CLI](/cli/azure)

## <a name="list-roles"></a>Lista szerepkörök

Kilistázhatja az összes rendelkezésre álló szerepkör-definíciók [az szerepkör-definíció lista](/cli/azure/role/definition#az-role-definition-list):

```azurecli
az role definition list
```

Az alábbi példa felsorolja a nevét és az összes rendelkezésre álló szerepkör-definíciók leírása:

```azurecli
az role definition list --output json | jq '.[] | {"roleName":.roleName, "description":.description}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs"
}

...
```

Az alábbi példa felsorolja a beépített szerepkör-definíciók mindegyikét:

```azurecli
az role definition list --custom-role-only false --output json | jq '.[] | {"roleName":.roleName, "description":.description, "roleType":.roleType}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs",
  "roleType": "BuiltInRole"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs",
  "roleType": "BuiltInRole"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs",
  "roleType": "BuiltInRole"
}

...
```

### <a name="list-actions-of-a-role"></a>Egy szerepkör lista műveletek

Egy szerepkör-definíció műveleteit kilistázhatja [az szerepkör-definíció lista](/cli/azure/role/definition#az-role-definition-list):

```azurecli
az role definition list --name <role_name>
```

A következő példa listákat a *közreműködő* szerepkör-definíció:

```azurecli
az role definition list --name "Contributor"
```

```Output
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "permissions": [
      {
        "actions": [
          "*"
        ],
        "additionalProperties": {},
        "dataActions": [],
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action"
        ],
        "notDataActions": []
      }
    ],
    "roleName": "Contributor",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

A következő példa listákat a *műveletek* és *notActions* , a *közreműködő* szerepkör:

```azurecli
az role definition list --name "Contributor" --output json | jq '.[] | {"actions":.permissions[0].actions, "notActions":.permissions[0].notActions}'
```

```Output
{
  "actions": [
    "*"
  ],
  "notActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action"
  ]
}
```

Az alábbi példa felsorolja a műveleteket a *virtuális gép közreműködő* szerepkör:

```azurecli
az role definition list --name "Virtual Machine Contributor" --output json | jq '.[] | .permissions[0].actions'
```

```Output
[
  "Microsoft.Authorization/*/read",
  "Microsoft.Compute/availabilitySets/*",
  "Microsoft.Compute/locations/*",
  "Microsoft.Compute/virtualMachines/*",
  "Microsoft.Compute/virtualMachineScaleSets/*",
  "Microsoft.Insights/alertRules/*",
  "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
  "Microsoft.Network/loadBalancers/backendAddressPools/join/action",

  ...

  "Microsoft.Storage/storageAccounts/listKeys/action",
  "Microsoft.Storage/storageAccounts/read"
]
```

## <a name="list-access"></a>A hozzáférési lista

Az RBAC lista eléréséhez felsorolja a szerepkör-hozzárendeléseket.

### <a name="list-role-assignments-for-a-user"></a>Szerepkör-hozzárendelések listáját egy felhasználó számára

Kilistázhatja az adott felhasználó szerepkör-hozzárendelések [az szerepkör társításának listája](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --assignee <assignee>
```

Alapértelmezés szerint csak az előfizetés hatóköre hozzárendelések jelenik meg. Az erőforrás vagy a csoport hatókörű hozzárendeléseinek megtekintéséhez használja a `--all`.

Az alábbi példa felsorolja a közvetlenül hozzárendelt szerepkör-hozzárendelések a *patlong@contoso.com* felhasználó:

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}
```

### <a name="list-role-assignments-for-a-resource-group"></a>Szerepkör-hozzárendelések listáját erőforráscsoport

A szerepkör-hozzárendelések erőforráscsoport létező kilistázhatja [az szerepkör társításának listája](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --resource-group <resource_group>
```

Az alábbi példa felsorolja a szerepkör-hozzárendelések a *pharma-értékesítési-projectforecast* erőforráscsoport:

```azurecli
az role assignment list --resource-group pharma-sales-projectforecast --output json | jq '.[] | {"roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}
{
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}

...
```

## <a name="grant-access"></a>Hozzáférés biztosítása

Az RBAC hozzáférést, létrehoz egy szerepkör-hozzárendelés.

### <a name="create-a-role-assignment-for-a-user"></a>A felhasználói szerepkör-hozzárendelés létrehozása

Egy felhasználó szerepkör-hozzárendelés létrehozása a erőforrás hatóköréből, használja a [az szerepkör-hozzárendelés létrehozása](/cli/azure/role/assignment#az-role-assignment-create):

```azurecli
az role assignment create --role <role> --assignee <assignee> --resource-group <resource_group>
```

Az alábbi példa a *virtuális gép közreműködő* szerepkör *patlong@contoso.com* felhasználójának a *pharma-értékesítési-projectforecast* erőforrás csoport hatóköre:

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales-projectforecast
```

### <a name="create-a-role-assignment-for-a-group"></a>Egy szerepkör-hozzárendelés létrehozása

Egy szerepkör-hozzárendelés a csoport létrehozásához használja [az szerepkör-hozzárendelés létrehozása](/cli/azure/role/assignment#az-role-assignment-create):

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

Az alábbi példa a *olvasó* szerepkört a *Reino Mack Team* azonosító 22222222-2222-2222-2222-222222222222 előfizetés hatókörben csoportban. Ahhoz, hogy a csoport azonosítója, használhatja a [az ad-csoport lista](/cli/azure/ad/group#az-ad-group-list) vagy [az ad-csoport megjelenítése](/cli/azure/ad/group#az-ad-group-show).

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/11111111-1111-1111-1111-111111111111
```

Az alábbi példa a *virtuális gép közreműködő* szerepkört a *Reino Mack Team* azonosító 22222222-2222-2222-2222-222222222222 nevűvirtuálishálózatokerőforráshatókörrecsoportban*pharma-értékesítési-projekt-hálózati*:

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/11111111-1111-1111-1111-111111111111/resourcegroups/pharma-sales-projectforecast/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="create-a-role-assignment-for-an-application"></a>Az alkalmazás szerepkör-hozzárendelés létrehozása

Az alkalmazás-szerepkör létrehozásához használja a [az szerepkör-hozzárendelés létrehozása](/cli/azure/role/assignment#az-role-assignment-create):

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

Az alábbi példa a *virtuális gép közreműködő* alkalmazást objektum azonosítója 44444444-4444-4444-4444-444444444444, a szerepkörnek a *pharma-értékesítési-projectforecast* erőforráscsoport hatókör. Ahhoz, hogy az alkalmazás Objektumazonosító, használhatja a [az ad alkalmazáslistájában](/cli/azure/ad/app#az-ad-app-list) vagy [az ad-alkalmazás megjelenítése](/cli/azure/ad/app#az-ad-app-show).

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales-projectforecast
```

## <a name="remove-access"></a>Hozzáférés eltávolítása

Az RBAC, elérését, eltávolított szerepkör-hozzárendelés használatával [az szerepkör-hozzárendelés törlése](/cli/azure/role/assignment#az-role-assignment-delete):

```azurecli
az role assignment delete --assignee <assignee> --role <role> --resource-group <resource_group>
```

A következő példában eltávolítjuk a *virtuális gép közreműködő* a szerepkör-hozzárendelés a *patlong@contoso.com* felhasználó számára a *pharma-értékesítési-projectforecast* erőforrás csoport:

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales-projectforecast
```

A következő példában eltávolítjuk a *olvasó* szerepkörnek a *Reino Mack Team* azonosító 22222222-2222-2222-2222-222222222222 előfizetés hatókörben csoportban. Ahhoz, hogy a csoport azonosítója, használhatja a [az ad-csoport lista](/cli/azure/ad/group#az-ad-group-list) vagy [az ad-csoport megjelenítése](/cli/azure/ad/group#az-ad-group-show).

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="next-steps"></a>További lépések

- [Oktatóanyag: Hozzon létre egy egyéni biztonsági szerepkört az Azure parancssori felület használatával](tutorial-custom-role-cli.md)
- [Azure-erőforrások és csoportok kezelése az Azure parancssori felület használatával](../azure-resource-manager/xplat-cli-azure-resource-manager.md)