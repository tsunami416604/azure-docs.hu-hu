---
title: Azure-beli szerepkör-hozzárendelések hozzáadása vagy eltávolítása a REST API-Azure RBAC használatával
description: Ismerje meg, hogyan biztosíthat hozzáférést az Azure-erőforrásokhoz felhasználók, csoportok, egyszerű szolgáltatások vagy felügyelt identitások számára a REST API és az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával.
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
ms.topic: how-to
ms.date: 05/06/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: d66b4c8e9f41f661cfc399f72a9ad97405a860fc
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/15/2020
ms.locfileid: "84790846"
---
# <a name="add-or-remove-azure-role-assignments-using-the-rest-api"></a>Azure-beli szerepkör-hozzárendelések hozzáadása vagy eltávolítása a REST API használatával

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]Ez a cikk azt ismerteti, hogyan rendelhet hozzá szerepköröket a REST API használatával.

## <a name="prerequisites"></a>Előfeltételek

Szerepkör-hozzárendelések hozzáadásához vagy eltávolításához a következőket kell tennie:

- `Microsoft.Authorization/roleAssignments/write`és `Microsoft.Authorization/roleAssignments/delete` engedélyek, például a [felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator) vagy a [tulajdonos](built-in-roles.md#owner)

## <a name="add-a-role-assignment"></a>Szerepkör-hozzárendelés hozzáadása

Az Azure RBAC a hozzáférés biztosításához hozzá kell adnia egy szerepkör-hozzárendelést. Szerepkör-hozzárendelés hozzáadásához használja a [szerepkör-hozzárendeléseket – hozzon létre](/rest/api/authorization/roleassignments/create) REST API, és adja meg a rendszerbiztonsági tag, a szerepkör-definíció és a hatókört. Az API meghívásához hozzáféréssel kell rendelkeznie a `Microsoft.Authorization/roleAssignments/write` művelethez. A beépített szerepkörök közül csak a [tulajdonosi](built-in-roles.md#owner) és a [felhasználói hozzáférés-adminisztrátor](built-in-roles.md#user-access-administrator) kap hozzáférést ehhez a művelethez.

1. Használja a [szerepkör-definíciók – lista](/rest/api/authorization/roledefinitions/list) REST API, vagy tekintse meg a [beépített szerepköröket](built-in-roles.md) a hozzárendelni kívánt szerepkör-definíció azonosítójának lekéréséhez.

1. Egy GUID-eszköz használatával állítson be egy egyedi azonosítót, amelyet a rendszer a szerepkör-hozzárendelési azonosítóhoz fog használni. Az azonosító formátuma:`00000000-0000-0000-0000-000000000000`

1. Kezdje a következő kéréssel és szövegtörzstel:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2015-07-01
    ```

    ```json
    {
      "properties": {
        "roleDefinitionId": "/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
        "principalId": "{principalId}"
      }
    }
    ```

1. Az URI-n belül cserélje le a *{scope}* értéket a szerepkör-hozzárendelés hatókörére.

    > [!div class="mx-tableFixed"]
    > | Hatókör | Típus |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Felügyeleti csoport |
    > | `subscriptions/{subscriptionId1}` | Előfizetés |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Erőforrás |

    Az előző példában a Microsoft. web egy olyan erőforrás-szolgáltató, amely egy App Service példányra hivatkozik. Hasonlóképpen használhatja bármely más erőforrás-szolgáltatót, és megadhatja a hatókört. További információ: [Azure erőforrás-szolgáltatók és típusok](../azure-resource-manager/management/resource-providers-and-types.md) és támogatott [Azure Resource Manager erőforrás-szolgáltatói műveletek](resource-provider-operations.md).  

1. Cserélje le a *{roleAssignmentId}* helyére a szerepkör-hozzárendelés GUID azonosítóját.

1. A kérelem törzsében cserélje le a *{scope}* helyére a szerepkör-hozzárendelés hatókörét.

    > [!div class="mx-tableFixed"]
    > | Hatókör | Típus |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Felügyeleti csoport |
    > | `subscriptions/{subscriptionId1}` | Előfizetés |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Erőforrás |

1. Cserélje le a *{roleDefinitionId}* helyére a szerepkör-definíció azonosítóját.

1. Cserélje le a *{principalId}* helyére annak a felhasználónak, csoportnak vagy egyszerű szolgáltatásnak az azonosítóját, amely hozzá lesz rendelve a szerepkörhöz.

A következő kérelem és törzs rendeli hozzá a [biztonságimásolat-olvasó](built-in-roles.md#backup-reader) szerepkört egy felhasználóhoz az előfizetés hatókörében:

```http
PUT https://management.azure.com/subscriptions/{subscriptionId1}/providers/microsoft.authorization/roleassignments/{roleAssignmentId1}?api-version=2015-07-01
```

```json
{
  "properties": {
    "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
    "principalId": "{objectId1}"
  }
}
```

Az alábbi ábrán egy példa látható a kimenetre:

```json
{
    "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
        "principalId": "{objectId1}",
        "scope": "/subscriptions/{subscriptionId1}",
        "createdOn": "2020-05-06T23:55:23.7679147Z",
        "updatedOn": "2020-05-06T23:55:23.7679147Z",
        "createdBy": null,
        "updatedBy": "{updatedByObjectId1}"
    },
    "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId1}",
    "type": "Microsoft.Authorization/roleAssignments",
    "name": "{roleAssignmentId1}"
}
```

## <a name="remove-a-role-assignment"></a>Szerepkör-hozzárendelés eltávolítása

Az Azure RBAC a hozzáférés eltávolításához el kell távolítania egy szerepkör-hozzárendelést. Szerepkör-hozzárendelés eltávolításához használja a [szerepkör-hozzárendeléseket – törölje](/rest/api/authorization/roleassignments/delete) REST API. Az API meghívásához hozzáféréssel kell rendelkeznie a `Microsoft.Authorization/roleAssignments/delete` művelethez. A beépített szerepkörök közül csak a [tulajdonosi](built-in-roles.md#owner) és a [felhasználói hozzáférés-adminisztrátor](built-in-roles.md#user-access-administrator) kap hozzáférést ehhez a művelethez.

1. A szerepkör-hozzárendelési azonosító (GUID) beolvasása. Ezt az azonosítót a rendszer a szerepkör-hozzárendelés első létrehozásakor adja vissza, vagy pedig a szerepkör-hozzárendelések listázásával.

1. Kezdje a következő kéréssel:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2015-07-01
    ```

1. Az URI-n belül cserélje le a *{scope}* elemet a szerepkör-hozzárendelés eltávolítására szolgáló hatókörre.

    > [!div class="mx-tableFixed"]
    > | Hatókör | Típus |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Felügyeleti csoport |
    > | `subscriptions/{subscriptionId1}` | Előfizetés |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Erőforrás |

1. Cserélje le a *{roleAssignmentId}* helyére a szerepkör-hozzárendelés GUID azonosítóját.

A következő kérelem eltávolítja a megadott szerepkör-hozzárendelést az előfizetés hatókörében:

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId1}/providers/microsoft.authorization/roleassignments/{roleAssignmentId1}?api-version=2015-07-01
```

Az alábbi ábrán egy példa látható a kimenetre:

```json
{
    "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
        "principalId": "{objectId1}",
        "scope": "/subscriptions/{subscriptionId1}",
        "createdOn": "2020-05-06T23:55:24.5379478Z",
        "updatedOn": "2020-05-06T23:55:24.5379478Z",
        "createdBy": "{createdByObjectId1}",
        "updatedBy": "{updatedByObjectId1}"
    },
    "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId1}",
    "type": "Microsoft.Authorization/roleAssignments",
    "name": "{roleAssignmentId1}"
}
```

## <a name="next-steps"></a>További lépések

- [Azure-beli szerepkör-hozzárendelések listázása a REST API használatával](role-assignments-list-rest.md)
- [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure Manager REST API-val](../azure-resource-manager/templates/deploy-rest.md)
- [Azure REST API-referenciák](/rest/api/azure/)
- [Egyéni Azure-szerepkörök létrehozása vagy frissítése a REST API használatával](custom-roles-rest.md)
