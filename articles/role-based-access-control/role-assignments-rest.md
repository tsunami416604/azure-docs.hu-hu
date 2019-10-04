---
title: Az Azure-erőforrásokhoz való hozzáférés kezelése a RBAC és a REST API-Azure használatával | Microsoft Docs
description: Ismerje meg, hogyan kezelheti az Azure-erőforrásokhoz való hozzáférést a felhasználók, csoportok és alkalmazások számára szerepköralapú hozzáférés-vezérlés (RBAC) és a REST API használatával. Ez tartalmazza a hozzáférés felsorolásának, a hozzáférés adásának és a hozzáférés eltávolításának módját.
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
ms.date: 09/11/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 86ee030e8c97cf3033b9d2d76b8125c64ecf8065
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996475"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-the-rest-api"></a>Az Azure-erőforrásokhoz való hozzáférés kezelése a RBAC és a REST API használatával

A [szerepköralapú hozzáférés-vezérlés (RBAC)](overview.md) az Azure-erőforrásokhoz való hozzáférés kezelésének módja. Ez a cikk azt ismerteti, hogyan kezelheti a felhasználók, csoportok és alkalmazások hozzáférését a RBAC és a REST API használatával.

## <a name="list-access"></a>Hozzáférések felsorolása

A RBAC-ben a hozzáférés listázásához listázhatja a szerepkör-hozzárendeléseket. A szerepkör-hozzárendelések listázásához használja a [szerepkör-hozzárendelések-lista](/rest/api/authorization/roleassignments/list) REST API-k egyikét. Az eredmények pontosításához meg kell adnia egy hatókört és egy opcionális szűrőt.

1. Kezdje a következő kéréssel:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. Az URI-n belül cserélje le a *{scope}* elemet arra a hatókörre, amelyre a szerepkör-hozzárendeléseket szeretné listázni.

    | Scope | Type |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Felügyeleti csoport |
    | `subscriptions/{subscriptionId1}` | Előfizetés |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

    Az előző példában a Microsoft. web egy olyan erőforrás-szolgáltató, amely egy App Service példányra hivatkozik. Hasonlóképpen használhatja bármely más erőforrás-szolgáltatót, és megadhatja a hatókört. További információ: [Azure erőforrás-szolgáltatók és típusok](../azure-resource-manager/resource-manager-supported-services.md) és támogatott [Azure Resource Manager erőforrás-szolgáltatói műveletek](resource-provider-operations.md).  
     
1. Cserélje le a *{Filter}* helyére azt a feltételt, amelyet alkalmazni szeretne a szerepkör-hozzárendelési lista szűréséhez.

    | Szűrés | Leírás |
    | --- | --- |
    | `$filter=atScope()` | Csak a megadott hatókörhöz tartozó szerepkör-hozzárendelések felsorolása, nem tartalmazza a szerepkör-hozzárendeléseket az alhatókörben. |
    | `$filter=principalId%20eq%20'{objectId}'` | Egy adott felhasználó, csoport vagy egyszerű szolgáltatásnév szerepkör-hozzárendeléseinek felsorolása. |
    | `$filter=assignedTo('{objectId}')` | Egy adott felhasználó vagy szolgáltatásnév szerepkör-hozzárendeléseinek felsorolása. Ha a felhasználó egy szerepkör-hozzárendeléssel rendelkező csoport tagja, akkor a szerepkör-hozzárendelés is megjelenik. Ez a szűrő a csoportok esetében tranzitív, ami azt jelenti, hogy ha a felhasználó egy csoport tagja, és a csoport egy másik, szerepkör-hozzárendelést tartalmazó csoport tagja, akkor a szerepkör-hozzárendelés is megjelenik. Ez a szűrő csak egy felhasználóhoz vagy egy egyszerű szolgáltatáshoz tartozó objektumazonosítót fogad el. Nem lehet átadni egy objektum AZONOSÍTÓját egy csoport számára. |

## <a name="grant-access"></a>Hozzáférés biztosítása

Az RBAC-ben a hozzáférés biztosítása egy szerepkör-hozzárendelés létrehozásával történik. Szerepkör-hozzárendelés létrehozásához használja a [szerepkör-hozzárendeléseket – hozzon létre](/rest/api/authorization/roleassignments/create) REST API, és határozza meg a rendszerbiztonsági tag, a szerepkör-definíció és a hatókört. Az API meghívásához hozzáféréssel kell rendelkeznie a `Microsoft.Authorization/roleAssignments/write` művelethez. A beépített szerepkörök közül csak a tulajdonosi [](built-in-roles.md#owner) és a [felhasználói hozzáférés-adminisztrátor](built-in-roles.md#user-access-administrator) kap hozzáférést ehhez a művelethez.

1. Használja a [szerepkör-definíciók – lista](/rest/api/authorization/roledefinitions/list) REST API, vagy tekintse meg a [beépített szerepköröket](built-in-roles.md) a hozzárendelni kívánt szerepkör-definíció azonosítójának lekéréséhez.

1. Egy GUID-eszköz használatával állítson be egy egyedi azonosítót, amelyet a rendszer a szerepkör-hozzárendelési azonosítóhoz fog használni. Az azonosító formátuma:`00000000-0000-0000-0000-000000000000`

1. Kezdje a következő kéréssel és szövegtörzstel:

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

1. Az URI-n belül cserélje le a *{scope}* értéket a szerepkör-hozzárendelés hatókörére.

    | Scope | Type |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Felügyeleti csoport |
    | `subscriptions/{subscriptionId1}` | Előfizetés |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Resource |

1. Cserélje le a *{roleAssignmentName}* helyére a szerepkör-hozzárendelés GUID azonosítóját.

1. A kérelem törzsében cserélje le a *{scope}* helyére a szerepkör-hozzárendelés hatókörét.

    | Scope | Type |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Felügyeleti csoport |
    | `subscriptions/{subscriptionId1}` | Előfizetés |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Resource |

1. Cserélje le a *{roleDefinitionId}* helyére a szerepkör-definíció azonosítóját.

1. Cserélje le a *{principalId}* helyére annak a felhasználónak, csoportnak vagy egyszerű szolgáltatásnak az azonosítóját, amely hozzá lesz rendelve a szerepkörhöz.

## <a name="remove-access"></a>Hozzáférés megszüntetése

Az RBAC-ben hozzáférés eltávolításához egy szerepkör-hozzárendelést kell eltávolítania. Szerepkör-hozzárendelés eltávolításához használja a [szerepkör-hozzárendeléseket – törölje](/rest/api/authorization/roleassignments/delete) REST API. Az API meghívásához hozzáféréssel kell rendelkeznie a `Microsoft.Authorization/roleAssignments/delete` művelethez. A beépített szerepkörök közül csak a tulajdonosi [](built-in-roles.md#owner) és a [felhasználói hozzáférés-adminisztrátor](built-in-roles.md#user-access-administrator) kap hozzáférést ehhez a művelethez.

1. A szerepkör-hozzárendelési azonosító (GUID) beolvasása. Ezt az azonosítót a rendszer a szerepkör-hozzárendelés első létrehozásakor adja vissza, vagy pedig a szerepkör-hozzárendelések listázásával.

1. Kezdje a következő kéréssel:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

1. Az URI-n belül cserélje le a *{scope}* elemet a szerepkör-hozzárendelés eltávolítására szolgáló hatókörre.

    | Scope | Type |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Felügyeleti csoport |
    | `subscriptions/{subscriptionId1}` | Előfizetés |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Resource |

1. Cserélje le a *{roleAssignmentName}* helyére a szerepkör-hozzárendelés GUID azonosítóját.

## <a name="next-steps"></a>További lépések

- [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure Manager REST API-val](../azure-resource-manager/resource-group-template-deploy-rest.md)
- [Azure REST API-referencia](/rest/api/azure/)
- [Egyéni szerepkörök létrehozása az Azure-erőforrásokhoz a REST API használatával](custom-roles-rest.md)
