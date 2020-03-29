---
title: Szerepkör-hozzárendelések hozzáadása vagy eltávolítása az RBAC-mal és a REST API-val
description: Ismerje meg, hogyan adhat hozzáférést az Azure-erőforrásokhoz felhasználók, csoportok, egyszerű szolgáltatástagok vagy felügyelt identitások számára az Azure szerepköralapú hozzáférés-vezérlés (RBAC) és a REST API használatával.
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
ms.openlocfilehash: 9beda6589c03f1b14fc9756af86a9ce0711894c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063010"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-the-rest-api"></a>Szerepkör-hozzárendelések hozzáadása vagy eltávolítása az Azure RBAC és a REST API használatával

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]Ez a cikk ismerteti, hogyan rendelhet szerepköröket a REST API használatával.

## <a name="prerequisites"></a>Előfeltételek

Szerepkör-hozzárendelések hozzáadásához vagy eltávolításához a következőkre van szükség:

- `Microsoft.Authorization/roleAssignments/write`és `Microsoft.Authorization/roleAssignments/delete` engedélyek, például [a Felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator) vagy [a tulajdonos](built-in-roles.md#owner)

## <a name="add-a-role-assignment"></a>Szerepkör-hozzárendelés hozzáadása

Az RBAC-ban a hozzáférés engedélyezéséhez egy szerepkör-hozzárendelést kell hozzáadnia. Szerepkör-hozzárendelés hozzáadásához használja a [Szerepkör-hozzárendelések – REST](/rest/api/authorization/roleassignments/create) LÉTREHOZÁSA API-t, és adja meg a rendszerbiztonsági tag, a szerepkör-definíció és a hatókör. Az API hívásához hozzáféréssel kell `Microsoft.Authorization/roleAssignments/write` rendelkeznie a művelethez. A beépített szerepkörök közül csak [a tulajdonos](built-in-roles.md#owner) és a felhasználói [hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator) kap hozzáférést ehhez a művelethez.

1. Használja a [szerepkör-definíciók – LIST](/rest/api/authorization/roledefinitions/list) REST API-t, vagy tekintse [meg a beépített szerepkörök](built-in-roles.md) a hozzárendelni kívánt szerepkör-definíció azonosítójának lekérni.

1. GuiD eszközzel hozzon létre egy egyedi azonosítót, amelyet a szerepkör-hozzárendelési azonosítóhoz fog használni. Az azonosító formátuma:`00000000-0000-0000-0000-000000000000`

1. Kezdje a következő kéréssel és törzsgel:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

    ```json
    {
      "properties": {
        "roleDefinitionId": "/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
        "principalId": "{principalId}"
      }
    }
    ```

1. Az URI-n belül cserélje le *a(z) {scope}* értéket a szerepkör-hozzárendelés hatókörére.

    > [!div class="mx-tableFixed"]
    > | Hatókör | Típus |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Felügyeleti csoport |
    > | `subscriptions/{subscriptionId1}` | Előfizetés |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Erőforrás |

    Az előző példában a microsoft.web egy olyan erőforrás-szolgáltató, amely egy App Service-példányra hivatkozik. Hasonlóképpen bármely más erőforrás-szolgáltatót is használhat, és megadhatja a hatókört. További információ: [Azure Resource providers and types](../azure-resource-manager/management/resource-providers-and-types.md) and supported Azure Resource Manager resource provider [operations.](resource-provider-operations.md)  

1. Cserélje le *a(z) {roleAssignmentName}* azonosítóját a szerepkör-hozzárendelésguid azonosítójára.

1. A kérelemtörzsön belül cserélje le *a(z) {scope}* értéket a szerepkör-hozzárendelés hatókörével.

    > [!div class="mx-tableFixed"]
    > | Hatókör | Típus |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Felügyeleti csoport |
    > | `subscriptions/{subscriptionId1}` | Előfizetés |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Erőforrás |

1. Cserélje le *a(z) {roleDefinitionId}* programot a szerepkör-definíciós azonosítóra.

1. Cserélje le *a(z) {principalId}* objektumazonosítóját a szerepkörhöz rendelt felhasználó, csoport vagy egyszerű szolgáltatás azonosítójára.

## <a name="remove-a-role-assignment"></a>Szerepkör-hozzárendelés eltávolítása

Az RBAC-ben hozzáférés eltávolításához egy szerepkör-hozzárendelést kell eltávolítania. Szerepkör-hozzárendelés eltávolításához használja a [szerepkör-hozzárendelések – REST TÖRLÉSE](/rest/api/authorization/roleassignments/delete) API-t. Az API hívásához hozzáféréssel kell `Microsoft.Authorization/roleAssignments/delete` rendelkeznie a művelethez. A beépített szerepkörök közül csak [a tulajdonos](built-in-roles.md#owner) és a felhasználói [hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator) kap hozzáférést ehhez a művelethez.

1. A szerepkör-hozzárendelés azonosítójának (GUID) beszereznie. Ezt az azonosítót a rendszer a szerepkör-hozzárendelés létrehozásakor adja vissza, vagy a szerepkör-hozzárendelések listázásával kaphatja meg.

1. Kezdje a következő kéréssel:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

1. Az URI-n belül cserélje le *a(z) {scope}* elemet a szerepkör-hozzárendelés eltávolításának hatókörével.

    > [!div class="mx-tableFixed"]
    > | Hatókör | Típus |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Felügyeleti csoport |
    > | `subscriptions/{subscriptionId1}` | Előfizetés |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Erőforrás |

1. Cserélje le *a(z) {roleAssignmentName}* azonosítóját a szerepkör-hozzárendelésguid azonosítójára.

## <a name="next-steps"></a>További lépések

- [Szerepkör-hozzárendelések listázása az Azure RBAC és a REST API használatával](role-assignments-list-rest.md)
- [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure Manager REST API-val](../azure-resource-manager/templates/deploy-rest.md)
- [Azure REST API-útmutató](/rest/api/azure/)
- [Egyéni szerepkörök létrehozása az Azure-erőforrásokhoz a REST API használatával](custom-roles-rest.md)
