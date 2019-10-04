---
title: Hozzon létre egyéni szerepkörök az Azure-erőforrások használatával a REST API – Azure |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egyéni szerepköröket a szerepköralapú hozzáférés-vezérlés (RBAC) az Azure-erőforrásokhoz a REST API használatával. Ez magában foglalja a listában, létrehozása, frissítése és egyéni szerepkörök törlése.
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 4024f6fdb40c752ef61f348d15f681e81d81c08c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60596679"
---
# <a name="create-custom-roles-for-azure-resources-using-the-rest-api"></a>Az Azure-erőforrásokhoz a REST API-val egyéni szerepkörök létrehozása

Ha a [beépített szerepkörök az Azure-erőforrások](built-in-roles.md) nem felelnek meg a szervezet konkrét igényeinek, saját egyéni szerepköröket is létrehozhat. Ez a cikk ismerteti a REST API-val egyéni szerepkörök létrehozására és kezelésére.

## <a name="list-custom-roles"></a>Egyéni szerepkörök listázása

A címtár összes egyéni szerepkörök listájában, használja a [szerepkör-definíciók - lista](/rest/api/authorization/roledefinitions/list) REST API-t.

1. Indítsa el a következő kérelmet:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Cserélje le *{szűrő}* szerepkör típusú.

    | Szűrés | Leírás |
    | --- | --- |
    | `$filter=type%20eq%20'CustomRole'` | Szűrő CustomRole típusa alapján |

## <a name="list-custom-roles-at-a-scope"></a>Egyéni szerepkörök listája a hatókörben

Egyéni szerepkörök hatókörre listájában, használja a [szerepkör-definíciók - lista](/rest/api/authorization/roledefinitions/list) REST API-t.

1. Indítsa el a következő kérelmet:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Cserélje le az URI-belül *{hatókör}* a hatókörben, amelynek meg szeretné össze a szerepkörök listáját.

    | Scope | Típus |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Előfizetés |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

1. Cserélje le *{szűrő}* szerepkör típusú.

    | Szűrés | Leírás |
    | --- | --- |
    | `$filter=type%20eq%20'CustomRole'` | Szűrő CustomRole típusa alapján |

## <a name="list-a-custom-role-definition-by-name"></a>Egy egyéni szerepkör-definíció listában név szerint

Egyéni szerepkör kapcsolatos információkat a megjelenített név alapján, amelyet a [szerepkör-definíciók – első](/rest/api/authorization/roledefinitions/get) REST API-t.

1. Indítsa el a következő kérelmet:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Cserélje le az URI-belül *{hatókör}* a hatókörben, amelynek meg szeretné össze a szerepkörök listáját.

    | Scope | Típus |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Előfizetés |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

1. Cserélje le *{szűrő}* a szerepkör megjelenített nevét.

    | Szűrés | Leírás |
    | --- | --- |
    | `$filter=roleName%20eq%20'{roleDisplayName}'` | Az URL-kódolású képernyőn a szerepkör pontos megjelenítendő nevét. Például `$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` |

## <a name="list-a-custom-role-definition-by-id"></a>Egy egyéni szerepkör-definíció azonosítója alapján listázása

Egyéni szerepkör adatainak lekérése az egyedi azonosítója alapján, használja a [szerepkör-definíciók – első](/rest/api/authorization/roledefinitions/get) REST API-t.

1. Használja a [szerepkör-definíciók - lista](/rest/api/authorization/roledefinitions/list) REST API-t a GUID-azonosító beszerzése a szerepkörhöz.

1. Indítsa el a következő kérelmet:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Cserélje le az URI-belül *{hatókör}* a hatókörben, amelynek meg szeretné össze a szerepkörök listáját.

    | Scope | Típus |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Előfizetés |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

1. Cserélje le *{roleDefinitionId}* a szerepkör-definíció GUID azonosítóját.

## <a name="create-a-custom-role"></a>Egyéni szerepkör létrehozása

Egyéni szerepkör létrehozásához használja a [szerepkör-definíciók - létrehozási vagy frissítési](/rest/api/authorization/roledefinitions/createorupdate) REST API-t. Az API meghívásához, be kell jelentkeznie jelentkezzen be, amely hozzá van rendelve egy szerepkör, amely rendelkezik a `Microsoft.Authorization/roleDefinitions/write` az összes engedélyt a `assignableScopes`. A beépített szerepkörök, csak [tulajdonosa](built-in-roles.md#owner) és [felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator) tartalmazzák ezt az engedélyt.

1. Tekintse át a [erőforrás-szolgáltatói műveletek](resource-provider-operations.md) , amelyek az engedélyek az egyéni szerepkör létrehozása.

1. Egy GUID eszköz segítségével hozza létre az egyéni szerepkör-azonosító esetében használt egyedi azonosító. Az azonosító formátuma: `00000000-0000-0000-0000-000000000000`

1. Indítsa el a következő kérés és a szervezet:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

    ```json
    {
      "name": "{roleDefinitionId}",
      "properties": {
        "roleName": "",
        "description": "",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
    
            ],
            "notActions": [
    
            ]
          }
        ],
        "assignableScopes": [
          "/subscriptions/{subscriptionId}"
        ]
      }
    }
    ```

1. Cserélje le az URI-belül *{hatókör}* – az első `assignableScopes` az egyéni szerepkör.

    | Scope | Típus |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Előfizetés |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

1. Cserélje le *{roleDefinitionId}* az egyéni szerepkör GUID azonosítóval.

1. A kérelem törzsében található az a `assignableScopes` tulajdonságot használja, cserélje le *{roleDefinitionId}* GUID azonosítóval.

1. Cserélje le *{subscriptionId}* az előfizetés-azonosítóval.

1. Az a `actions` tulajdonság, adja hozzá a szerepkör lehetővé teszi, hogy a végrehajtandó műveleteket.

1. Az a `notActions` tulajdonság, a kizárt műveletek hozzáadása az engedélyezett a `actions`.

1. Az a `roleName` és `description` tulajdonságai között adjon meg egy egyedi szerepkör nevét és leírását. A tulajdonságokkal kapcsolatos további információkért lásd: [egyéni szerepkörök](custom-roles.md).

    Az alábbiakban látható egy példa a kérelem törzséhez:

    ```json
    {
      "name": "88888888-8888-8888-8888-888888888888",
      "properties": {
        "roleName": "Virtual Machine Operator",
        "description": "Can monitor and restart virtual machines.",
        "type": "CustomRole",
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
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000"
        ]
      }
    }
    ```

## <a name="update-a-custom-role"></a>Egyéni szerepkörök frissítése

Egyéni szerepkör frissítéséhez használja a [szerepkör-definíciók – létrehozása vagy frissítése](/rest/api/authorization/roledefinitions/createorupdate) REST API-t. Az API meghívásához, be kell jelentkeznie jelentkezzen be, amely hozzá van rendelve egy szerepkör, amely rendelkezik a `Microsoft.Authorization/roleDefinitions/write` az összes engedélyt a `assignableScopes`. A beépített szerepkörök, csak [tulajdonosa](built-in-roles.md#owner) és [felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator) tartalmazzák ezt az engedélyt.

1. Használja a [szerepkör-definíciók - lista](/rest/api/authorization/roledefinitions/list) vagy [szerepkör-definíciók – első](/rest/api/authorization/roledefinitions/get) REST API-t az egyéni szerepkör adatainak beolvasása. További információkért lásd az előző [egyéni szerepkörök listában](#list-custom-roles) szakaszban.

1. Indítsa el a következő kérelmet:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Cserélje le az URI-belül *{hatókör}* – az első `assignableScopes` az egyéni szerepkör.

    | Scope | Típus |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Előfizetés |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

1. Cserélje le *{roleDefinitionId}* az egyéni szerepkör GUID azonosítóval.

1. Az egyéni szerepkör kapcsolatos információk alapján, hozzon létre egy kérelem törzse a következő formátumban:

    ```json
    {
      "name": "{roleDefinitionId}",
      "properties": {
        "roleName": "",
        "description": "",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
    
            ],
            "notActions": [
    
            ]
          }
        ],
        "assignableScopes": [
          "/subscriptions/{subscriptionId}"
        ]
      }
    }
    ```

1. A szükséges módosításokat, hogy az egyéni szerepkör frissítése a kérelem törzsében.

    Az alábbiakban látható egy példa a kérelem törzsében hozzáadott új diagnosztikai beállítások művelettel:

    ```json
    {
      "name": "88888888-8888-8888-8888-888888888888",
      "properties": {
        "roleName": "Virtual Machine Operator",
        "description": "Can monitor and restart virtual machines.",
        "type": "CustomRole",
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
            "notActions": []
          }
        ],
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000"
        ]
      }
    }
    ```

## <a name="delete-a-custom-role"></a>Egyéni szerepkörök törlése

Egyéni szerepkör törléséhez használja a [szerepkör-definíciók – Törlés](/rest/api/authorization/roledefinitions/delete) REST API-t. Az API meghívásához, be kell jelentkeznie jelentkezzen be, amely hozzá van rendelve egy szerepkör, amely rendelkezik a `Microsoft.Authorization/roleDefinitions/delete` az összes engedélyt a `assignableScopes`. A beépített szerepkörök, csak [tulajdonosa](built-in-roles.md#owner) és [felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator) tartalmazzák ezt az engedélyt.

1. Használja a [szerepkör-definíciók - lista](/rest/api/authorization/roledefinitions/list) vagy [szerepkör-definíciók – első](/rest/api/authorization/roledefinitions/get) REST API-t az egyéni szerepkör GUID azonosítójának lekérése. További információkért lásd az előző [egyéni szerepkörök listában](#list-custom-roles) szakaszban.

1. Indítsa el a következő kérelmet:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Cserélje le az URI-belül *{hatókör}* törli az egyéni szerepkör a hatókörrel rendelkező.

    | Scope | Típus |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Előfizetés |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

1. Cserélje le *{roleDefinitionId}* az egyéni szerepkör GUID azonosítóval.

## <a name="next-steps"></a>További lépések

- [Egyéni szerepkörök Azure-erőforrásokhoz](custom-roles.md)
- [Rbac-RÓL és a REST API használatával Azure-erőforrásokhoz való hozzáférés kezelése](role-assignments-rest.md)
- [Azure REST API-referencia](/rest/api/azure/)