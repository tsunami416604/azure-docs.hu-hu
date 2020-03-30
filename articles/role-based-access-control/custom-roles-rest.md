---
title: Egyéni szerepkörök létrehozása vagy frissítése az Azure-erőforrásokhoz a REST API-val
description: Megtudhatja, hogyan listázhatja, hozhat létre, frissítheti vagy törölheti az egyéni szerepköröket szerepköralapú hozzáférés-vezérléssel (RBAC) az Azure-erőforrásokhoz a REST API használatával.
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
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: fda0400310f46da64322654c42af75521746d679
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062192"
---
# <a name="create-or-update-custom-roles-for-azure-resources-using-the-rest-api"></a>Egyéni szerepkörök létrehozása vagy frissítése az Azure-erőforrásokhoz a REST API használatával

> [!IMPORTANT]
> Felügyeleti csoport hozzáadása `AssignableScopes` jelenleg előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

Ha az [Azure-erőforrások beépített szerepkörei](built-in-roles.md) nem felelnek meg a szervezet egyedi igényeinek, létrehozhat saját egyéni szerepköröket. Ez a cikk bemutatja, hogyan listázheti, hozhat létre, frissítheti és törölheti az egyéni szerepköröket a REST API használatával.

## <a name="list-custom-roles"></a>Egyéni szerepkörök listázása

A címtárban lévő összes egyéni szerepkör listázásához használja a [Szerepkör-definíciók – REST-lista](/rest/api/authorization/roledefinitions/list) API-t.

1. Kezdje a következő kéréssel:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Cserélje le *a(z) {filter}* programot a szerepkörtípusra.

    > [!div class="mx-tableFixed"]
    > | Szűrés | Leírás |
    > | --- | --- |
    > | `$filter=type+eq+'CustomRole'` | Szűrő a CustomRole típus alapján |

## <a name="list-custom-roles-at-a-scope"></a>Egyéni szerepkörök listázása hatókörben

Egyéni szerepkörök listázásához használja a [szerepkör-definíciók – REST-lista API-t.](/rest/api/authorization/roledefinitions/list)

1. Kezdje a következő kéréssel:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Az URI-n belül cserélje le *a(z) {scope}* értéket arra a hatókörre, amelynek a szerepköreit fel szeretné sorolni.

    > [!div class="mx-tableFixed"]
    > | Hatókör | Típus |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Előfizetés |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Erőforráscsoport |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | Erőforrás |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Felügyeleti csoport |

1. Cserélje le *a(z) {filter}* programot a szerepkörtípusra.

    > [!div class="mx-tableFixed"]
    > | Szűrés | Leírás |
    > | --- | --- |
    > | `$filter=type+eq+'CustomRole'` | Szűrő a CustomRole típus alapján |

## <a name="list-a-custom-role-definition-by-name"></a>Egyéni szerepkör-definíció felsorolása név szerint

Ha egy egyéni szerepkörről a megjelenítendő neve alapján szeretne információt kapni, használja a [Szerepkör-definíciók – REST API lehívása.](/rest/api/authorization/roledefinitions/get)

1. Kezdje a következő kéréssel:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Az URI-n belül cserélje le *a(z) {scope}* értéket arra a hatókörre, amelynek a szerepköreit fel szeretné sorolni.

    > [!div class="mx-tableFixed"]
    > | Hatókör | Típus |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Előfizetés |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Erőforráscsoport |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | Erőforrás |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Felügyeleti csoport |

1. Cserélje le *a(z) {filter}* programot a szerepkör megjelenítendő nevére.

    > [!div class="mx-tableFixed"]
    > | Szűrés | Leírás |
    > | --- | --- |
    > | `$filter=roleName+eq+'{roleDisplayName}'` | Használja a szerepkör pontos megjelenítendő nevének URL-kódolású formáját. Például,`$filter=roleName+eq+'Virtual%20Machine%20Contributor'` |

## <a name="list-a-custom-role-definition-by-id"></a>Egyéni szerepkör-definíció listázása azonosító szerint

Ha információt szeretne kapni egy egyéni szerepkörről az egyedi azonosítója alapján, használja a [Szerepkör-definíciók – REST API lehívása.](/rest/api/authorization/roledefinitions/get)

1. Használja a [szerepkör-definíciók – LIST](/rest/api/authorization/roledefinitions/list) REST API-t a szerepkör GUID-azonosítójának lekérni.

1. Kezdje a következő kéréssel:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Az URI-n belül cserélje le *a(z) {scope}* értéket arra a hatókörre, amelynek a szerepköreit fel szeretné sorolni.

    > [!div class="mx-tableFixed"]
    > | Hatókör | Típus |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Előfizetés |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Erőforráscsoport |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | Erőforrás |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Felügyeleti csoport |

1. Cserélje le *a(z) {roleDefinitionId}* azonosítóját a szerepkör-definíció GUID azonosítójára.

## <a name="create-a-custom-role"></a>Egyéni szerepkör létrehozása

Egyéni szerepkör létrehozásához használja a [szerepkör-definíciók – REST](/rest/api/authorization/roledefinitions/createorupdate) API létrehozása vagy frissítése. Az API hívásához be kell jelentkeznie egy olyan felhasználóval, `Microsoft.Authorization/roleDefinitions/write` aki hez `assignableScopes`olyan szerepkör van rendelve, amely rendelkezik az összes engedélyével. A beépített szerepkörök közül csak a [tulajdonos](built-in-roles.md#owner) és [a felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator) tartalmazza ezt az engedélyt.

1. Tekintse át az egyéni szerepkör engedélyeinek létrehozásához rendelkezésre álló [erőforrás-szolgáltatói műveletek](resource-provider-operations.md) listáját.

1. GuiD eszközzel hozzon létre egy egyedi azonosítót, amelyet az egyéni szerepkör-azonosítóhoz fog használni. Az azonosító formátuma:`00000000-0000-0000-0000-000000000000`

1. Kezdje a következő kéréssel és törzsgel:

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
          "/subscriptions/{subscriptionId1}",
          "/subscriptions/{subscriptionId2}",
          "/subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}",
          "/subscriptions/{subscriptionId2}/resourceGroups/{resourceGroup2}",
          "/providers/Microsoft.Management/managementGroups/{groupId1}"
        ]
      }
    }
    ```

1. Az URI-n belül cserélje le `assignableScopes` *a(z) {scope}* értéket az egyéni szerepkör közül az elsőre.

    > [!div class="mx-tableFixed"]
    > | Hatókör | Típus |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Előfizetés |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Erőforráscsoport |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Felügyeleti csoport |

1. Cserélje le *a(z) {roleDefinitionId}* azonosítóját az egyéni szerepkör GUID azonosítójára.

1. A kérelemtörzsön belül cserélje le *a(z) {roleDefinitionId}* azonosítót a GUID azonosítóra.

1. Ha `assignableScopes` előfizetés vagy erőforráscsoport, cserélje le a *{subscriptionId}* vagy *{resourceGroup}* példányokat az azonosítókra.

1. Ha `assignableScopes` felügyeleti csoport, cserélje le *a(z) {groupId} példányt* a felügyeleticsoport azonosítójára. Felügyeleti csoport hozzáadása `assignableScopes` jelenleg előzetes verzióban érhető el.

1. A `actions` tulajdonságban adja hozzá azokat a műveleteket, amelyeket a szerepkör lehetővé tesz.

1. A `notActions` tulajdonságban adja hozzá azokat a műveleteket, amelyek ki vannak zárva az engedélyezett `actions`ből.

1. A `roleName` és `description` a tulajdonságok között adjon meg egyedi szerepkörnevet és leírást. A tulajdonságokról további információt az [Egyéni szerepkörök című témakörben talál.](custom-roles.md)

    A következő egy példa egy kérelem törzs:

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
          "/subscriptions/00000000-0000-0000-0000-000000000000",
          "/providers/Microsoft.Management/managementGroups/marketing-group"
        ]
      }
    }
    ```

## <a name="update-a-custom-role"></a>Egyéni szerepkörök frissítése

Egyéni szerepkör frissítéséhez használja a [szerepkör-definíciók – REST](/rest/api/authorization/roledefinitions/createorupdate) API létrehozása vagy frissítése. Az API hívásához be kell jelentkeznie egy olyan felhasználóval, `Microsoft.Authorization/roleDefinitions/write` aki hez `assignableScopes`olyan szerepkör van rendelve, amely rendelkezik az összes engedélyével. A beépített szerepkörök közül csak a [tulajdonos](built-in-roles.md#owner) és [a felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator) tartalmazza ezt az engedélyt.

1. Használja a [szerepkör-definíciók - lista](/rest/api/authorization/roledefinitions/list) vagy [szerepkör-definíciók - REST API-t](/rest/api/authorization/roledefinitions/get) az egyéni szerepkör höz. További információt a Korábbi [Egyéni szerepkörök listázása](#list-custom-roles) című szakaszban talál.

1. Kezdje a következő kéréssel:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Az URI-n belül cserélje le `assignableScopes` *a(z) {scope}* értéket az egyéni szerepkör közül az elsőre.

    > [!div class="mx-tableFixed"]
    > | Hatókör | Típus |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Előfizetés |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Erőforráscsoport |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Felügyeleti csoport |

1. Cserélje le *a(z) {roleDefinitionId}* azonosítóját az egyéni szerepkör GUID azonosítójára.

1. Az egyéni szerepkörre vonatkozó információk alapján hozzon létre egy kéréstörzset a következő formátummal:

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
          "/subscriptions/{subscriptionId1}",
          "/subscriptions/{subscriptionId2}",
          "/subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}",
          "/subscriptions/{subscriptionId2}/resourceGroups/{resourceGroup2}",
          "/providers/Microsoft.Management/managementGroups/{groupId1}"
        ]
      }
    }
    ```

1. Frissítse a kérelem törzsét az egyéni szerepkörben végrehajtani kívánt módosításokkal.

    Az alábbi példa egy új diagnosztikai beállítási művelettel rendelkező kéréstörzsre mutat be:

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
          "/subscriptions/00000000-0000-0000-0000-000000000000",
          "/providers/Microsoft.Management/managementGroups/marketing-group"
        ]
      }
    }
    ```

## <a name="delete-a-custom-role"></a>Egyéni szerepkörök törlése

Egyéni szerepkör törléséhez használja a [szerepkör-definíciók – REST-API törlése.](/rest/api/authorization/roledefinitions/delete) Az API hívásához be kell jelentkeznie egy olyan felhasználóval, `Microsoft.Authorization/roleDefinitions/delete` aki hez `assignableScopes`olyan szerepkör van rendelve, amely rendelkezik az összes engedélyével. A beépített szerepkörök közül csak a [tulajdonos](built-in-roles.md#owner) és [a felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator) tartalmazza ezt az engedélyt.

1. Használja a [szerepkör-definíciók - lista](/rest/api/authorization/roledefinitions/list) vagy [szerepkör-definíciók - REST](/rest/api/authorization/roledefinitions/get) API lehívása az egyéni szerepkör GUID azonosítójának lehívásához. További információt a Korábbi [Egyéni szerepkörök listázása](#list-custom-roles) című szakaszban talál.

1. Kezdje a következő kéréssel:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Az URI-n belül cserélje le *a(z) {scope}* értéket az egyéni szerepkör törölni kívánt hatókörre.

    > [!div class="mx-tableFixed"]
    > | Hatókör | Típus |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Előfizetés |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Erőforráscsoport |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Felügyeleti csoport |

1. Cserélje le *a(z) {roleDefinitionId}* azonosítóját az egyéni szerepkör GUID azonosítójára.

## <a name="next-steps"></a>További lépések

- [Egyéni szerepkörök Azure-erőforrásokhoz](custom-roles.md)
- [Azure-erőforrásokhoz való hozzáférés kezelése az RBAC és a REST API használatával](role-assignments-rest.md)
- [Azure REST API-útmutató](/rest/api/azure/)
