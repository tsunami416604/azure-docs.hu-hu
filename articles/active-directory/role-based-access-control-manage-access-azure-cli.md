---
title: "Az Azure CLI szerepköralapú hozzáférés-vezérlés (RBAC) kezelése |} Microsoft Docs"
description: "Megtudhatja, hogyan kezelheti a szerepköralapú hozzáférés-vezérlést (RBAC) az Azure parancssori felületével, szerepkörök és a szerepkör műveletek listázása és szerepkörök hozzárendelése az előfizetés és az alkalmazás hatókörhöz."
services: active-directory
documentationcenter: 
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.openlocfilehash: 5c099a7fd8848c2934603ec9b2db8947885226f9
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2018
---
# <a name="manage-role-based-access-control-with-the-azure-command-line-interface"></a>Szerepköralapú hozzáférés-vezérlés az Azure parancssori felületével kezelése

> [!div class="op_single_selector"]
> * [PowerShell](role-based-access-control-manage-access-powershell.md)
> * [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
> * [REST API](role-based-access-control-manage-access-rest.md)


A szerepköralapú hozzáférés-vezérlés (RBAC), definiált felhasználók, csoportok és szolgáltatásnevekről hozzáférést egy adott hatókörhöz szerepkörök hozzárendelése. A cikkből megtudhatja, hogyan kezelheti a hozzáférést az Azure parancssori felület (CLI) használatával.

## <a name="prerequisites"></a>Előfeltételek

Az Azure parancssori felület használatával RBAC felügyeli, a következő előfeltételeknek kell rendelkeznie:

* [Azure CLI 2.0](/cli/azure/overview). Használhatja a böngészőjében az [Azure Cloud Shell-lel](../cloud-shell/overview.md), vagy [telepítheti](/cli/azure/install-azure-cli) macOS, Linux és Windows rendszeren, és futtathatja a parancssorból.

## <a name="list-roles"></a>Lista szerepkörök

### <a name="list-role-definitions"></a>Szerepkör-definíciók listája

Kilistázhatja az összes rendelkezésre álló szerepkör-definíciók [az szerepkör-definíció lista](/cli/azure/role/definition#az_role_definition_list):

```azurecli
az role definition list
```

Az alábbi példa felsorolja a nevét és az összes rendelkezésre álló szerepkör-definíciók leírása:

```azurecli
az role definition list --output json | jq '.[] | {"roleName":.properties.roleName, "description":.properties.description}'
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
az role definition list --custom-role-only false --output json | jq '.[] | {"roleName":.properties.roleName, "description":.properties.description, "type":.properties.type}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs",
  "type": "BuiltInRole"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs",
  "type": "BuiltInRole"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs",
  "type": "BuiltInRole"
}

...
```

### <a name="list-actions-of-a-role-definition"></a>Egy szerepkör-definíció lista műveletek

Egy szerepkör-definíció műveleteit kilistázhatja [az szerepkör-definíció lista](/cli/azure/role/definition#az_role_definition_list):

```azurecli
az role definition list --name <role_name>
```

A következő példa listákat a *közreműködő* szerepkör-definíció:

```azurecli
az role definition list --name "Contributor"
```

```Output
[
  {
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "properties": {
      "additionalProperties": {
        "createdBy": null,
        "createdOn": "0001-01-01T08:00:00.0000000Z",
        "updatedBy": null,
        "updatedOn": "2016-12-14T02:04:45.1393855Z"
      },
      "assignableScopes": [
        "/"
      ],
      "description": "Lets you manage everything except access to resources.",
      "permissions": [
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
      ],
      "roleName": "Contributor",
      "type": "BuiltInRole"
    },
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

A következő példa listákat a *műveletek* és *notActions* , a *közreműködő* szerepkör:

```azurecli
az role definition list --name "Contributor" --output json | jq '.[] | {"actions":.properties.permissions[0].actions, "notActions":.properties.permissions[0].notActions}'
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
az role definition list --name "Virtual Machine Contributor" --output json | jq '.[] | .properties.permissions[0].actions'
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

### <a name="list-role-assignments-for-a-user"></a>Szerepkör-hozzárendelések listáját egy felhasználó számára

Kilistázhatja az adott felhasználó szerepkör-hozzárendelések [az szerepkör társításának listája](/cli/azure/role/assignment#az_role_assignment_list):

```azurecli
az role assignment list --assignee <assignee>
```

Alapértelmezés szerint csak az előfizetés hatóköre hozzárendelések jelenik meg. Az erőforrás vagy a csoport hatókörű hozzárendeléseinek megtekintéséhez használja a `--all`.

Az alábbi példa felsorolja a közvetlenül hozzárendelt szerepkör-hozzárendelések a  *patlong@contoso.com*  felhasználó:

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.properties.principalName, "roleDefinitionName":.properties.roleDefinitionName, "scope":.properties.scope}'
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

A szerepkör-hozzárendelések erőforráscsoport létező kilistázhatja [az szerepkör társításának listája](/cli/azure/role/assignment#az_role_assignment_list):

```azurecli
az role assignment list --resource-group <resource_group>
```

Az alábbi példa felsorolja a szerepkör-hozzárendelések a *pharma-értékesítési-projectforecast* erőforráscsoport:

```azurecli
az role assignment list --resource-group pharma-sales-projectforecast --output json | jq '.[] | {"roleDefinitionName":.properties.roleDefinitionName, "scope":.properties.scope}'
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

## <a name="assign-access"></a>Hozzáférés hozzárendelése

### <a name="assign-a-role-to-a-user"></a>A szerepkör hozzárendelése felhasználóhoz

A szerepkör hozzárendelése egy felhasználóhoz a erőforrás csoport hatókörben, használja a [az szerepkör-hozzárendelés létrehozása](/cli/azure/role/assignment#az_role_assignment_create):

```azurecli
az role assignment create --role <role> --assignee <assignee> --resource-group <resource_group>
```

Az alábbi példa a *virtuális gép közreműködő* szerepkör  *patlong@contoso.com*  felhasználójának a *pharma-értékesítési-projectforecast* erőforrás csoport hatóköre:

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales-projectforecast
```

### <a name="assign-a-role-to-a-group"></a>A szerepkör hozzárendelése egy csoporthoz

A szerepkör hozzárendelése egy csoporthoz, használja a [az szerepkör-hozzárendelés létrehozása](/cli/azure/role/assignment#az_role_assignment_create):

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

Az alábbi példa a *olvasó* szerepkört a *Reino Mack Team* azonosító 22222222-2222-2222-2222-222222222222 előfizetés hatókörben csoportban. Ahhoz, hogy a csoport azonosítója, használhatja a [az ad-csoport lista](/cli/azure/ad/group#az_ad_group_list) vagy [az ad-csoport megjelenítése](/cli/azure/ad/group#az_ad_group_show).

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/11111111-1111-1111-1111-111111111111
```

Az alábbi példa a *virtuális gép közreműködő* szerepkört a *Reino Mack Team* azonosító 22222222-2222-2222-2222-222222222222 nevűvirtuálishálózatokerőforráshatókörrecsoportban*pharma-értékesítési-projekt-hálózati*:

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/11111111-1111-1111-1111-111111111111/resourcegroups/pharma-sales-projectforecast/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="assign-a-role-to-an-application"></a>Az alkalmazás szerepkör hozzárendelése

Szerepkör hozzárendelése egy alkalmazást, használja a [az szerepkör-hozzárendelés létrehozása](/cli/azure/role/assignment#az_role_assignment_create):

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

Az alábbi példa a *virtuális gép közreműködő* alkalmazást objektum azonosítója 44444444-4444-4444-4444-444444444444, a szerepkörnek a *pharma-értékesítési-projectforecast* erőforráscsoport hatókör. Ahhoz, hogy az alkalmazás Objektumazonosító, használhatja a [az ad alkalmazáslistájában](/cli/azure/ad/app#az_ad_app_list) vagy [az ad-alkalmazás megjelenítése](/cli/azure/ad/app#az_ad_app_show).

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales-projectforecast
```

## <a name="remove-access"></a>Megszünteti a hozzáférést

### <a name="remove-a-role-assignment"></a>Távolítsa el a szerepkör-hozzárendelés

Szerepkör-hozzárendelés eltávolításához használja [az szerepkör-hozzárendelés törlése](/cli/azure/role/assignment#az_role_assignment_delete):

```azurecli
az role assignment delete --assignee <assignee> --role <role> --resource-group <resource_group>
```

A következő példában eltávolítjuk a *virtuális gép közreműködő* a szerepkör-hozzárendelés a  *patlong@contoso.com*  felhasználó számára a *pharma-értékesítési-projectforecast* erőforrás csoport:

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales-projectforecast
```

A következő példában eltávolítjuk a *olvasó* szerepkörnek a *Reino Mack Team* azonosító 22222222-2222-2222-2222-222222222222 előfizetés hatókörben csoportban. Ahhoz, hogy a csoport azonosítója, használhatja a [az ad-csoport lista](/cli/azure/ad/group#az_ad_group_list) vagy [az ad-csoport megjelenítése](/cli/azure/ad/group#az_ad_group_show).

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="custom-roles"></a>Egyéni szerepkörök

### <a name="list-custom-roles"></a>Egyéni szerepkörök listája

A szerepkörök, amelyek rendelhető hozzá hatókör kilistázhatja [az szerepkör-definíció lista](/cli/azure/role/definition#az_role_definition_list).

Az alábbi példák mindegyikét listában a egyéni szerepkörök az aktuális előfizetésben:

```azurecli
az role definition list --custom-role-only true --output json | jq '.[] | {"roleName":.properties.roleName, "type":.properties.type}'
```

```azurecli
az role definition list --output json | jq '.[] | if .properties.type == "CustomRole" then {"roleName":.properties.roleName, "type":.properties.type} else empty end'
```

```Output
{
  "roleName": "My Management Contributor",
  "type": "CustomRole"
}
{
  "roleName": "My Service Operator Role",
  "type": "CustomRole"
}
{
  "roleName": "My Service Reader Role",
  "type": "CustomRole"
}

...
```

### <a name="create-a-custom-role"></a>Egyéni szerepkör létrehozása

Egy egyéni biztonsági szerepkört hozhat létre [az szerepkör-definíció létrehozása](/cli/azure/role/definition#az_role_definition_create). A szerepkör-definíció lehet JSON-leírásuk vagy egy JSON-leírásuk tartalmazó fájl elérési útját.

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

### <a name="update-a-custom-role"></a>Frissítés egy egyéni biztonsági szerepkört

Ha egy egyéni biztonsági szerepkört frissítéséhez először az [szerepkör-definíció listán az](/cli/azure/role/definition#az_role_definition_list) szerepkör-definíció beolvasása. A szerepkör-definíció, végezze el a szükséges módosításokat. Végül [az szerepkör-definíció frissítése](/cli/azure/role/definition#az_role_definition_update) frissített szerepkör-definíció mentése.

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

### <a name="delete-a-custom-role"></a>Egyéni szerepkör törléséhez

Egyéni szerepkör törléséhez használja [az szerepkör-definíció törlése](/cli/azure/role/definition#az_role_definition_delete). A szerepkör törléséhez megadásához használja a szerepkör nevét vagy a szerepkör-azonosítót. A szerepkör-Azonosítót meghatározásához [az szerepkör-definíció lista](/cli/azure/role/definition#az_role_definition_list).

```azurecli
az role definition delete --name <role_name or role_id>
```

A következő példa törli a *virtuális gépet üzemeltető* egyéni biztonsági szerepkört:

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>További lépések

[!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

