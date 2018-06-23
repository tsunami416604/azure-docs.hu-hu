---
title: A REST API - Azure használatával egyéni szerepkörök létrehozása |} Microsoft Docs
description: Útmutató a szerepköralapú hozzáférés-vezérlést (RBAC) a REST API használatával egyedi szerepkörök létrehozását. Ez magában foglalja a listában, létrehozása, frissítése és egyéni szerepkörök törlése.
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
ms.topic: article
ms.date: 06/20/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 8267846fed30baf2c37dcddd453ae9ead9341da9
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2018
ms.locfileid: "36320589"
---
# <a name="create-custom-roles-using-the-rest-api"></a>A REST API használatával egyéni szerepkörök létrehozása

Ha a [beépített szerepkörök](built-in-roles.md) nem felelnek meg a szervezet igényeinek, saját egyéni szerepköröket is létrehozhat. Ez a cikk ismerteti a REST API használatával egyéni szerepkörök létrehozására és kezelésére.

## <a name="list-roles"></a>Lista szerepkörök

Lista összes szerepkör vagy a megjelenítési név megadásával egyetlen szerepkör vonatkozó információt, használja a [szerepkör-definíciók - lista](/rest/api/authorization/roledefinitions/list) REST API-t. Ez az API hívása, hozzáféréssel kell rendelkeznie a `Microsoft.Authorization/roleDefinitions/read` műveletet a hatókörben. Több [beépített szerepkörök](built-in-roles.md) férhetnek hozzá ezt a műveletet.

1. Indítsa el a következő kért:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Az URI belül cserélje le a *{hatókör}* legyen a szerepkörök elemet a hatókörben.

    | Hatókör | Típus |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Előfizetés |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Erőforrás |

1. Cserélje le *{szűrő}* azzal a feltétellel, amely a szerepkör szűréséhez alkalmazni kívánja.

    | Szűrés | Leírás |
    | --- | --- |
    | `$filter=atScopeAndBelow()` | Szerepkörökhöz rendelhető hozzá a megadott hatókörben és bármely a gyermekhatókör a listában. |
    | `$filter=roleName%20eq%20'{roleDisplayName}'` | Az URL-kódolású képernyőn a pontos megjelenített névbe, a szerepkör. Például `$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` |

### <a name="get-information-about-a-role"></a>Egy szerepkör adatainak beolvasása

Egy szerepkör, szerepkör-definíció azonosítójának használatával kapcsolatos információk beszerzéséhez használja a [szerepkör-definíciók - beolvasása](/rest/api/authorization/roledefinitions/get) REST API-t. Ez az API hívása, hozzáféréssel kell rendelkeznie a `Microsoft.Authorization/roleDefinitions/read` műveletet a hatókörben. Több [beépített szerepkörök](built-in-roles.md) férhetnek hozzá ezt a műveletet.

Ahhoz, hogy a megjelenített név megadásával egyetlen szerepkör információt, lásd az előző: [szerepkörök listában](custom-roles-rest.md#list-roles) szakasz.

1. Használja a [szerepkör-definíciók - lista](/rest/api/authorization/roledefinitions/list) REST API-t a GUID azonosító beszerzése a szerepkörhöz. A beépített szerepkörök is lehet a azonosítója [beépített szerepkörök](built-in-roles.md).

1. Indítsa el a következő kért:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Az URI belül cserélje le a *{hatókör}* legyen a szerepkörök elemet a hatókörben.

    | Hatókör | Típus |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Előfizetés |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Erőforrás |

1. Cserélje le *{roledefinitionid-értékkel}* a szerepkör-definíció GUID azonosítóval.

## <a name="create-a-custom-role"></a>Egyéni szerepkör létrehozása

Egy egyéni biztonsági szerepkört hozhat létre a [szerepkör-definíciók - létrehozása vagy frissítése](/rest/api/authorization/roledefinitions/createorupdate) REST API-t. Ez az API hívása, hozzáféréssel kell rendelkeznie a `Microsoft.Authorization/roleDefinitions/write` minden műveletet a `assignableScopes`. A beépített szerepkörök, csak [tulajdonos](built-in-roles.md#owner) és [felhasználói hozzáférés adminisztrátora](built-in-roles.md#user-access-administrator) férhetnek hozzá ezt a műveletet. 

1. Tekintse át a listában, [erőforrás-szolgáltatói műveletekhez](resource-provider-operations.md) , amelyek számára történő létrehozásához az egyéni szerepkör engedélyeit.

1. A GUID eszköz használatával fog történni az egyéni szerepkör-azonosító egyedi azonosító létrehozása. Az azonosító formátuma alább látható: `00000000-0000-0000-0000-000000000000`

1. Indítsa el a következő kérés és fő:

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

1. Az URI belül cserélje le a *{hatókör}* az első `assignableScopes` az egyéni szerepkör.

    | Hatókör | Típus |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Előfizetés |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Erőforrás |

1. Cserélje le *{roledefinitionid-értékkel}* az egyéni szerepkör GUID azonosítóval.

1. A kérelem törzsében belül a a `assignableScopes` tulajdonság, cserélje le *{roledefinitionid-értékkel}* GUID azonosítóval.

1. Cserélje le *{subscriptionId}* az előfizetés-azonosítóval.

1. Az a `actions` tulajdonság, vegye fel a műveleteket, amely a szerepkör lehetővé teszi a végrehajtását.

1. Az a `notActions` tulajdonság, vegye fel a műveleteket, amelyek ki lettek zárva az engedélyezett a `actions`.

1. Az a `roleName` és `description` tulajdonságokat, adjon meg egy egyedi szerepkör nevét és leírását. Tulajdonságaival kapcsolatos további információkért lásd: [egyéni szerepkörök](custom-roles.md).

    A következő egy kérelemtörzset példáját mutatja be:

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

## <a name="update-a-custom-role"></a>Frissítés egy egyéni biztonsági szerepkört

Egy egyéni biztonsági szerepkört frissítéséhez használja a [szerepkör-definíciók - létrehozása vagy frissítése](/rest/api/authorization/roledefinitions/createorupdate) REST API-t. Ez az API hívása, hozzáféréssel kell rendelkeznie a `Microsoft.Authorization/roleDefinitions/write` minden műveletet a `assignableScopes`. A beépített szerepkörök, csak [tulajdonos](built-in-roles.md#owner) és [felhasználói hozzáférés adminisztrátora](built-in-roles.md#user-access-administrator) férhetnek hozzá ezt a műveletet. 

1. Használja a [szerepkör-definíciók - lista](/rest/api/authorization/roledefinitions/list) vagy [szerepkör-definíciók - beolvasása](/rest/api/authorization/roledefinitions/get) REST API segítségével szerezzen információt az egyéni biztonsági szerepkört. További információkért tekintse meg a korábban [szerepkörök listában](custom-roles-rest.md#list-roles) szakasz.

1. Indítsa el a következő kért:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Az URI belül cserélje le a *{hatókör}* az első `assignableScopes` az egyéni szerepkör.

    | Hatókör | Típus |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Előfizetés |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Erőforrás |

1. Cserélje le *{roledefinitionid-értékkel}* az egyéni szerepkör GUID azonosítóval.

1. Az egyéni biztonsági szerepkört kapcsolatos információk alapján hozzon létre egy kérelemtörzset a következő formátumban:

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

1. A kérelem törzsében frissítheti a engedélyezni szeretné az egyéni szerepkörhöz módosításokkal.

    A következő példáját mutatja be egy kérelemtörzset hozzáadott új diagnosztikai beállítások művelettel:

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

## <a name="delete-a-custom-role"></a>Egyéni szerepkör törléséhez

Egyéni szerepkör törléséhez használja a [szerepkör-definíciók - törlése](/rest/api/authorization/roledefinitions/delete) REST API-t. Ez az API hívása, hozzáféréssel kell rendelkeznie a `Microsoft.Authorization/roleDefinitions/delete` minden műveletet a `assignableScopes`. A beépített szerepkörök, csak [tulajdonos](built-in-roles.md#owner) és [felhasználói hozzáférés adminisztrátora](built-in-roles.md#user-access-administrator) férhetnek hozzá ezt a műveletet. 

1. Használja a [szerepkör-definíciók - lista](/rest/api/authorization/roledefinitions/list) vagy [szerepkör-definíciók - beolvasása](/rest/api/authorization/roledefinitions/get) REST API-t az egyéni szerepkör GUID-azonosító eléréséhez. További információkért tekintse meg a korábban [szerepkörök listában](custom-roles-rest.md#list-roles) szakasz.

1. Indítsa el a következő kért:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Az URI belül cserélje le a *{hatókör}* a hatókörrel, hogy törli az egyéni biztonsági szerepkört.

    | Hatókör | Típus |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Előfizetés |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Erőforrás |

1. Cserélje le *{roledefinitionid-értékkel}* az egyéni szerepkör GUID azonosítóval.

## <a name="next-steps"></a>További lépések

- [Egyéni szerepkörök az Azure-ban](custom-roles.md)
- [Az RBAC és a REST API-hozzáférés kezelése](role-assignments-rest.md)
- [Az Azure REST API-referencia](/rest/api/azure/)