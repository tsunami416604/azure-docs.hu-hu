---
title: Azure-erőforrások egyéni szerepköreinek létrehozása vagy frissítése a REST API
description: Megtudhatja, hogyan listázhat, hozhat létre, frissíthet vagy törölhet egyéni szerepköröket a szerepköralapú hozzáférés-vezérléssel (RBAC) az Azure-erőforrásokhoz az REST API használatával.
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
ms.openlocfilehash: 145bc45e1b7faeddc23cf5f0662337e15ab51c29
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79245693"
---
# <a name="create-or-update-custom-roles-for-azure-resources-using-the-rest-api"></a>Egyéni szerepkörök létrehozása vagy frissítése az Azure-erőforrásokhoz a REST API használatával

Ha az [Azure-erőforrások beépített szerepkörei](built-in-roles.md) nem felelnek meg a szervezet konkrét igényeinek, létrehozhat saját egyéni szerepköröket is. Ez a cikk az egyéni szerepkörök listázását, létrehozását, frissítését és törlését ismerteti a REST API használatával.

## <a name="list-custom-roles"></a>Egyéni szerepkörök listázása

A címtárban lévő összes egyéni szerepkör listázásához használja a [szerepkör-definíciók – lista](/rest/api/authorization/roledefinitions/list) REST API.

1. Kezdje a következő kéréssel:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Cserélje le a *{Filter}* értéket a szerepkör típusára.

    | Szűrés | Leírás |
    | --- | --- |
    | `$filter=type%20eq%20'CustomRole'` | Szűrés a CustomRole típusa alapján |

## <a name="list-custom-roles-at-a-scope"></a>Hatókörhöz tartozó egyéni szerepkörök listázása

Egy hatókörben lévő egyéni szerepkörök listázásához használja a [szerepkör-definíciók – lista](/rest/api/authorization/roledefinitions/list) REST API.

1. Kezdje a következő kéréssel:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Az URI-n belül cserélje le a *{scope}* értéket arra a hatókörre, amelyre a szerepköröket szeretné listázni.

    | Hatókör | Típus |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Előfizetést |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Erőforrás |

1. Cserélje le a *{Filter}* értéket a szerepkör típusára.

    | Szűrés | Leírás |
    | --- | --- |
    | `$filter=type%20eq%20'CustomRole'` | Szűrés a CustomRole típusa alapján |

## <a name="list-a-custom-role-definition-by-name"></a>Egyéni szerepkör-definíciók listázása név alapján

Ha a megjelenített nevével szeretne információt kapni egy egyéni szerepkörről, használja a [szerepkör-definíciókat – get](/rest/api/authorization/roledefinitions/get) REST API.

1. Kezdje a következő kéréssel:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Az URI-n belül cserélje le a *{scope}* értéket arra a hatókörre, amelyre a szerepköröket szeretné listázni.

    | Hatókör | Típus |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Előfizetést |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Erőforrás |

1. Cserélje le a *{Filter}* helyére a szerepkör megjelenített nevét.

    | Szűrés | Leírás |
    | --- | --- |
    | `$filter=roleName%20eq%20'{roleDisplayName}'` | Használja a szerepkör pontos megjelenítendő neve URL-kódolású formáját. Például `$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` |

## <a name="list-a-custom-role-definition-by-id"></a>Egyéni szerepkör-definíció listázása azonosító alapján

Ha egyedi azonosítóval szeretne adatokat lekérni egy egyéni szerepkörről, használja a [szerepkör-definíciókat – get](/rest/api/authorization/roledefinitions/get) REST API.

1. Használja a szerepkör [-definíciók – lista](/rest/api/authorization/roledefinitions/list) REST API a szerepkör GUID azonosítójának lekéréséhez.

1. Kezdje a következő kéréssel:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Az URI-n belül cserélje le a *{scope}* értéket arra a hatókörre, amelyre a szerepköröket szeretné listázni.

    | Hatókör | Típus |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Előfizetést |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Erőforrás |

1. Cserélje le a *{roleDefinitionId}* helyére a szerepkör-definíció GUID azonosítóját.

## <a name="create-a-custom-role"></a>Egyéni szerepkör létrehozása

Egyéni szerepkör létrehozásához használja a [szerepkör-definíciókat – hozzon létre vagy frissítsen](/rest/api/authorization/roledefinitions/createorupdate) REST API. Az API meghívásához olyan felhasználóval kell bejelentkeznie, akinek van olyan szerepköre, amely az összes `assignableScopes``Microsoft.Authorization/roleDefinitions/write` engedéllyel rendelkezik. A beépített szerepkörök közül csak a [tulajdonosi](built-in-roles.md#owner) és a [felhasználói hozzáférés-kezelő rendszergazda](built-in-roles.md#user-access-administrator) adja meg ezt az engedélyt.

1. Tekintse át azon [erőforrás-szolgáltatói műveletek](resource-provider-operations.md) listáját, amelyek elérhetők az egyéni szerepkör engedélyeinek létrehozásához.

1. Egy GUID-eszköz használatával állítson be egy egyedi azonosítót, amelyet a rendszer az egyéni szerepkör-azonosítóhoz fog használni. Az azonosító formátuma: `00000000-0000-0000-0000-000000000000`

1. Kezdje a következő kéréssel és szövegtörzstel:

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

1. Az URI-n belül cserélje le a *{scope}* értéket az egyéni szerepkör első `assignableScopes`ére.

    | Hatókör | Típus |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Előfizetést |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Erőforrás |

1. Cserélje le a *{roleDefinitionId}* helyére az egyéni szerepkör GUID azonosítóját.

1. A kérelem törzsében a `assignableScopes` tulajdonságban cserélje le a *{roleDefinitionId}* azonosítót a GUID azonosítóra.

1. Cserélje le a *{subscriptionId}* helyére az előfizetés-azonosítóját.

1. A `actions` tulajdonságban adja hozzá azokat a műveleteket, amelyeket a szerepkör engedélyez.

1. A `notActions` tulajdonságban adja hozzá azokat a műveleteket, amelyek ki vannak zárva az engedélyezett `actions`ból.

1. A `roleName` és `description` tulajdonságok területen adjon meg egy egyedi szerepkör-nevet és egy leírást. A tulajdonságokkal kapcsolatos további információkért lásd: [Egyéni szerepkörök](custom-roles.md).

    A következő példa egy kérelem törzsét mutatja be:

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

Egyéni szerepkör frissítéséhez használja a [szerepkör-definíciók – létrehozás vagy frissítés](/rest/api/authorization/roledefinitions/createorupdate) REST API. Az API meghívásához olyan felhasználóval kell bejelentkeznie, akinek van olyan szerepköre, amely az összes `assignableScopes``Microsoft.Authorization/roleDefinitions/write` engedéllyel rendelkezik. A beépített szerepkörök közül csak a [tulajdonosi](built-in-roles.md#owner) és a [felhasználói hozzáférés-kezelő rendszergazda](built-in-roles.md#user-access-administrator) adja meg ezt az engedélyt.

1. Használja a [szerepkör-definíciók – lista](/rest/api/authorization/roledefinitions/list) vagy a [szerepkör-definíciók – Get](/rest/api/authorization/roledefinitions/get) REST API az egyéni szerepkörre vonatkozó információk lekéréséhez. További információ: az [Egyéni szerepkörök korábbi listája](#list-custom-roles) szakasz.

1. Kezdje a következő kéréssel:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Az URI-n belül cserélje le a *{scope}* értéket az egyéni szerepkör első `assignableScopes`ére.

    | Hatókör | Típus |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Előfizetést |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Erőforrás |

1. Cserélje le a *{roleDefinitionId}* helyére az egyéni szerepkör GUID azonosítóját.

1. Az egyéni szerepkörre vonatkozó információk alapján hozzon létre egy kérelem törzsét a következő formátumban:

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

1. Frissítse a kérés törzsét az egyéni szerepkörbe felvenni kívánt módosításokkal.

    Az alábbi példa egy olyan kérés törzsét mutatja be, amelyben új diagnosztikai beállításokkal kapcsolatos művelet lett hozzáadva:

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

Egyéni szerepkör törléséhez használja a [szerepkör-definíciókat – törölje](/rest/api/authorization/roledefinitions/delete) REST API. Az API meghívásához olyan felhasználóval kell bejelentkeznie, akinek van olyan szerepköre, amely az összes `assignableScopes``Microsoft.Authorization/roleDefinitions/delete` engedéllyel rendelkezik. A beépített szerepkörök közül csak a [tulajdonosi](built-in-roles.md#owner) és a [felhasználói hozzáférés-kezelő rendszergazda](built-in-roles.md#user-access-administrator) adja meg ezt az engedélyt.

1. Használja a [szerepkör-definíciók – lista](/rest/api/authorization/roledefinitions/list) vagy a [szerepkör-definíciók – Get](/rest/api/authorization/roledefinitions/get) REST API az egyéni szerepkör GUID azonosítójának beszerzéséhez. További információ: az [Egyéni szerepkörök korábbi listája](#list-custom-roles) szakasz.

1. Kezdje a következő kéréssel:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Az URI-n belül cserélje le a *{scope}* értéket arra a hatókörre, amelynek az egyéni szerepkörét törölni szeretné.

    | Hatókör | Típus |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Előfizetést |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Erőforrás |

1. Cserélje le a *{roleDefinitionId}* helyére az egyéni szerepkör GUID azonosítóját.

## <a name="next-steps"></a>Következő lépések

- [Egyéni szerepkörök Azure-erőforrásokhoz](custom-roles.md)
- [Az Azure-erőforrásokhoz való hozzáférés kezelése a RBAC és a REST API használatával](role-assignments-rest.md)
- [Azure REST API-referencia](/rest/api/azure/)
